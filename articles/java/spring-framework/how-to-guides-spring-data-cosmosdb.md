---
title: Entwicklerhandbuch zu Spring Data Azure Cosmos DB
description: In dieser Anleitung wird beschrieben, was Sie bei der Verwendung des Spring Data Azure Cosmos DB SDK beachten sollten.
author: anfeldma-ms
ms.author: anfeldma
ms.topic: conceptual
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 45872eba2fd6929cf406df4a551559fd5ad78c40
ms.sourcegitcommit: 63732132cb88206b99876f0bcd035b52c301f315
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523135"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Entwicklerhandbuch zu Spring Data Azure Cosmos DB

In diesem Thema werden die Funktionen von [Spring Data Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) bei Verwendung der SQL-API beschrieben. Darüber hinaus enthält dieses Thema Anleitungen bei allgemeinen Problemen sowie Problemumgehungen und Diagnoseschritte.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs die Arbeit mit Daten ermöglicht. Das Spring Data Azure Cosmos DB SDK basiert auf dem [Spring Data](https://spring.io/projects/spring-data)-Framework und ermöglicht dank der SQL-API die Integration mit Azure Cosmos DB. Informationen zur Unterstützung anderer APIs finden Sie in den folgenden Themen:

- [Verwenden der Spring Data-MongoDB-API mit Azure Cosmos DB](./configure-spring-data-mongodb-with-cosmos-db.md)
- [Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB](./configure-spring-data-apache-cassandra-with-cosmos-db.md).
- [Verwenden von Spring Data Gremlin Starter mit der SQL-API von Azure Cosmos DB](./configure-spring-data-gremlin-java-app-with-cosmos-db.md)

Das Spring Data Azure Cosmos DB SDK ist als Open Source-Ressource im Repository [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) auf GitHub verfügbar. Dieses Repository enthält eine aktive Liste mit [Problemen](https://github.com/Azure/azure-sdk-for-java/issues), über die Sie Fehler melden oder Problemumgehungen für bereits eingereichte Probleme suchen können. Sie können auch die Liste mit den [Releases](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md) überprüfen, um zu ermitteln, ob ein Problem in einer neueren Version behoben wurde. 

## <a name="available-features"></a>Verfügbare Features

In den folgenden Abschnitten werden die derzeit verfügbar Funktionen beschrieben.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Unterstützung für CrudRepository und ReactiveCrudRepository

Das Spring Data Azure Cosmos DB SDK stellt die Schnittstellen `CosmosRepository` und `ReactiveCosmosRepository` bereit, die die Spring Data-Schnittstellen `CrudRepository` und `ReactiveCrudRepository` erweitern.

Das folgende Beispiel zeigt, wie diese Einstellungen erweitert werden:

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {
    List<SampleEntity> findByName(String name);
}

@Repository
public interface ReactiveSampleRepository extends ReactiveCosmosRepository<SampleEntity, String> {
    Flux<SampleEntity> findByName(String name);
}
```

Abhängig von der Verwendung müssen Sie beide Repositorys separat in der Klasse `Configuration` aktivieren. Beispiel:

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>Definieren einer einfachen Entität

Sie können Entitäten definieren, indem Sie die Anmerkung `@Container` hinzufügen und Eigenschaften im Zusammenhang mit der Sammlung angeben, etwa den Sammlungsnamen, Anforderungseinheiten (Request Units, RUs), Gültigkeitsdauer und das Sammlungsflag für die automatische Erstellung.

Der Sammlungsname ist standardmäßig der Klassenname der Klasse „user-domain“. Fügen Sie zum Anpassen der Domänenklasse die Anmerkung `@Container(containerName="myCustomCollectionName")` hinzu. Das Feld `containerName` unterstützt darüber hinaus [SpEL-Ausdrücke](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (Spring Expression Language, Spring-Ausdruckssprache), sodass Sie Sammlungsnamen programmgesteuert über Konfigurationseigenschaften angeben können. Sie können beispielsweise Ausdrücke wie `containerName = "${dynamic.container.name}"` und `containerName = "#{@someBean.getContainerName()}"` verwenden.

Es gibt zwei Möglichkeiten, ein Feld in einer Domänenklasse dem Feld `id` eines Azure Cosmos DB-Dokuments zuzuordnen:

- Kommentieren Sie das Feld mit `@Id`.
- Legen Sie den Namen des Felds auf `id` fest.

Das folgende Beispiel zeigt die Verwendung der Anmerkungen `@Container` und `@Id`.

```java
@Container(containerName = "myContainer")
class MyDocument {

    @Id
    private String myId;

    @PartitionKey
    private String data;

    @Version
    private String _etag;
}
```

Standardmäßig wird `IndexingPolicy` vom Azure-Dienst festgelegt. Fügen Sie zum Anpassen der Domänenklasse die Anmerkung `@DocumentIndexingPolicy` hinzu. Diese Anmerkung hat vier Attribute:

```java
boolean automatic;     // Indicates whether the indexing policy is automatic.
IndexingMode mode;     // The indexing policy mode; the options are Consistent, Lazy, or None.
String[] includePaths; // Included paths for indexing.
String[] excludePaths; // Excluded paths for indexing.
```

Das SDK unterstützt auch die Partitionierung. Weitere Informationen finden Sie unter [Partitionieren und horizontales Skalieren in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#:~:text=%20Partitioning%20and%20horizontal%20scaling%20in%20Azure%20Cosmos,partitions.%20Azure%20Cosmos%20DB%20transparently%20and...%20More%20). Wenn Sie ein Feld einer Domänenklasse als Partitionsschlüsselfeld angeben möchten, kommentieren Sie es mit `@PartitionKey`. Geben Sie dann beim Ausführen von CRUD-Vorgängen Ihren Partitionswert an.

Im folgenden Beispiel wird gezeigt, wie die Anmerkung `@PartitionKey` beim Ausführen von CRUD-Vorgängen verwendet wird.

```java
@Container(ru = "400")
public class Address {
    @Id
    String postalCode;

    @PartitionKey
    String city;

    String street;
    String country;
    String phoneNumber;

    ...
}

class AddressService {

    @Autowired
    AddressRepository repository;

    final Address newAddress = new Address("12345", "Seattle");

    // There's no need to specify a partition key in the save operation.
    repository.save(updatedAddress);

    // Provide a partition key when performing a find-by-id operation.
    final Optional<Address> addressById = repository.findById("12345", new PartitionKey("city"));

    final Address foundAddress = addressById.get();

    // Provide a partition key when performing a delete-by-id operation.
    repository.deleteById(foundAddress.getPostalCode(), new PartitionKey(foundAddress.getCity())); 
}
```

Das SDK unterstützt außerdem Suchvorgänge für benutzerdefinierte Spring Data-Abfragen, etwa `findByAFieldAndBField`. Weitere Informationen finden Sie in der Spring-Dokumentation unter [Festlegen von Abfragemethoden](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details).

## <a name="best-practices"></a>Bewährte Methoden

Die folgenden Abschnitte beschreiben bewährte Methoden für die Verwendung des SDK.

### <a name="pulling-configuration-properties-into-the-application"></a>Übertragen von Konfigurationseigenschaften in die Anwendung per Pull

Sie können eine Eigenschaftenklasse erstellen, die **application.properties**-Einstellungen als Java-Zugriffsmethoden verfügbar macht. Die Struktur von **application.properties** kann wie folgt aussehen:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Erstellen Sie entsprechend dieser Struktur ein `CosmosProperties`-Java-Klassenelement mit folgender Struktur:

```java
@ConfigurationProperties(prefix = "cosmos")
public class CosmosProperties {

    private String uri;

    private String key;

    private String secondaryKey;

    private boolean queryMetricsEnabled;

    public String getUri() {
        return uri;
    }

    public void setUri(String uri) {
        this.uri = uri;
    }

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getSecondaryKey() {
        return secondaryKey;
    }

    public void setSecondaryKey(String secondaryKey) {
        this.secondaryKey = secondaryKey;
    }

    public boolean isQueryMetricsEnabled() {
        return queryMetricsEnabled;
    }

    public void setQueryMetricsEnabled(boolean enableQueryMetrics) {
        this.queryMetricsEnabled = enableQueryMetrics;
    }
}
```

Beachten Sie, dass diese Klasse über einen Member verfügt, der den einzelnen **application.properties**-Konfigurationseigenschaften entspricht, und dass `CosmosProperties` für jeden Member *get*- und *set*-Methoden verfügbar macht. Die Anmerkung `@ConfigurationProperties` gibt an, dass die Klasse Konfigurationseigenschaften darstellt, und das `prefix = "cosmos"`-Argument gibt an, dass ein bestimmter *Member* von `CosmosProperties` der `cosmos.member`-Eigenschaft in **application.properties** zugeordnet ist.

Im nächsten Abschnitt erfahren Sie, wie Sie Ihre `CosmosProperties`-Klasse in den automatisierten Konfigurationsflow integrieren. Zur Konfigurationszeit wird eine `CosmosProperties`-Instanz erstellt, und die Instanzmethoden werden mit den Konfigurationseinstellungen in **application.properties** aufgefüllt. Diese Eigenschafteninstanz ermöglicht der Anwendung das Lesen und Ändern von Konfigurationseigenschaften zur Laufzeit.

### <a name="configuring-the-application-based-on-properties"></a>Konfigurieren der Anwendung auf der Grundlage von Eigenschaften

Der nächste Schritt ist das Erstellen einer Konfigurationsklasse, die die Konfiguration der Anwendung automatisiert. Das folgende Beispiel führt Sie durch die Erstellung.

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class AppConfiguration extends AbstractCosmosConfiguration {

    private static final Logger logger = LoggerFactory.getLogger(QuickstartSampleConfiguration.class);

    @Autowired
    private CosmosProperties properties;

    private AzureKeyCredential azureKeyCredential;

    @Bean
    public CosmosClientBuilder cosmosClientBuilder() {
        this.azureKeyCredential = new AzureKeyCredential(properties.getKey());
        return new CosmosClientBuilder()
            .endpoint(properties.getUri())
            .key(this.azureKeyCredential)
    }

    @Bean
    public CosmosConfig cosmosConfig() {
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig();        
        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig);                           
                           .build();
    }

    @Override
    protected String getDatabaseName() {
        return "testdb";
    }

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            logger.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Erstellen der Struktur der Konfigurationsklasse:

1. Erweitern Sie die Klasse `AbstractCosmosConfiguration`, um die Konfiguration der Anwendung (Cosmos DB-Schlüssel, -URL, -Datenbankname usw.) einzurichten.
1. Fügen Sie die Anmerkung `@Configuration` hinzu.
1. Fügen Sie abhängig von der Repositorynutzung mindestens eine der Anmerkungen `@EnableCosmosRepositories` und `@EnableReactiveCosmosRepositories` hinzu.
1. Fügen Sie die `@PropertySource("classpath:application.properties")`-Anmerkung hinzu, mit der signalisiert wird, Schlüssel-Wert-Paare von Eigenschaften aus **application.properties** zu extrahieren.
1. Fügen Sie die `@EnableConfigurationProperties`-Anmerkung hinzu, die Spring Data auf eine Klasse verweist, die Schlüssel-Wert-Paare aus **application.properties** speichern kann. Diese Anmerkung nimmt die Klassendefinition als Argument an. Sie müssen `CosmosProperties.class` übergeben.

Die Konfigurationsklasse verwendet die folgenden Member:

1. Deklarieren und definieren Sie einen log4j2-`logger`-Member, der Spring Data für alle Protokollausgaben verwendet.
1. Deklarieren Sie einen `@Autowired`-`CosmosProperties`-Member, **in dem die application.properties-Einstellungen abgelegt werden.**

Mit der Funktion `AzureKeyCredential` können Sie Schlüssel spontan rotieren. Um dies zu aktivieren, definieren Sie einen `AzureKeyCredential`-Member. Sie können Schlüssel wechseln, indem Sie eine `switchToSecondaryKey`-Methode hinzufügen, wie im obigen Beispiel gezeigt.

Als nächstes müssen Sie definieren, wie die automatisierte Konfiguration durchgeführt werden soll.
1. Definieren Sie eine `@Bean`-`cosmosClientBuilder`-Methode, um die Clientinitialisierung mit `CosmosClientBuilder` durchzuführen. Der Zweck dieser Methode ist das Durchführen einer grundlegenden Clienteinrichtung, d. h. den Kontoendpunkt-URI und den Zugriffsschlüssel anzugeben. In der Regel sind der Kontoendpunkt-URI und der Zugriffsschlüssel in **application.properties** definiert, die wiederum in `properties` eingetragen werden. Sie können den `azureKeyCredential`-Member mit `properties.getKey()` initialisieren und dann `properties.getUri()` und `this.azureKeyCredential` an die `endpoint`- bzw. `key`-Generatormethode übermitteln. 

Beachten Sie, dass `cosmosClientBuilder` im obigen Beispiel `build()` nicht für den Clientgenerator aufruft, sondern die nicht abgeschlossene Generatorstruktur zurückgegeben wird. Mit Spring Data können wir die Konfiguration in zwei Phasen durchführen: Zuerst kann `cosmosClientBuilder` die grundlegende Konfiguration anwenden und die Konfigurationsstruktur zurückgeben. Spring Data ruft dann eine `cosmosConfig`-Methode auf, die es Ihnen ermöglicht, erweiterte Konfigurationen wie z. B. Metriken und Diagnosen zu definieren. Im nächsten Schritt werden wir diese erweiterte Konfiguration in der `cosmosConfig`-Methode durchlaufen:
1. Erstellen Sie wie oben gezeigt eine `@Bean`-`cosmosConfig`-Methode.
1. Azure Cosmos DB kann serverseitige Diagnosen zurückgeben, die jeder Anforderung zugeordnet sind. Spring Data ermöglicht Ihnen die Transformation der unformatierten Diagnoseausgabe, bevor sie protokolliert wird, indem Sie einen Kundendiagnoseprozessor definieren. Definieren Sie wie oben gezeigt eine Klasse, die `ResponseDiagnosticsProcessor` implementiert und die `processResponseDiagnostics`-Methode überschreibt. Sie können `processResponseDiagnostics` definieren, um zu steuern, wie Diagnoseausgaben behandelt werden. Im obigen Beispiel wird einfach die unformatierte Diagnose protokolliert.
1. Zum Aktivieren der Diagnose und zum Initialisieren des Diagnoseprozessors rufen Sie die `responseDiagnosticsProcessor`-Generatormethode auf, und übergeben Sie eine neue Instanz der Kundenprozessorklasse:

    ```java
    return CosmosConfig.builder()
                       .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
    ```
1. Azure Cosmos DB verfügt auch über eine spezifischere Leistungsmetrikfunktionalität für Abfragen, die als „Abfragemetriken“ bezeichnet wird. Wie im vorherigen Abschnitt gezeigt, besteht die bewährte Methode darin, eine **application.properties**-Einstellung zu haben, die Abfragemetriken aktiviert/deaktiviert. Wenden Sie diese Konfigurationseinstellung an, indem Sie die `.enableQueryMetrics(properties.isQueryMetricsEnabled())`-Generatormethode in `cosmosConfig` übernehmen.
1. Die Konnektivität im direkten Modus wird für minimale Latenz und maximalem Durchsatz empfohlen. Diese können Sie also auch im Clientgenerator konfigurieren.

Nachdem die erweiterte Konfiguration in `cosmosConfig` fertiggestellt wurde, müssen wir die Clienterstellung durch Aufrufen von `build()` für die Konfigurationsstruktur auslösen. Dadurch wird eine Azure Cosmos DB Client-Instanz basierend auf Ihren Konfigurationseinstellungen generiert.

Der letzte Schritt beim Definieren des Konfigurationsvorgangs ist das Hinzufügen der `@Override`-Methode `getDatabaseName()` die den Namen der Azure Cosmos DB-Datenbank als Zeichenfolge zurückgibt.

### <a name="customizing-the-configuration"></a>Anpassen der Konfiguration

Sie können die Konfiguration auch anpassen, um den Verbindungsmodus, die maximale Größe des Verbindungspools, das Anforderungstimeout usw. zu ändern, wie im folgenden Beispiel gezeigt:

```java
    @Bean
    public CosmosConfig cosmosConfig() {

        // Set the connection mode to Direct (TCP) which applies to data plane operations
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig(); 

        // Even in Direct mode, some control plane operations always pass through the gateway as HTTP requests (i.e. container/database CRUD.)
        // Optionally, you can customize connection properties for these specific operations which are
        // always Gateway mode
        GatewayConnectionConfig gatewayConnectionConfig = GatewayConnectionConfig.getDefaultConfig(); 

        // Set the maximum number of HTTP connections to 1000 per application.
        gatewayConnectionConfig.setMaxConnectionPoolSize(1000);

        // Set the request timeout to 10 seconds.
        gatewayConnectionConfig.setIdleConnectionTimeout(Duration.ofMillis(10000));

        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig, gatewayConnectionConfig); // directMode() has an override which accepts Gateway config                        
                           .build();
    }
```

### <a name="response-diagnostics-and-query-metrics"></a>Antwortdiagnose und Abfragemetriken

Das Spring Data Azure Cosmos DB SDK unterstützt Antwortdiagnosezeichenfolgen und Abfragemetriken seit Version 2.

Legen Sie zum Aktivieren von Abfragemetriken in der Datei `application.properties` das Flag `queryMetricsEnabled` auf **true** fest. Befolgen Sie dann das im vorherigen Abschnitt beschriebene Verfahren, um die `ResponseDiagnosticsProcessor`-Schnittstelle zu erweitern und die `processResponseDiagnostics`-Methode zu implementieren, um die Diagnoseinformationen zu protokollieren. Übergeben Sie schließlich eine Instanz der Implementierung an die Methode `CosmosDbConfig.setResponseDiagnosticsProcessor`.

### <a name="pagination-and-sorting"></a>Paginierung und Sortierung

Das Spring Data Azure Cosmos DB SDK unterstützt die Spring Data-Paginierung und -Sortierung. Weitere Informationen finden Sie in der Spring-Dokumentation unter [Behandlung spezieller Parameter](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters).

Basierend auf den verfügbaren Anforderungseinheiten (Request Units, RUs) für das Datenbankkonto kann Cosmos DB Dokumente zurückgeben, die kleiner als die angeforderte Größe sind oder der angeforderten Größe entsprechen. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](/azure/cosmos-db/request-units).

Sie sollten sich nicht auf den Wert `totalPageSize` verlassen, weil die Anzahl der zurückgegebenen Dokumente in jeder Iteration variabel ist. Stattdessen sollten Sie ein `Pageable`-Objekt durchlaufen, wie im folgenden Beispiel gezeigt:

```java
final Sort sort = Sort.by(Sort.Direction.DESC, "name");
final CosmosPageRequest pageRequest = new CosmosPageRequest(0, pageSize,   null, sort);
Page<T> page = tRepository.findAll(pageRequest);
List<T> pageContent = page.getContent();
while(page.hasNext()) {
    Pageable nextPageable = page.nextPageable();
    page = repository.findAll(nextPageable);
    pageContent = page.getContent();
}
```

## <a name="common-issues-and-workarounds"></a>Häufig auftretende Probleme und Problemumgehungen

Die folgenden Abschnitte enthalten Informationen zu Problemen, die bei der Verwendung des Spring Data Azure Cosmos DB SDK auftreten können.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Abrufen der richtigen Cosmos DB-Konfiguration

Das Erweitern der `AbstractCosmosConfiguration`-Schnittstelle ist aufgrund verschiedener Anmerkungen und Konfigurationen, die in der Klasse enthalten sind, unter Umständen nicht ganz einfach. Am häufigsten tritt ein Problem mit der Anmerkung `Enable Repositories` auf.

Wenn die Repositorys `CosmosRepository` erweitern, achten Sie darauf, die Anmerkung `@EnableCosmosRepositories`hinzuzufügen. Wenn die Repositorys `ReactiveCosmosRepository` erweitern, achten Sie darauf, die Anmerkung `@EnableReactiveCosmosRepositories`hinzuzufügen. Das folgende Beispiel veranschaulicht die Verwendung dieser Anmerkungen:

```java
@Configuration
@EnableConfigurationProperties(CosmosProperties.class)
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
@PropertySource("classpath:application.properties")
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

Verwenden Sie beim Erstellen oder Anpassen eines `CosmosDBConfig`-Beans unbedingt das Objekt `AzureKeyCredential`, statt den Schlüssel direkt zu verwenden.

Mit der Funktion `AzureKeyCredential` können Sie Schlüssel spontan rotieren. Mit der Methode `switchToSecondaryKey` können Sie Schlüssel wechseln.

`AzureKeyCredential` muss ein Singleton-Objekt sein, weil das Cosmos DB SDK das gleiche Objekt intern verwendet, um Änderungen im Schlüsselwert innerhalb dieses Objekt zu erkennen.

### <a name="custom-query-execution"></a>Ausführen von benutzerdefinierten Abfragen

Spring Data Azure Cosmos DB SDK 3.x.x unterstützt die `@query`-Anmerkung zum Definieren benutzerdefinierter Abfragen.

Der folgende Code zeigt ein einfaches Beispiel für das Definieren von Offset- und Limit-Abfragen mithilfe der `@query`-Anmerkung:

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {

    ...

    @Query(value = "SELECT * from c OFFSET @skipCount LIMIT @takeCount")
    List<SampleEntity> findByName(@Param("skipCount") int skipCount, @Param("takeCount") int takeCount);
}
```

### <a name="enable-diagnostics-and-query-metrics"></a>Aktivieren von Diagnose und Abfragemetriken

Beim Debuggen sind die Antwortdiagnosezeichenfolge und Abfragemetriken aus dem Cosmos DB SDK hilfreich. Das Cosmos DB SDK protokolliert die Antwortdiagnosezeichenfolge auf der Clientseite. Das Back-End protokolliert die Abfragemetriken und stellt sie für das Cosmos DB SDK bereit.

Die Methode `ResponseDiagnosticsProcessor.processResponseDiagnostics` wird nach jedem API-Aufruf im Spring Data Azure Cosmos DB SDK aufgerufen. Daher ist es wichtig, dass Ihre Implementierung fehlerfrei und einfach ist, um eine hohe Leistung zu erzielen. Es wird beispielsweise empfohlen, nicht die vollständigen Diagnoseinformationen in dieser Methode zu protokollieren, da die Menge an Informationen zu erheblichen Leistungseinbußen führen würde. Darüber hinaus sollten Sie den Protokolliergrad `Debug` verwenden, um Auswirkungen auf die Anwendungsleistung zu vermeiden.

Der folgende Code zeigt ein Beispiel für die Implementierung der Schnittstelle `ResponseDiagnosticsProcessor`:

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log Cosmos DB response diagnostics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Cosmos DB response diagnostics {}", cosmosResponseDiagnostics);
        }

        // To log just the request latency:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Request latency {}", cosmosResponseDiagnostics.requestLatency());
        }

        // To log query metrics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            FeedResponseDiagnostics feedResponseDiagnostics =
                responseDiagnostics.getFeedResponseDiagnostics();
            log.debug("Query metrics {}", feedResponseDiagnostics);
        }
    }
}
```

## <a name="how-to-troubleshoot"></a>Informationen zur Problembehandlung

In den folgenden Abschnitten werden Methoden zur Problembehandlung bei allgemeinen Problemen beschrieben.

### <a name="connection-issues"></a>Verbindungsprobleme

Wenn Verbindungsprobleme auftreten, vergewissern Sie sich, dass alle erforderlichen Anmerkungen in der Konfigurationsklasse vorhanden und korrekt sind. Informationen hierzu finden Sie im Abschnitt [Abrufen der richtigen Cosmos DB-Konfiguration](#getting-the-correct-cosmos-db-configuration).

### <a name="naming-changes"></a>Benennungsänderungen

Version 3.1.0+ des Spring Data Azure Cosmos DB SDK enthält die folgenden wesentlichen Änderungen an den Namen/Schnittstellen von Klassen, Methoden, Anmerkungen und Maven-Artefakten:
* Die Gruppen-ID wurde auf `com.azure` aktualisiert.
* Die Artefakt-ID wurde auf `azure-spring-data-cosmos` aktualisiert.
* Aktualisierte Synchronisierungs-APIs geben Typen als `Iterable`-Typen statt als `List` zurück.
* `CosmosDbFactory` in `CosmosFactory`.
* `CosmosDBConfig` in `CosmosConfig`.
* `CosmosDBAccessException` in `CosmosAccessException`.
* `Document`-Anmerkung in die `Container`-Anmerkung.
* `DocumentIndexingPolicy`-Anmerkung in die `CosmosIndexingPolicy`-Anmerkung.
* `DocumentQuery` in `CosmosQuery`.
* **application.properties**-Flag `populateQueryMetrics` in `queryMetricsEnabled`.

### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen

Version 3.1.0+ des Spring Data Azure Cosmos DB SDK enthält die folgenden wichtigen Fehlerbehebungen:
* Es wurde ein Problem behoben, bei dem mit Anmerkungen versehene Abfragen nicht den mit Anmerkungen versehenen Containernamen ausgewählt haben.
* Diagnoseprotokollierungstasks werden als parallele Threads geplant, um das Blockieren von Netty-E/A-Threads zu vermeiden.
* Optimistische Sperre beim Löschvorgang behoben.
* Problem beim Versehen von Abfragen mit Escapezeichen für die IN-Klausel behoben.
* Ein Problem wurde behoben, indem der long-Datentyp für @Id zugelassen wurde.
* Ein Problem wurde behoben, indem „boolean“, „long“, „int“ und „double“ als Datentypen für die @PartitionKey-Anmerkung zugelassen wurden.
* Schlüsselwörter „IgnoreCase“ und „AllIgnoreCase“ für Abfragen ohne Berücksichtigung von Groß-/Kleinschreibung korrigiert.
* Der Standardwert 4000 für die Anforderungseinheit beim automatischen Erstellen von Containern wurde entfernt.
* Ein Fehler im Zusammenhang mit geschachteltem Partitionsschlüssel bei Verwendung mit der Anmerkung @GeneratedValue wurde behoben.

### <a name="api-or-query-slowness"></a>API oder langsame Abfragen

Wenn bei APIs oder Abfrageausführungen lange Wartezeiten auftreten, versuchen Sie, die Diagnosezeichenfolgen und Abfragemetriken zu protokollieren, wie im Abschnitt [Aktivieren von Diagnose und Abfragemetriken](#enable-diagnostics-and-query-metrics) beschrieben. Überprüfen Sie CPU-Auslastung, Netzwerkbandbreite und E/A-Speicherplatz, da diese eine langsame Ausführung auf der Clientseite verursachen können.