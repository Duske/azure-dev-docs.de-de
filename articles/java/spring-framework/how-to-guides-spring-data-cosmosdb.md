---
title: Entwicklerhandbuch zu Spring Data Azure Cosmos DB
description: In dieser Anleitung wird beschrieben, was Sie bei der Verwendung des Spring Data Azure Cosmos DB SDK beachten sollten.
author: kushagraThapar
ms.author: kuthapar
ms.topic: conceptual
ms.date: 1/9/2019
ms.custom: devx-track-java
ms.openlocfilehash: 119a2b3ff761778e111500a9fe0cf33d2dc6b080
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378444"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Entwicklerhandbuch zu Spring Data Azure Cosmos DB

In diesem Thema werden die Funktionen von [Spring Data Cosmos DB](https://github.com/microsoft/spring-data-cosmosdb) bei Verwendung der SQL-API beschrieben. Darüber hinaus enthält dieses Thema Anleitungen bei allgemeinen Problemen sowie Problemumgehungen und Diagnoseschritte.

[Azure Cosmos DB](/azure/cosmos-db/introduction) ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs die Arbeit mit Daten ermöglicht. Das Spring Data Cosmos DB SDK basiert auf dem [Spring Data](https://spring.io/projects/spring-data)-Framework und ermöglicht dank der SQL-API die Integration mit Azure Cosmos DB. Informationen zur Unterstützung anderer APIs finden Sie in den folgenden Themen:

- [Verwenden der Spring Data-MongoDB-API mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
- [Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db).
- [Verwenden von Spring Data Gremlin Starter mit der SQL-API von Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)

Das Spring Data Cosmos DB SDK ist als Open Source-Ressource im Repository [spring-data-cosmosdb](https://github.com/microsoft/spring-data-cosmosdb) auf GitHub verfügbar. Dieses Repository enthält eine aktive Liste mit [Problemen](https://github.com/microsoft/spring-data-cosmosdb/issues), über die Sie Fehler melden oder Problemumgehungen für bereits eingereichte Probleme suchen können. Sie können auch die Liste mit den [Releases](https://github.com/microsoft/spring-data-cosmosdb/releases) überprüfen, um zu ermitteln, ob ein Problem in einer neueren Version behoben wurde. Der Releaseplan für Version 2.2.x des Spring Data Cosmos DB SDK unterstützt die Version 2.2.0.RELEASE von „spring-data-commons“, der Releaseplan für Version 2.1.x des SDK hingegen Version 2.1.0.RELEASE von „spring-data-common“.

## <a name="available-features"></a>Verfügbare Funktionen

In den folgenden Abschnitten werden die derzeit verfügbar Funktionen beschrieben.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Unterstützung für CrudRepository und ReactiveCrudRepository

Das Spring Data Cosmos DB SDK stellt die Schnittstellen `CosmosRepository` und `ReactiveCosmosRepository` bereit, die die Spring Data-Schnittstellen `CrudRepository` und `ReactiveCrudRepository` erweitern.

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
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>Definieren einer einfachen Entität

Sie können Entitäten definieren, indem Sie die Anmerkung `@Document` hinzufügen und Eigenschaften im Zusammenhang mit der Sammlung angeben, etwa den Sammlungsnamen, Anforderungseinheiten (Request Units, RUs), Gültigkeitsdauer und das Sammlungsflag für die automatische Erstellung.

Der Sammlungsname ist standardmäßig der Klassenname der Klasse „user-domain“. Fügen Sie zum Anpassen der Domänenklasse die Anmerkung `@Document(collection="myCustomCollectionName")` hinzu. Das Sammlungsfeld unterstützt darüber hinaus [SpEL](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html)-Ausdrücke (Spring Expression Language, Spring-Ausdruckssprache), sodass Sie Sammlungsnamen programmgesteuert über Konfigurationseigenschaften angeben können. Sie können beispielsweise Ausdrücke wie `collection = "${dynamic.collection.name}"` und `collection = "#{@someBean.getCollectionName()}"` verwenden.

Es gibt zwei Möglichkeiten, ein Feld in einer Domänenklasse dem Feld `id` eines Azure Cosmos DB-Dokuments zuzuordnen:

- Kommentieren Sie das Feld mit `@Id`.
- Legen Sie den Namen des Felds auf `id` fest.

Das folgende Beispiel zeigt die Verwendung der Anmerkungen `@Document` und `@Id`.

```java
@Document(collection = "myCollection")
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

Das SDK unterstützt auch die Partitionierung. Weitere Informationen finden Sie unter [Partitionieren und horizontales Skalieren in Azure Cosmos DB](/azure/cosmos-db/partition-data). Wenn Sie ein Feld einer Domänenklasse als Partitionsschlüsselfeld angeben möchten, kommentieren Sie es mit `@PartitionKey`. Geben Sie dann beim Ausführen von CRUD-Vorgängen Ihren Partitionswert an.

Im folgenden Beispiel wird gezeigt, wie die Anmerkung `@PartitionKey` beim Ausführen von CRUD-Vorgängen verwendet wird.

```java
@Document(ru = "400")
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

    final Address newAddress = new Address("12345", "city");

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

### <a name="configuring-the-application"></a>Konfigurieren der Anwendung

Führen Sie zum Konfigurieren der Anwendung die folgenden Schritte aus:

1. Erweitern Sie die Klasse `AbstractCosmosConfiguration`, um die Konfiguration der Anwendung (Cosmos DB-Schlüssel, -URL, -Datenbankname usw.) einzurichten.
1. Fügen Sie die Anmerkung `@Configuration` hinzu.
1. Fügen Sie abhängig von der Repositorynutzung mindestens eine der Anmerkungen `@EnableCosmosRepositories` und `@EnableReactiveCosmosRepositories` hinzu.

Mit der Funktion `CosmosKeyCredential` können Sie Schlüssel spontan rotieren. Mit der Methode `switchToSecondaryKey` können Sie Schlüssel wechseln.

Der folgende Beispielcode zeigt eine Anwendungskonfiguration und veranschaulicht die Verwendung von `switchToSecondaryKey`:

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    @Value("${azure.cosmosdb.uri}")
    private String uri;

    @Value("${azure.cosmosdb.key}")
    private String key;

    @Value("${azure.cosmosdb.secondaryKey}")
    private String secondaryKey;

    @Value("${azure.cosmosdb.database}")
    private String dbName;

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private CosmosKeyCredential cosmosKeyCredential;

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri,
            this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Sie können die Konfiguration auch anpassen, um den Verbindungsmodus, die maximale Größe des Verbindungspools, das Anforderungstimeout usw. zu ändern, wie im folgenden Beispiel gezeigt:

```java
public CosmosDBConfig getConfig() {

    this.cosmosKeyCredential = new CosmosKeyCredential(key);
    ConnectionPolicy customizedConnectionPolicy = new ConnectionPolicy();

    // Set the connection mode to Direct (TCP).
    customizedConnectionPolicy.setConnectionMode(ConnectionMode.DIRECT);

    // Set the maximum number of HTTP/TCP connections to 1000 per application.
    customizedConnectionPolicy.setMaxPoolSize(1000);

    // Set the request timeout to 10 seconds.
    customizedConnectionPolicy.requestTimeoutInMillis(10000);

    // Set the idle connection timeout to two minutes.
    customizedConnectionPolicy.idleConnectionTimeoutInMillis(120000);
    CosmosDBConfig cosmosDbConfig = CosmosDBConfig.builder(uri,   this.cosmosKeyCredential, dbName)
                                                  .connectionPolicy  (customizedConnectionPolic  y)
                                                  .build();
    return cosmosDbConfig;
}
```

### <a name="response-diagnostics-and-query-metrics"></a>Antwortdiagnose und Abfragemetriken

Version 2.2.x des Spring Data Cosmos DB SDK unterstützt Antwortdiagnosezeichenfolgen und Abfragemetriken.

Legen Sie zum Aktivieren von Abfragemetriken in der Datei `application.properties` das Flag `populateQueryMetrics` auf **true** fest. Erweitern Sie dann die Schnittstelle `ResponseDiagnosticsProcessor`, und implementieren Sie die Methode `processResponseDiagnostics`, um die Diagnoseinformationen zu protokollieren. Übergeben Sie schließlich eine Instanz der Implementierung an die Methode `CosmosDbConfig.setResponseDiagnosticsProcessor`. Der folgende Code zeigt eine Beispielimplementierung:

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    ...

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            log.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri, this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }
}
```

### <a name="pagination-and-sorting"></a>Paginierung und Sortierung

Das Spring Data Cosmos DB SDK unterstützt die Spring Data-Paginierung und -Sortierung. Weitere Informationen finden Sie in der Spring-Dokumentation unter [Behandlung spezieller Parameter](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters).

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

Die folgenden Abschnitte enthalten Informationen zu Problemen, die bei der Verwendung des Spring Data Cosmos DB SDK auftreten können.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Abrufen der richtigen Cosmos DB-Konfiguration

Das Erweitern der `AbstractCosmosConfiguration`-Schnittstelle ist aufgrund verschiedener Anmerkungen und Konfigurationen, die in der Klasse enthalten sind, unter Umständen nicht ganz einfach. Am häufigsten tritt ein Problem mit der Anmerkung `Enable Repositories` auf.

Wenn die Repositorys `CosmosRepository` erweitern, achten Sie darauf, die Anmerkung `@EnableCosmosRepositories`hinzuzufügen. Wenn die Repositorys `ReactiveCosmosRepository` erweitern, achten Sie darauf, die Anmerkung `@EnableReactiveCosmosRepositories`hinzuzufügen. Das folgende Beispiel veranschaulicht die Verwendung dieser Anmerkungen:

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

Verwenden Sie beim Erstellen oder Anpassen eines `CosmosDBConfig`-Beans unbedingt das Objekt `CosmosKeyCredential`, statt den Schlüssel direkt zu verwenden.

Mit der Funktion `CosmosKeyCredential` können Sie Schlüssel spontan rotieren. Mit der Methode `switchToSecondaryKey` können Sie Schlüssel wechseln.

`CosmosKeyCredential` muss ein Singleton-Objekt sein, weil das Cosmos DB SDK das gleiche Objekt intern verwendet, um Änderungen im Schlüsselwert innerhalb dieses Objekt zu erkennen.

### <a name="custom-query-execution"></a>Ausführen von benutzerdefinierten Abfragen

Das Feature für die Abfrageanmerkung wird vom Spring Data Cosmos DB SDK noch nicht unterstützt. Bis dahin können Sie benutzerdefinierte und komplexe Abfragen direkt für das `cosmosClient`-Bean ausführen, das durch den Spring-Anwendungskontext verfügbar gemacht wird.

Der folgende Code zeigt ein einfaches Beispiel für das Ausführen von Offset- und Limit-Abfragen mithilfe des `cosmosClient`-Beans.

```java
final FeedOptions feedOptions = new FeedOptions();

// Enable cross-partition queries.
feedOptions.enableCrossPartitionQuery(true);

// Set the page size.
feedOptions.maxItemCount(20);

// Set the number of parallel operations on the client-side SDK when executing parallel queries.
feedOptions.maxDegreeOfParallelism(2);

// Populate query metrics from Cosmos DB.
feedOptions.populateQueryMetrics(true);

final String query = "SELECT * from c OFFSET " + skipCount + " LIMIT " + takeCount;

final CosmosClient cosmosClient = applicationContext.getBean(CosmosClient.class);

Flux<FeedResponse<CosmosItemProperties>> feedResponseFlux =
    cosmosClient.getDatabase(databaseId)
                .getContainer(collectionId)
                .queryItems(query, feedOptions);
    feedResponseFlux.subscribeOn(Schedulers.parallel())
                    .flatMap(feedResponse -> {
                        List<CosmosItemProperties> results =
                        feedResponseFlux.results();
                        log.info("Results are {}", results);
                        return feedResponse;
                    })
                    .subscribe();
```

### <a name="enable-diagnostics-and-query-metrics"></a>Aktivieren von Diagnose und Abfragemetriken

Beim Debuggen sind die Antwortdiagnosezeichenfolge und Abfragemetriken aus dem Cosmos DB SDK hilfreich. Das Cosmos DB SDK protokolliert die Antwortdiagnosezeichenfolge auf der Clientseite. Das Back-End protokolliert die Abfragemetriken und stellt sie für das Cosmos DB SDK bereit.

Die Methode `ResponseDiagnosticsProcessor.processResponseDiagnostics` wird nach jedem API-Aufruf im Spring Data Cosmos DB SDK aufgerufen. Daher ist es wichtig, dass Ihre Implementierung fehlerfrei und einfach ist, um eine hohe Leistung zu erzielen. Es wird beispielsweise empfohlen, nicht die vollständigen Diagnoseinformationen in dieser Methode zu protokollieren, da die Menge an Informationen zu erheblichen Leistungseinbußen führen würde. Darüber hinaus sollten Sie den Protokolliergrad `Debug` verwenden, um Auswirkungen auf die Anwendungsleistung zu vermeiden.

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

### <a name="api-exceptions"></a>API-Ausnahmen

Version 2.2.1 des Spring Data Cosmos DB SDK bietet die folgenden Verbesserungen für die Ausnahmebehandlung:

- Alle APIs lösen `CosmosDBAccessException` aus. Dadurch wird über einen Getter ein Feld vom Typ `cosmosClientException` verfügbar macht.
- Das Cosmos DB SDK löst `CosmosClientException` aus. Damit können Sie eine beliebige Wiederholungslogik auf der Clientseite implementieren.
- Allgemeine Ausnahmen bei der Wiederholung enthalten die Meldungen `Resource already exists`, `Request rate too large`, `Request timeout exception` usw.

### <a name="api-or-query-slowness"></a>API oder langsame Abfragen

Wenn bei APIs oder Abfrageausführungen lange Wartezeiten auftreten, versuchen Sie, die Diagnosezeichenfolgen und Abfragemetriken zu protokollieren, wie im Abschnitt [Aktivieren von Diagnose und Abfragemetriken](#enable-diagnostics-and-query-metrics) beschrieben. Überprüfen Sie CPU-Auslastung, Netzwerkbandbreite und E/A-Speicherplatz, da diese eine langsame Ausführung auf der Clientseite verursachen können.
