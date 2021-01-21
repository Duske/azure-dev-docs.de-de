---
title: Entwicklerhandbuch zu Spring Data Azure Cosmos DB
description: In diesem Leitfaden werden die Features, Probleme, Problemumgehungen und Diagnoseschritte beschrieben, die Sie bei der Verwendung des Spring Data Azure Cosmos DB SDK berücksichtigen sollten.
author: anfeldma-ms
ms.author: anfeldma
ms.topic: conceptual
ms.date: 11/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: ebec3cdc6a1f16534132a333b4c6119c5e6208bd
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561816"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Entwicklerhandbuch zu Spring Data Azure Cosmos DB

In diesem Artikel werden die Features von [Spring Data Azure Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) bei Verwendung der SQL-API beschrieben. Darüber hinaus enthält dieser Artikel Anleitungen bei allgemeinen Problemen sowie Problemumgehungen und Diagnoseschritte.

[Azure Cosmos DB](/azure/cosmos-db/introduction) ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs die Arbeit mit Daten ermöglicht. Das Spring Data Azure Cosmos DB SDK basiert auf dem [Spring Data](https://spring.io/projects/spring-data)-Framework und ermöglicht dank der SQL-API die Integration mit Azure Cosmos DB. Informationen zur Unterstützung anderer APIs finden Sie unter:

- [Verwenden der Spring Data-MongoDB-API mit Azure Cosmos DB](./configure-spring-data-mongodb-with-cosmos-db.md)
- [Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB](./configure-spring-data-apache-cassandra-with-cosmos-db.md)
- [Verwenden von Spring Data Gremlin Starter mit der SQL-API von Azure Cosmos DB](./configure-spring-data-gremlin-java-app-with-cosmos-db.md)

Das Spring Data Azure Cosmos DB SDK ist als Open Source-Ressource im Repository [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) auf GitHub verfügbar. Dieses Repository enthält eine aktive [Liste von Problemen](https://github.com/Azure/azure-sdk-for-java/issues), über die Sie Fehler melden oder Problemumgehungen für bereits eingereichte Probleme suchen können. Sie können auch auf der Seite [Releaseverlauf](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md) überprüfen, ob ein Problem in einer neueren Version behoben wurde. 

## <a name="available-features"></a>Verfügbare Features

In den folgenden Abschnitten werden die derzeit im Spring Data Azure Cosmos DB SDK verfügbaren Features beschrieben.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Unterstützung für CrudRepository und ReactiveCrudRepository

Das Spring Data Azure Cosmos DB SDK stellt die Schnittstellen `CosmosRepository` und `ReactiveCosmosRepository` bereit, die die Spring Data-Schnittstellen `CrudRepository` und `ReactiveCrudRepository` erweitern.

Im folgenden Beispiel wird gezeigt, wie Sie diese Schnittstellen erweitern:

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

Je nach Verwendungszweck müssen Sie die einzelnen Repositorys separat in der Klasse `Configuration` aktivieren. Beispiel:

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

Der Sammlungsname ist standardmäßig der Klassenname der Benutzerdomänenklasse. Fügen Sie zum Anpassen der Domänenklasse die Anmerkung `@Container(containerName="myCustomCollectionName")` hinzu. Das Feld `containerName` unterstützt darüber hinaus [SpEL-Ausdrücke](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (Spring Expression Language, Spring-Ausdruckssprache), sodass Sie Sammlungsnamen programmgesteuert über Konfigurationseigenschaften angeben können. Sie können beispielsweise Ausdrücke wie `containerName = "${dynamic.container.name}"` und `containerName = "#{@someBean.getContainerName()}"` verwenden.

Ihnen stehen zwei Vorgehensweisen zur Auswahl, um ein Feld in einer Domänenklasse dem `id`-Feld eines Azure Cosmos DB-Dokuments zuzuordnen:

- Kommentieren Sie das Feld mit `@Id`.
- Legen Sie den Namen des Felds auf `id` fest.

In den folgenden Beispielen wird die Verwendung der Anmerkungen `@Container` und `@Id` veranschaulicht:

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
IndexingMode mode;     // The indexing policy mode. The options are Consistent, Lazy, or None.
String[] includePaths; // The included paths for indexing.
String[] excludePaths; // The excluded paths for indexing.
```

Das SDK unterstützt die Partitionierung. Weitere Informationen finden Sie unter [Partitionieren und horizontales Skalieren in Azure Cosmos DB](/azure/cosmos-db/partitioning-overview). Wenn Sie ein Feld einer Domänenklasse als Partitionsschlüsselfeld angeben möchten, versehen Sie es mit der Anmerkung `@PartitionKey`. Geben Sie dann beim Ausführen von CRUD-Vorgängen Ihren Partitionswert an.

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

Das SDK unterstützt außerdem Suchvorgänge für benutzerdefinierte Spring Data-Abfragen, etwa `findByAFieldAndBField`. Weitere Informationen finden Sie im Abschnitt „Definieren von Abfragemethoden“ der [Spring Data Commons-Referenzdokumentation](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details).

## <a name="best-practices"></a>Bewährte Methoden

In den folgenden Abschnitten werden bewährte Methoden für die Verwendung des SDK beschrieben.

### <a name="pull-configuration-properties-into-the-application"></a>Übertragen von Konfigurationseigenschaften an die Anwendung per Pull

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

Beachten Sie, dass diese Klasse über einen Member verfügt, der den einzelnen **application.properties**-Konfigurationseigenschaften entspricht, und dass `CosmosProperties` die Methoden `get` und `set` zur Verfügung stellt. Die Anmerkung `@ConfigurationProperties` gibt an, dass die Klasse Konfigurationseigenschaften darstellt, und das `prefix = "cosmos"`-Argument gibt an, dass ein bestimmter *Member* von `CosmosProperties` der `cosmos.member`-Eigenschaft in **application.properties** zugeordnet ist.

Im nächsten Abschnitt erfahren Sie, wie Sie Ihre `CosmosProperties`-Klasse in den automatisierten Konfigurationsflow integrieren. Zur Konfigurationszeit wird eine `CosmosProperties`-Instanz erstellt, und die Instanzmethoden werden mit den Konfigurationseinstellungen in **application.properties** aufgefüllt. Diese Eigenschafteninstanz ermöglicht der Anwendung das Lesen und Ändern von Konfigurationseigenschaften zur Laufzeit.

### <a name="configure-the-application-based-on-properties"></a>Konfigurieren der Anwendung auf der Grundlage von Eigenschaften

Ihr nächster Schritt besteht darin, eine Konfigurationsklasse zu erstellen, die die Konfiguration der Anwendung automatisiert, wie im Folgenden gezeigt:

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

Im Folgenden werden die Schritte zur Erstellung des obigen Beispiels erläutert. Gehen Sie folgendermaßen vor, um die Struktur Ihrer Konfigurationsklasse zu erstellen:

1. Erweitern Sie die Klasse `AbstractCosmosConfiguration`, um die Konfiguration der Anwendung (Azure Cosmos DB-Schlüssel, -URL, -Datenbankname usw.) einzurichten.
1. Fügen Sie die Anmerkung `@Configuration` hinzu.
1. Fügen Sie abhängig von der Repositorynutzung mindestens eine der Anmerkungen `@EnableCosmosRepositories` und `@EnableReactiveCosmosRepositories` hinzu.
1. Fügen Sie die `@PropertySource("classpath:application.properties")`-Anmerkung hinzu, mit der signalisiert wird, Schlüssel-Wert-Paare von Eigenschaften aus **application.properties** zu extrahieren.
1. Fügen Sie die `@EnableConfigurationProperties`-Anmerkung hinzu, die Spring Data auf eine Klasse verweist, die Schlüssel-Wert-Paare aus **application.properties** speichern kann. Diese Anmerkung akzeptiert die Klassendefinition als Argument. Sie sollten `CosmosProperties.class` übergeben.

Die Konfigurationsklasse verwendet die folgenden Member:

* Deklarieren und definieren Sie einen log4j2-`logger`-Member, der Spring Data für alle Protokollausgaben verwendet.
* Deklarieren Sie einen `@Autowired` `CosmosProperties`-Member, in dem die **application.properties**-Einstellungen abgelegt werden.

Mithilfe des `AzureKeyCredential`-Features können Sie Schlüssel bei Bedarf rotieren. Definieren Sie einen `AzureKeyCredential`-Member, um das Feature zu aktivieren. Sie können die Schlüssel wechseln, indem Sie wie im vorherigen Beispielcode gezeigt eine `switchToSecondaryKey`-Methode hinzufügen.

Als Nächstes müssen Sie definieren, wie die automatisierte Konfiguration durchgeführt werden soll.
1. Definieren Sie eine `@Bean` `cosmosClientBuilder`-Methode zum Verarbeiten der Clientinitialisierung mit `CosmosClientBuilder`. Der Zweck dieser Methode besteht darin, eine grundlegende Clienteinrichtung durchzuführen (einen Kontoendpunkt-URI und Zugriffsschlüssel angeben). Der Kontoendpunkt-URI und der Zugriffsschlüssel werden in der Regel in **application.properties** definiert, was wiederum in `properties` eingefügt wird. 
1. Sie können den Member `azureKeyCredential` mithilfe von `properties.getKey()` initialisieren. 
1. Anschließend können Sie `properties.getUri()` an die `endpoint`-Generatormethode und `this.azureKeyCredential` an die `key`-Generatormethode übertragen. 

Beachten Sie, dass `cosmosClientBuilder` im vorherigen Beispiel keinen Aufruf von `build()` für den Clientgenerator ausführt. Die nicht fertiggestellte Generatorstruktur wird zurückgegeben. Mit Spring Data können Sie die Konfiguration in zwei Phasen durchführen: Zunächst wendet `cosmosClientBuilder` die grundlegende Konfiguration an und gibt die Konfigurationsstruktur zurück, dann ruft Spring Data eine `cosmosConfig`-Methode auf, mit der Sie eine fortgeschrittenere Konfiguration wie Metriken und Diagnosen definieren können. 

Als Nächstes wird diese erweiterte Konfiguration in der `cosmosConfig`-Methode erläutert:
1. Erstellen Sie wie zuvor beschrieben eine `@Bean` `cosmosConfig`-Methode.
   
   Azure Cosmos DB kann serverseitige Diagnosen zurückgeben, die jeder Anforderung zugeordnet sind. Mit Spring Data können Sie die unformatierte Diagnoseausgabe transformieren, bevor sie protokolliert wird, indem Sie einen Kundendiagnoseprozessor definieren. 

1. Definieren Sie außerdem wie zuvor beschrieben eine Klasse, die `ResponseDiagnosticsProcessor` implementiert und die Methode `processResponseDiagnostics` überschreibt. Sie können `processResponseDiagnostics` definieren, um zu steuern, wie Diagnoseausgaben behandelt werden. Im obigen Beispiel wird einfach die unformatierte Diagnose protokolliert.

1. Zum Aktivieren der Diagnose und zum Initialisieren des Diagnoseprozessors rufen Sie die `responseDiagnosticsProcessor`-Generatormethode auf, die eine neue Instanz der Kundenprozessorklasse wie im Folgenden gezeigt übergibt:

    ```java
    return CosmosConfig.builder()
                       .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
    ```

   Azure Cosmos DB verfügt auch über eine spezifischere Leistungsmetrikfunktionalität für Abfragen, die als „Abfragemetriken“ bezeichnet wird. Wie zuvor gezeigt besteht die bewährte Methode darin, eine **application.properties**-Einstellung zu besitzen, die Abfragemetriken aktiviert und deaktiviert. 
   
1. Wenden Sie diese Konfigurationseinstellung an, indem Sie die `.enableQueryMetrics(properties.isQueryMetricsEnabled())`-Generatormethode in `cosmosConfig` übernehmen.

1. Die Konnektivität im direkten Modus wird für minimale Latenz und maximalem Durchsatz empfohlen. Diese können Sie also auch im Clientgenerator konfigurieren.

1. Nachdem Sie die erweiterte Konfiguration in `cosmosConfig` vorgenommen haben, lösen Sie die Clienterstellung aus, indem Sie `build()` für die Konfigurationsstruktur aufrufen. Dadurch wird eine Azure Cosmos DB-Clientinstanz generiert, die auf Ihren Konfigurationseinstellungen basiert.

1. Der letzte Schritt beim Definieren des Konfigurationsvorgangs ist das Hinzufügen der `@Override`-Methode `getDatabaseName()`, die den Namen der Azure Cosmos DB-Datenbank als Zeichenfolge zurückgibt.

### <a name="customize-the-configuration"></a>Anpassen der Konfiguration

Sie können die Konfiguration auch anpassen, um den Verbindungsmodus, die maximale Größe des Verbindungspools, das Anforderungstimeout usw. zu ändern, wie im folgenden Beispiel gezeigt:

```java
    @Bean
    public CosmosConfig cosmosConfig() {

        // Set the connection mode to Direct (TCP), which applies to data plane operations.
        DirectConnectionConfig directConnectionConfig = DirectConnectionConfig.getDefaultConfig(); 

        // Even in Direct mode, some control plane operations always pass through the gateway as HTTP requests (that is, container/database CRUD [create, retrieve, update, and delete]).
        // Optionally, you can customize connection properties for these specific operations, which are always Gateway mode.
        GatewayConnectionConfig gatewayConnectionConfig = GatewayConnectionConfig.getDefaultConfig(); 

        // Set the maximum number of HTTP connections to 1000 per application.
        gatewayConnectionConfig.setMaxConnectionPoolSize(1000);

        // Set the request timeout to 10 seconds.
        gatewayConnectionConfig.setIdleConnectionTimeout(Duration.ofMillis(10000));

        return CosmosConfig.builder()
                           .responseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation())
                           .enableQueryMetrics(properties.isQueryMetricsEnabled())
                           .directMode(directConnectionConfig, gatewayConnectionConfig); // directMode() has an override that accepts Gateway config.                        
                           .build();
    }
```

### <a name="response-diagnostics-and-query-metrics"></a>Antwortdiagnose und Abfragemetriken

Ab Version 2 unterstützt das Spring Data Azure Cosmos DB SDK Antwortdiagnosezeichenfolgen und Abfragemetriken.

Legen Sie zum Aktivieren von Abfragemetriken in der Datei `application.properties` das Flag `queryMetricsEnabled` auf **true** fest. 

Befolgen Sie dann das im vorherigen Abschnitt beschriebene Verfahren, um die `ResponseDiagnosticsProcessor`-Schnittstelle zu erweitern und die `processResponseDiagnostics`-Methode zu implementieren, um die Diagnoseinformationen zu protokollieren. 

Übergeben Sie schließlich eine Instanz der Implementierung an die Methode `CosmosDbConfig.setResponseDiagnosticsProcessor`.

### <a name="pagination-and-sorting"></a>Paginierung und Sortierung

Das Spring Data Azure Cosmos DB SDK unterstützt die Spring Data-Paginierung und -Sortierung. Weitere Informationen finden Sie im Abschnitt „Behandlung spezieller Parameter“ der [Spring Data Commons-Referenzdokumentation](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters).

Basierend auf den verfügbaren Anforderungseinheiten (Request Units, RUs) für das Datenbankkonto kann Azure Cosmos DB Dokumente zurückgeben, die kleiner als die angeforderte Größe sind oder der angeforderten Größe entsprechen. Weitere Informationen finden Sie unter [Anforderungseinheiten in Azure Cosmos DB](/azure/cosmos-db/request-units).

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

In den folgenden Abschnitten werden Probleme beschrieben, die Sie bei Verwendung des Spring Data Azure Cosmos DB SDK berücksichtigen sollten.

### <a name="get-the-correct-azure-cosmos-db-configuration"></a>Abrufen der richtigen Azure Cosmos DB-Konfiguration

Das Erweitern der `AbstractCosmosConfiguration`-Schnittstelle ist aufgrund verschiedener Anmerkungen und Konfigurationen, die in der Klasse enthalten sind, unter Umständen nicht ganz einfach. Am häufigsten tritt ein Problem mit der Anmerkung `Enable Repositories` auf.

Wenn die Repositorys `CosmosRepository` erweitern, fügen Sie die Anmerkung `@EnableCosmosRepositories` hinzu. Wenn die Repositorys `ReactiveCosmosRepository` erweitern, fügen Sie die Anmerkung `@EnableReactiveCosmosRepositories` hinzu. Im folgenden Beispiel wird die Verwendung dieser Anmerkungen veranschaulicht:

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

Mithilfe des `AzureKeyCredential`-Features können Sie Schlüssel bei Bedarf rotieren. Mit der Methode `switchToSecondaryKey` können Sie Schlüssel wechseln.

`AzureKeyCredential` muss ein Singleton-Objekt sein, weil das Azure Cosmos DB SDK das gleiche Objekt intern verwendet, um Änderungen im Schlüsselwert innerhalb dieses Objekt zu erkennen.

### <a name="custom-query-execution"></a>Ausführen von benutzerdefinierten Abfragen

Spring Data Azure Cosmos DB SDK 3.x.x unterstützt die `@query`-Anmerkung zum Definieren benutzerdefinierter Abfragen.

Im folgenden Code wird ein einfaches Beispiel zum Definieren von Offset- und Limit-Abfragen mithilfe der `@query`-Anmerkung veranschaulicht:

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {

    ...

    @Query(value = "SELECT * from c OFFSET @skipCount LIMIT @takeCount")
    List<SampleEntity> findByName(@Param("skipCount") int skipCount, @Param("takeCount") int takeCount);
}
```

### <a name="enable-diagnostics-and-query-metrics"></a>Aktivieren von Diagnose und Abfragemetriken

Beim Debuggen sind die Antwortdiagnosezeichenfolge und Abfragemetriken aus dem Azure Cosmos DB SDK hilfreich. Das Azure Cosmos DB SDK protokolliert die Antwortdiagnosezeichenfolge auf der Clientseite. Das Back-End protokolliert die Abfragemetriken und stellt sie für das Azure Cosmos DB SDK bereit.

Die Methode `ResponseDiagnosticsProcessor.processResponseDiagnostics` wird nach jedem API-Aufruf im Spring Data Azure Cosmos DB SDK aufgerufen. Daher ist es wichtig, dass Ihre Implementierung fehlerfrei und einfach ist, um eine hohe Leistung zu erzielen. Beispielsweise sollten Sie nicht die vollständigen Diagnoseinformationen in dieser Methode zu protokollieren, da die Menge an Informationen zu erheblichen Leistungseinbußen führen würde. Darüber hinaus sollten Sie den Protokolliergrad `Debug` verwenden, um Auswirkungen auf die Anwendungsleistung zu vermeiden.

Im folgenden Code wird ein Beispiel für die Implementierung der `ResponseDiagnosticsProcessor`-Schnittstelle veranschaulicht:

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log the Azure Cosmos DB response diagnostics:
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

## <a name="troubleshoot-common-issues"></a>Häufig auftretende Probleme und Problembehandlung

In den folgenden Abschnitten werden Methoden zur Problembehandlung bei allgemeinen Problemen beschrieben.

### <a name="connection-issues"></a>Verbindungsprobleme

Wenn Verbindungsprobleme auftreten, vergewissern Sie sich, dass alle erforderlichen Anmerkungen in der Konfigurationsklasse vorhanden und korrekt sind. Informationen hierzu finden Sie im Abschnitt [Abrufen der richtigen Azure Cosmos DB-Konfiguration](#get-the-correct-azure-cosmos-db-configuration).

### <a name="naming-changes"></a>Benennungsänderungen

Ab Version 3.1.0 enthält das Spring Data Azure Cosmos DB SDK die folgenden wesentlichen Änderungen an den Namen und Schnittstellen von Klassen, Methoden, Anmerkungen und Maven-Artefakten:
* Die Gruppen-ID wurde auf `com.azure` aktualisiert.
* Die Artefakt-ID wurde auf `azure-spring-data-cosmos` aktualisiert.
* Aktualisierte Synchronisierungs-APIs geben Typen als `Iterable`-Typen statt als `List` zurück.
* `CosmosDbFactory` wurde in `CosmosFactory` geändert.
* `CosmosDBConfig` wurde in `CosmosConfig` geändert.
* `CosmosDBAccessException` wurde in `CosmosAccessException` geändert.
* Die Anmerkung `Document` wurde in die Anmerkung `Container` geändert.
* Die Anmerkung `DocumentIndexingPolicy` wurde in die Anmerkung `CosmosIndexingPolicy` geändert.
* `DocumentQuery` wurde in `CosmosQuery` geändert.
* Das **application.properties**-Flag `populateQueryMetrics` wurde in `queryMetricsEnabled` geändert.

### <a name="key-bug-fixes"></a>Wichtige Fehlerbehebungen

Ab Version 3.1.0 enthält das Spring Data Azure Cosmos DB SDK die folgenden wichtigen Fehlerbehebungen:
* Es wurde ein Problem behoben, bei dem mit Anmerkungen versehene Abfragen nicht den mit Anmerkungen versehenen Containernamen ausgewählt haben.
* Diagnoseprotokollierungstasks werden als parallele Threads geplant, um das Blockieren von Netty-E/A-Threads (Eingabe/Ausgabe) zu vermeiden.
* Optimistische Sperre beim Löschvorgang behoben.
* Ein Problem beim Versehen von Abfragen für die IN-Klausel mit Escapezeichen wurde behoben.
* Ein Problem wurde behoben, indem der long-Datentyp für @Id zugelassen wurde.
* Ein Problem wurde behoben, indem „boolean“, „long“, „int“ und „double“ als Datentypen für die @PartitionKey-Anmerkung zugelassen wurden.
* Die Schlüsselwörter „IgnoreCase“ und „AllIgnoreCase“ für Abfragen ohne Berücksichtigung von Groß-/Kleinschreibung wurden korrigiert.
* Der Standardwert 4000 für die Anforderungseinheit beim automatischen Erstellen von Containern wurde entfernt.
* Ein Fehler im Zusammenhang mit geschachteltem Partitionsschlüssel bei Verwendung mit der Anmerkung @GeneratedValue wurde behoben.

### <a name="api-or-query-slowness"></a>API oder langsame Abfragen

Wenn bei APIs oder Abfrageausführungen lange Wartezeiten auftreten, versuchen Sie, die Diagnosezeichenfolgen und Abfragemetriken wie im Abschnitt [Aktivieren von Diagnose- und Abfragemetriken](#enable-diagnostics-and-query-metrics) beschrieben zu protokollieren. Überprüfen Sie CPU-Auslastung, Netzwerkbandbreite und E/A-Speicherplatz, da diese eine langsame Ausführung auf der Clientseite verursachen können.