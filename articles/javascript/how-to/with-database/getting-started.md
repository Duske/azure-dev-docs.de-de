---
title: Erste Schritte mit Azure-Datenbanken
description: Hier finden Sie Informationen zu den allgemeinen Aufgaben für die Verwendung einer beliebigen in Azure gehosteten Datenbank.
ms.topic: how-to
ms.date: 02/09/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5d104dcbfe6aa89db6ee434fafcb2f4796de1b18
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100424200"
---
# <a name="getting-started-with-databases-on-azure"></a>Erste Schritte mit Datenbanken in Azure

Die Azure-Cloudplattform ermöglicht es Ihnen, alle Azure-Datenbanken (als Dienste) oder eine eigene Datenbank zu verwenden. Nach der Einrichtung des Servers und der Datenbank müssen mit dem vorhandenen Code nur die Verbindungseinstellungen geändert werden. 

Wenn Sie eine Datenbank in Azure nutzen, müssen Sie mehrere allgemeine Aufgaben ausführen, um die Datenbank über Ihre JavaScript-App zu verwenden. Erfahren Sie mehr über das Abrufen und Verwenden Ihrer Datenbank in Azure. 

## <a name="select-a-database-to-use-on-azure"></a>Auswählen einer Datenbank für die Verwendung in Azure

Microsoft stellt verwaltete Dienste für die folgenden Datenbanken bereit:

|Datenbank|Azure-Dienst|Weitere Informationen|
|--|--|--|
|Cassandra|[Azure Cosmos DB](/azure/cosmos-db/)|[Schnellstart: Erstellen einer Cassandra-App mit dem Node.js-SDK und Azure Cosmos DB](/azure/cosmos-db/create-cassandra-nodejs)|
|Gremlin|[Azure Cosmos DB](/azure/cosmos-db/)|[Schnellstart: Erstellen einer Node.js-Anwendung mithilfe eines Gremlin-API-Kontos für Azure Cosmos DB](/azure/cosmos-db/create-graph-nodejs)|
|MongoDB|[Azure Cosmos DB](/azure/cosmos-db/)|[Entwickeln einer JavaScript-Anwendung mit MongoDB in Azure](use-mongodb-as-cosmosdb.md)<br>[Schnellstart: Migrieren einer vorhandenen MongoDB-Node.js-Web-App zu Azure Cosmos DB](/azure/cosmos-db/create-mongodb-nodejs)|
|MariaDB|[Azure Database for MariaDB](/azure/mariadb/)|[Entwickeln einer JavaScript-Anwendung mit MariaDB in Azure](use-mariadb.md)|
|MySQL|[Azure Database for MySQL](/azure/mysql/)|[Schnellstart: Verwenden von Node.js zum Herstellen von Verbindungen mit Daten und Abfragen von Daten in Azure Database for MySQL](/azure/mysql/connect-nodejs)<br>[Entwickeln einer JavaScript-Anwendung mit MySQL in Azure](use-mysql-db.md)|
|PostgreSQL|[Azure-Datenbank für PostgreSQL](/azure/postgresql/)|[Schnellstart: Verwenden von Node.js zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver und zum Abfragen von Daten](/azure/postgresql/connect-nodejs)<br>[Entwickeln einer JavaScript-Anwendung mit PostgreSQL in Azure](use-postgresql-db.md)|
|Redis|[Azure Cache for Redis](/azure/azure-cache-for-redis/)|[Schnellstart: Verwenden von Azure Cache for Redis mit Node.js](/azure/azure-cache-for-redis/cache-nodejs-get-started)|
|SQL|[Azure Cosmos DB](/azure/cosmos-db/)|[Schnellstart: Verwenden von Node.js zum Herstellen einer Verbindung mit einem und Abfragen von Daten aus einem Azure Cosmos DB-SQL-API-Konto](/azure/cosmos-db/create-sql-api-nodejs)|
|Tabellen|[Azure Cosmos DB](/azure/cosmos-db/)|[Verwenden von Azure Table Storage oder der Azure Cosmos DB-Tabellen-API über Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs)|

**Benötigen Sie Hilfe bei der Auswahl?** 
* Wählen Sie die Datenbank basierend auf den [gewünschten Aktionen](https://azure.microsoft.com/product-categories/databases/) aus:
* Verwenden Sie [Azure Database Migration Service](/azure/dms/), um zu Azure zu wechseln. 

**Sie haben Ihre Datenbank nicht gefunden?**
Stellen Sie Ihre Datenbank entweder als Container oder als virtuellen Computer bereit. Sie können einen beliebigen Datenbanktyp mit diesen Diensten bereitstellen und von Hochverfügbarkeit und Sicherheit für Ihre anderen Azure-Ressourcen profitieren. Der Nachteil besteht darin, dass Sie die Infrastruktur (Container oder VM) selbst verwalten müssen. Die restlichen Informationen in diesem Dokument sind zwar in erster Linie für die Verwendung eines Azure-Datenbankdiensts vorgesehen, können aber ggf. auch für Container oder virtuelle Computer hilfreich sein. 

## <a name="create-the-server"></a>Erstellen des Servers

Die Erstellung eines Servers wird abgeschlossen, indem eine Ressource für den jeweiligen Azure-Dienst in Ihrem Abonnement erstellt wird, in dem Ihre Datenbank gehostet wird. 

Eine Ressource wird wie folgt erstellt:

|Tool|Zweck|
|--|--|
|Azure-Portal|Für die erste oder eine selten erstellte Datenbank wird das Azure-Portal verwendet.|
|Azure CLI|Wird für wiederholbare/skriptfähige Szenarien verwendet.|
|Visual Studio Code-Erweiterung (für diesen Dienst)|Diese Erweiterung wird verwendet, um die integrierte Entwicklungsumgebung nicht verlassen zu müssen.|
|npm-ARM-Bibliothek (für diesen Dienst)|Diese Bibliothek wird verwendet, um weiterhin JavaScript verwenden zu können.| 

Nach der Erstellung des Servers müssen Sie je nach Dienst möglicherweise noch folgende Schritte ausführen:

* Konfigurieren von Sicherheitseinstellungen wie Firewall und SSL-Erzwingung
* Abrufen der Verbindungsinformationen
* Erstellen der Datenbank

## <a name="configure-security-settings-for-your-database"></a>Konfigurieren der Sicherheitseinstellungen für Ihre Datenbank

Für den Dienst müssen u. a. die folgenden allgemeinen Sicherheitseinstellungen konfiguriert werden:

* Öffnen der Firewall für Ihre Client-IP-Adresse
* Konfigurieren der SSL-Erzwingung
* Akzeptieren öffentlicher Anforderungen oder Voraussetzen, dass alle Anforderungen aus einem anderen Azure-Dienst stammen

## <a name="create-a-database-on-the-azure-server"></a>Erstellen einer Datenbank auf dem Azure-Server

Sie können Ihre Verbindungsinformationen mit demselben Tool abrufen, mit dem Sie auch den Server erstellt haben. Verwenden Sie die Verbindungsinformationen, um auf den Server zuzugreifen. Sie müssen noch die für Ihre Anwendung spezifische Datenbank erstellen. 

Greifen Sie auf Ihren Server zu: 

* Verwenden Sie ein für diesen Datenbanktyp spezifisches Tool, z. B. pgAdmin, SQL Server Management Studio und MySQL Workbench. 
* Verwenden Sie weiterhin Microsoft-Tools.
    * [Azure Cloud Shell](https://shell.azure.com) umfasst viele Datenbank-CLIs, etwa psql und mysql.
    * Visual Studio Code-Erweiterungen
    * npm-Pakete für JavaScript
    * Azure-Portal

## <a name="programmatically-access-the-server-and-database-with-javascript"></a>Programmgesteuertes Zugreifen auf den Server und die Datenbank mit JavaScript

Wenn Sie über die Verbindungsinformationen verfügen, können Sie mit branchenüblichen npm-Paketen und JavaScript auf Ihren Server zugreifen. 

Nach dem Erstellen oder Migrieren einer Datenbank müssen nur die Verbindungsinformationen für den neuen Server und die Datenbank geändert werden. 

## <a name="configure-an-azure-web-apps-connection-to-database"></a>Konfigurieren der Verbindung einer Azure-Web-App mit der Datenbank

Wenn Ihre Azure-Web-App eine Verbindung mit der Datenbank herstellt, müssen Sie die App-Einstellung für die Verbindungsinformationen ändern. 

## <a name="next-steps"></a>Nächste Schritte

* [Entwickeln einer JavaScript-Anwendung mit MongoDB in Azure](use-mongodb-as-cosmosdb.md)