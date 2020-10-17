---
title: Verwenden von Spring Data Gremlin Starter mit der SQL-API von Azure Cosmos DB
description: Hier erfahren Sie, wie eine mit Spring Boot Initializr erstellte Anwendung mit der SQL-API von Azure Cosmos DB konfiguriert wird.
services: cosmos-db
documentationcenter: java
ms.date: 08/03/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: b1fe15704e325316febc405289df27be1cfea37c
ms.sourcegitcommit: 723441eda0eb4ff893123201a9e029b7becf5ecc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91846581"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Verwenden von Spring Data Gremlin Starter mit der SQL-API von Azure Cosmos DB

## <a name="overview"></a>Übersicht

Spring Data Gremlin Starter bietet Spring-Datenunterstützung für die Gremlin-Abfragesprache von Apache, die Entwickler für jeden beliebigen, mit Gremlin kompatiblen Datenspeicher verwenden können.

Dieser Artikel zeigt, wie über das Azure-Portal eine Azure Cosmos DB-Instanz für die Verwendung mit der Gremlin-API und dann mithilfe von **[Spring Initializr]** eine benutzerdefinierte Java-Anwendung erstellt wird. Anschließend wird veranschaulicht, wie Spring Data Gremlin Starter-Funktionen zu Ihrer benutzerdefinierten Anwendung hinzugefügt werden, um Daten mithilfe von Gremlin in Ihrer Azure Cosmos DB-Instanz zu speichern und daraus abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher


## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Erstellen eines Cosmos DB-Kontos über das Azure-Portal

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und wählen Sie `+Create a resource` aus.

   >[!div class="mx-imgBorder"]
   >![Ressource erstellen][create-a-resource-01]

1. Wählen Sie `Databases` und dann `Azure Cosmos DB` aus.

   >[!div class="mx-imgBorder"]
   >![Azure Cosmos DB-Datenbank erstellen][create-a-resource-02]

1. Geben Sie auf der Seite `Azure Cosmos DB` die folgenden Informationen ein:

   * Wählen Sie das `Subscription` aus, das Sie für Ihre Datenbank verwenden möchten.
   * Legen Sie fest, ob eine neue `Resource Group` für Ihre Datenbank erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   * Geben Sie einen eindeutigen Wert für `Account Name` ein, der als Teil des Gremlin-URI für Ihre Datenbank verwendet wird. Beispiel: Wenn Sie `account-sample` für `Account Name` eingeben, lautet der Gremlin-URI `account-samplewingtiptoysdata.gremlin.cosmosdb.azure.com`.
   * Wählen Sie für die API `Gremlin (Graph)` aus.
   * Geben Sie den `Location` für Ihre Datenbank an.
   
1. Wählen Sie nach Angabe dieser Optionen die Option `Review + create` aus.

   >[!div class="mx-imgBorder"]
   >![Azure Cosmos DB-Konto erstellen][create-a-resource-03]

1. Überprüfen Sie die Angaben, und wählen Sie anschließend `Create` aus, um Ihre Datenbank zu erstellen.

1. Wählen Sie nach der Erstellung der Datenbank die Option **Zu Ressource wechseln** aus. Sie ist auch in Ihrem Azure-**Dashboard** sowie auf den Seiten **Alle Ressourcen** und **Azure Cosmos DB** aufgeführt. Sie können Ihre Datenbank an all diesen Orten auswählen, um die Eigenschaftenseite für Ihren Cache zu öffnen.

1. Gehen Sie wie folgt vor, wenn die Eigenschaftenseite für Ihre Datenbank angezeigt wird: Wählen Sie **Schlüssel** aus, und kopieren Sie Ihren URI sowie Ihre Zugriffsschlüssel für die Datenbank. Diese Werte benötigen Sie in Ihrer Spring Boot-Anwendung.

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Hinzufügen eines Diagramms zu Ihrer Azure-Cosmos-Datenbank

1. Wählen Sie auf der Cosmos DB-Seite die Option `Data Explorer` und dann `New Graph` aus.

   >[!div class="mx-imgBorder"]
   >![Neuer Graph][create-a-graph-01]

1. Wenn `Add Graph` angezeigt wird, geben Sie folgende Informationen ein:

   * Geben Sie eine eindeutige `Database id` für Ihre Datenbank an.
   * Sie können Ihre `Storage capacity` angeben oder die Standardeinstellung übernehmen.
   * Geben Sie eine eindeutige `Graph id` für Ihre Datenbank an.
   * Geben Sie einen `Partition key` an. Weitere Informationen finden Sie unter [Graphpartitionierung]. Wählen Sie `OK`aus.
   
   Wählen Sie nach Angabe dieser Optionen `OK` aus, um den Graphen zu erstellen.

   >[!div class="mx-imgBorder"]
   >![Graph hinzufügen][create-a-graph-02]

1. Der erstellte Graph kann im `Data Explorer` angezeigt werden.

   >[!div class="mx-imgBorder"]
   >![Graphdetails][create-a-graph-03]
   
   

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer einfachen Spring Boot-Anwendung mit Spring Initializr

1. Navigieren Sie zu <https://start.spring.io/>.

1. Geben Sie an, dass Sie ein **Maven**-Projekt mit **Java** generieren möchten, geben Sie die Namen für **Group** (Gruppe) und **Artifact** (Artefakt) für Ihre Anwendung ein, und geben Sie Version 2.3.1. als **Spring Boot**-Version an. Wählen Sie anschließend **GENERATE** (GENERIEREN) aus.

> [!NOTE]
>
> Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für **Gruppe** und **Artefakt** (also beispielsweise `com.example.wintiptoysdata`).


   >[!div class="mx-imgBorder"]
   >![Spring Initializr][spring-initializr-01]

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

1. Nachdem Sie die Dateien auf Ihrem lokalen System extrahiert haben, können Sie den Import in Ihre IDE durchführen.


## <a name="configure-your-spring-boot-app-to-use-the-spring-data-gremlin-starter"></a>Konfigurieren Ihrer Spring Boot-App für die Verwendung von Spring Data Gremlin Starter

Wir replizieren die Konfigurationen des vorhandenen [Azure Spring Data-Gremlin-Beispiels](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin). Navigieren Sie zum Beispiel, und führen Sie die in diesem Abschnitt angegebenen Schritte aus, um Ihre Spring Boot-App zu konfigurieren.

1. Suchen Sie die Datei *pom.xml* im Verzeichnis Ihrer App. Beispiel:

   *C:\SpringBoot\wingtiptoysdata\pom.xml*

   - oder -

   */users/example/home/wingtiptoysdata/pom.xml*

1. Öffnen Sie die Datei *pom.xml*, und fügen Sie Spring Data Gremlin Starter der Liste mit den Abhängigkeiten (`<dependencies>`) hinzu:

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-gremlin</artifactId>
      <version>2.3.1-beta.1</version> <!-- {x-version-update;com.azure:azure-spring-data-gremlin;current} -->
    </dependency>
   ```

1. Speichern und schließen Sie die Datei *pom.xml*.

1. Navigieren Sie zum Ordner *src/test/* , und löschen Sie seinen gesamten Inhalt.

1. Navigieren Sie in der Beispiel-App zum Ordner *src/main/java*, und kopieren und überschreiben Sie dieses Verzeichnis in Ihrer lokalen Spring Boot-App.

1. Aktualisieren Sie die Konfigurationen in der Datei *src/main/resources/application.properties* so, dass sie Folgendes enthalten:

   | Feld              | BESCHREIBUNG                                                                                                                                                                                                             |
   |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `endpoint`         | Gibt den Gremlin-URI für Ihre Datenbank an. Dieser wird von der eindeutigen **ID** abgeleitet, die Sie zuvor in dieser Schnellstartanleitung beim Erstellen Ihrer Azure Cosmos DB-Instanz angegeben haben.                                                 |
   | `port`             | Gibt den TCP/IP-Port an (**443** für HTTPS).                                                                                                                                                           |
   | `username`         | Gibt die eindeutige **Datenbank-ID** und **Graph-ID** an, die Sie zuvor in dieser Schnellstartanleitung beim Hinzufügen Ihres Graphen verwendet haben (Syntax: /dbs/ **{Datenbank-ID}** /colls/ **{Graph-ID}** ). |
   | `password`         | Gibt den primären oder sekundären **Zugriffsschlüssel** an, den Sie zuvor in dieser Schnellstartanleitung kopiert haben.                                                                                                                      |
   | `sslEnabled`       | Gibt an, ob Secure Sockets Layer (SSL) aktiviert werden soll.                                                                                                                                                                                           |
   | `telemetryAllowed` | Geben Sie **true** an, wenn Sie Telemetriedaten aktivieren möchten (oder **false**, falls nicht).
   | `maxContentLength` | Gibt die maximale Inhaltslänge an.                                                                                                                                                                                           |

## <a name="build-and-run-the-project"></a>Erstellen und Ausführen des Projekts

1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Wenn Ihre App erfolgreich gestartet wird, können Sie den Graphen im Azure-Portal überprüfen:

   >[!div class="mx-imgBorder"]
   >![Ergebnis der Ausführung][execute-result-01]


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring in Azure finden Sie in der Dokumentation zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Azure-Unterstützung für Gremlin und Graph-API finden Sie in den folgenden Artikeln:

* [Einführung in die Graph-API von Azure Cosmos DB](/azure/cosmos-db/graph-introduction)

* [Unterstützung für Gremlin-Diagramme in Azure Cosmos DB](/azure/cosmos-db/gremlin-support)

* [Azure Cosmos DB: Erstellen einer Graphdatenbank mit Java und dem Azure-Portal](/azure/cosmos-db/create-graph-java)

* [Tutorial: Abfragen der Graph-API von Azure Cosmos DB mithilfe von Gremlin](/azure/cosmos-db/tutorial-query-graph)

* [Spring Data Gremlin Starter]

Weitere Informationen zur Verwendung von Azure Cosmos DB und Java finden Sie in den folgenden Artikeln:

* [Dokumentation für Azure Cosmos DB]

* [Azure Cosmos DB: Erstellen einer Dokumentdatenbank mit Java und dem Azure-Portal][Build a SQL API app with Java]

* [Spring-Daten für SQL-API von Azure Cosmos DB]

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Bereitstellen einer Spring Boot-Anwendung für Linux in Azure App Service](deploy-spring-boot-java-app-on-linux.md)

* [Ausführen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[Dokumentation für Azure Cosmos DB]: /azure/cosmos-db/
[Azure für Java-Entwickler]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring-Daten für SQL-API von Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Data Gremlin Starter]: https://github.com/Microsoft/spring-data-gremlin
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Graphpartitionierung]: /azure/cosmos-db/graph-partitioning
[azure-spring-data-sample-gremlin]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin

<!-- IMG List -->

[create-a-resource-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-01.png
[create-a-resource-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-02.png
[create-a-resource-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-03.png

[create-a-graph-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-01.png
[create-a-graph-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-02.png
[create-a-graph-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-03.png

[spring-initializr-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/spring-initializr-01.png

[get-password-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/get-password-01.png

[execute-result-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/execute-result-01.png