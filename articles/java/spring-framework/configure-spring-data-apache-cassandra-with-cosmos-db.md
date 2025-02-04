---
title: Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB
description: Erfahren Sie, wie Sie die Spring Data-Apache Cassandra-API mit Azure Cosmos DB verwenden.
services: cosmos-db
documentationcenter: java
ms.date: 10/13/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 7879a47bdcbc9b1a4cf41210fc9fb49ad28d8dd8
ms.sourcegitcommit: 8e1d3a384ccb0e083589418d65a70b3a01afebff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560322"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB

In diesem Artikel wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data] verwendet, um Informationen mithilfe der [Cassandra-API für Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction) zu speichern und abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher
* [cURL](https://curl.haxx.se/) oder ein ähnliches HTTP-Hilfsprogramm zum Testen der Funktionalität
* Einen [Git-Client](https://git-scm.com/downloads)

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Mit dem folgenden Verfahren wird ein Cosmos-Konto im Azure-Portal erstellt und konfiguriert:

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Erstellen eines Cosmos DB-Kontos über das Azure-Portal

> [!NOTE]
> 
> Ausführlichere Informationen zum Erstellen von Azure Cosmos DB-Konten finden Sie in der [Dokumentation für Azure Cosmos DB](/azure/cosmos-db/).

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **Ressource erstellen** > **Erste Schritte** > **Azure Cosmos DB** aus.
    
   >[!div class="mx-imgBorder"]
   >![Erstellen eines Azure Cosmos DB-Kontos][COSMOSDB01]

1. Geben Sie die folgenden Informationen an:

   - **Abonnement**: Geben Sie das Azure-Abonnement an, das Sie verwenden möchten.
   - **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   - **Kontoname**: Wählen Sie einen eindeutigen Namen für Ihr Cosmos DB-Konto aus. Dieser Name wird zum Erstellen eines vollqualifizierten Domänennamens (z. B. *wingtiptoyscassandra.documents.azure.com*) verwendet.
   - **API**: Geben Sie für dieses Tutorial *Cassandra* an.
   - **Standort**: Geben Sie die nächstgelegene geografische Region für Ihre Datenbank an.
   
   >[!div class="mx-imgBorder"]
   >![Festlegen der Cosmos DB-Kontoeinstellungen][COSMOSDB02]
   
1. Nachdem Sie alle oben genannten Informationen eingegeben haben, klicken Sie auf **Bewerten + erstellen**.

1. Wenn die Angaben auf der Seite „Überprüfen“ korrekt sind, klicken Sie auf **Erstellen**.
   
   >[!div class="mx-imgBorder"]
   >![Überprüfen der Cosmos DB-Kontoeinstellungen][COSMOSDB03]

Die Bereitstellung der Datenbank dauert einige Minuten.

### <a name="add-a-keyspace-to-your-azure-cosmos-db-account"></a>Hinzufügen eines Keyspace zu Ihrem Azure Cosmos DB-Konto

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **Alle Ressourcen** und anschließend das Azure Cosmos DB-Konto aus, das Sie gerade erstellt haben.

1. Wählen Sie **Daten-Explorer**, den Abwärtspfeil und anschließend **New Keyspace** (Neuer Keyspace) aus. Geben Sie einen eindeutigen Bezeichner für **Keyspace id** (Keyspace-ID) ein, und wählen Sie dann **OK** aus.
    
   >[!div class="mx-imgBorder"]
   >![Auswählen von „New Keyspace“ (Neuer Keyspace)][COSMOSDB05]
   
   >[!div class="mx-imgBorder"]
   >![Erstellen eines Cosmos DB-Keyspace][COSMOSDB05-1]

### <a name="retrieve-the-connection-settings-for-your-azure-cosmos-db-account"></a>Abrufen der Verbindungseinstellungen für das Azure Cosmos DB-Konto

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **Alle Ressourcen** und anschließend das Azure Cosmos DB-Konto aus, das Sie gerade erstellt haben.

1. Wählen Sie **Verbindungszeichenfolgen** aus, und kopieren Sie die Werte für **Kontaktpunkt**, **Port**, **Benutzername** und **Primäres Kennwort**. Sie werden später zum Konfigurieren Ihrer Anwendung benötigt.
   
   >[!div class="mx-imgBorder"]
   >![Abrufen der Cosmos DB-Verbindungseinstellungen][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

Mit dem folgenden Verfahren wird die Testanwendung konfiguriert:

1. Öffnen Sie eine Befehlsshell, und klonen Sie das Beispielprojekt wie im folgenden Beispiel gezeigt mit einem Git-Befehl:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure.git
   ```

1. Suchen Sie im Verzeichnis *resources* des Beispielprojekts nach der Datei *application.properties*, oder erstellen Sie diese Datei, wenn sie noch nicht vorhanden ist.

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie die folgenden Zeilen in der Datei hinzu, bzw. konfigurieren Sie diese Zeilen. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Werte, die Sie zuvor festgelegt haben:

   ```yaml
   spring.data.cassandra.contact-points=wingtiptoyscassandra.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=wingtiptoyscassandra
   spring.data.cassandra.password=********
   ```
   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `spring.data.cassandra.contact-points` | Der **Kontaktpunkt**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.port` | Der **Port**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.username` | Der **Benutzername**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.password` | Das **primäre Kennwort**, das Sie weiter oben in diesem Artikel kopiert haben |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Verpacken und Testen der Beispielanwendung 

Navigieren Sie zum Verzeichnis mit der POM-Datei, um die Anwendung zu erstellen und zu testen.

1. Erstellen Sie die Beispielanwendung mit Maven. Beispiel:

   ```shell
   mvn clean package
   ```

1. Starten Sie die Beispielanwendung. Beispiel:

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Erstellen Sie wie in den folgenden Beispielen dargestellt über eine Eingabeaufforderung mit `curl` neue Datensätze:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Rufen Sie wie im folgenden Beispiel dargestellt über eine Eingabeaufforderung mit `curl` alle vorhandenen Datensätze ab:

   ```shell
   curl -s http://localhost:8080/pets
   ```

   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie eine Java-Beispielanwendung erstellt, die Spring Data verwendet, um Informationen mithilfe der Cassandra-API für Azure Cosmos DB zu speichern und abzurufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

<!-- URL List -->

[Azure für Java-Entwickler]: ../index.yml
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB05]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-05.png
[COSMOSDB05-1]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-05-1.png
[COSMOSDB06]: media/configure-spring-data-apache-cassandra-with-cosmos-db/create-cosmos-db-06.png