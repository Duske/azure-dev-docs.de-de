---
title: Datenbanken mit Node.js-Apps in Azure
description: Azure bietet eine Vielzahl verschiedener Datenbanken für die Verwendung mit Web- und anderen Node.js-Apps an.
ms.topic: how-to
ms.date: 12/08/2020
ms.custom: devx-track-js
ms.openlocfilehash: b39a7d3e39600081148893a68d3dbc064c1db380
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561686"
---
# <a name="integrate-databases-in-nodejs-apps"></a>Integrieren von Datenbanken in Node.js-Apps

Azure-Datenbanken stellen eine hervorragende verwaltete Clouddatenlösung zusammen mit einer nativen API oder einem Azure SDK bereit, um eine Verbindung mit der Datenbank herzustellen. 

## <a name="database-and-data-storage-solutions-on-azure"></a>Datenbank- und Datenspeicherlösungen in Azure

Die folgende Tabelle enthält Links zu verschiedenen Artikeln zum Herstellen einer Verbindung mit und zur Verwendung von Azure-Datenbanken mit Node.js. Eine Liste zur Gegenüberstellung der verschiedenen Datenbankoptionen finden Sie unter [Datenbanken: Vollständig verwaltete intelligente Datenbankdienste](https://azure.microsoft.com/product-categories/databases/).

| Dienst | Schnellstart | npm-Paket |
| --- | --- | --- |
| **SQL Server** in Cosmos DB| [Erstellen einer Azure Cosmos DB-Web-App für Node.js mit SQL Server](/azure/cosmos-db/create-sql-api-nodejs) | [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos) |
| **MongoDB** in Cosmos DB| [Erstellen einer Node.js- und MongoDB-Web-App](/azure/app-service-web/app-service-web-tutorial-nodejs-mongodb-app) | beliebiger MongoDB-Client |
| **Cassandra** in Cosmos DB|[Erstellen einer Cassandra-App mit dem Node.js-SDK und Azure Cosmos DB](/azure/cosmos-db/create-cassandra-nodejs)|[npm cassandra-driver](https://www.npmjs.com/package/cassandra-driver)|
| **Gremlin** in Cosmos DB|[Erstellen einer Node.js-Anwendung mithilfe eines Gremlin-API-Kontos für Azure Cosmos DB](/azure/cosmos-db/create-graph-nodejs)|[npm gremlin](https://www.npmjs.com/package/gremlin)|
| **Redis Cache** in Cosmos DB| [Erstellen und Nutzen eines Redis-Cache](/azure/redis-cache/cache-nodejs-get-started) | [npm redis](https://www.npmjs.com/package/redis)|
| **Azure SQL-Datenbank** | [Abfragen einer Azure SQL-Datenbank mithilfe von Node.js](/azure/sql-database/sql-database-connect-query-nodejs) |[npm tedious](https://www.npmjs.com/package/tedious) |
| **MySQL** | [Verwenden von Node.js zum Herstellen einer Verbindung und Abfragen von Daten](/azure/mysql/connect-nodejs) | [npm mysql](https://www.npmjs.com/package/mysql)|
| **PostgreSQL** | [Verwenden von Node.js zum Herstellen einer Verbindung und Abfragen von Daten](/azure/postgresql/connect-nodejs) |[npm pg](https://www.npmjs.com/package/pg) |

## <a name="cosmos-db-connection-strings-with-azure-cli"></a>Cosmos DB-Verbindungszeichenfolgen mit der Azure CLI

Verwenden Sie den folgenden Befehl [az cosmosdb keys list](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings):

```azurecli-interactive
az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="sql-connection-strings-with-azure-cli"></a>SQL-Verbindungszeichenfolgen mit der Azure CLI

Verwenden Sie den folgenden Befehl [az sql db show-connection-string](/cli/azure/sql/db#az_sql_db_show_connection_string):

```azurecli-interactive
az sql db show-connection-string \
    --client {ado.net, jdbc, odbc, php, php_pdo, sqlcmd} \
     [--auth-type {ADIntegrated, ADPassword, SqlPassword}] \
     [--ids] \
     [--name] \
     [--server] \
     [--subscription]
```

## <a name="mysql-username-and-password-with-azure-cli"></a>MySQL-Benutzername und -Kennwort mit der Azure CLI

Diese werden zum [Zeitpunkt der Ressourcenerstellung](/cli/azure/mysql/server#az_mysql_server_create) festgelegt. 

## <a name="postgresql-username-and-password-with-azure-cli"></a>PostgreSQL-Benutzername und -Kennwort mit der Azure CLI

Diese werden zum [Zeitpunkt der Ressourcenerstellung](/cli/azure/postgres/server#az_postgres_server_create) festgelegt. 

## <a name="azure-storage-solutions-for-files-and-data"></a>Azure Storage-Lösungen für Dateien und Daten

Sie können auch Azure Storage zum Speichern von Dateien (Blobs), Tabellen und Warteschlangen (Nachrichten) verwenden:

| Dienst | Schnellstart |Empfohlenes SDK |
| --- | --- |--- |
| **Blobs** | [Hochladen, Herunterladen, Auflisten und Löschen von Blobs mit Azure Storage v10 SDK für JavaScript](/azure/storage/blobs/storage-quickstart-blobs-nodejs-v10) |[@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob)|
| **Warteschlangen** | [How to use Queue storage from Node.js (Verwenden von Queue Storage mit Node.js)](/azure/storage/queues/storage-nodejs-how-to-use-queues) |[npm @azure/storage-queue](https://www.npmjs.com/package/@azure/storage-queue)|
| **Tabellen** | [Gewusst wie: Verwenden von Table Storage mit Node.js](/azure/cosmos-db/table-storage-how-to-use-nodejs) |[npm azure-storage](https://www.npmjs.com/package/azure-storage)|

## <a name="next-steps"></a>Nächste Schritte

* [Schreiben von serverlosem Code](develop-serverless-apps.md) mit Azure Functions