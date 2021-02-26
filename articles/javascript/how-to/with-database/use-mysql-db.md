---
title: Verwenden von JavaScript in Azure MySQL
description: Sie benötigen eine MySQL-Ressource, um Ihre MySQL-Datenbank in Azure zu erstellen oder zu verschieben.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: caab884c0a943f00ccc1701a7d364bb0825bce78
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004992"
---
# <a name="develop-a-javascript-application-with-mysql-on-azure"></a>Entwickeln einer JavaScript-Anwendung mit MySQL in Azure

Sie benötigen eine **Azure Database for MySQL**-Ressource, um eine MySQL-Datenbank in Azure zu erstellen, zu verschieben oder zu verwenden. Hier erfahren Sie, wie Sie die Ressource erstellen und Ihre Datenbank verwenden.

## <a name="create-an-azure-database-for-mysql-resource"></a>Erstellen einer Azure Database for MySQL-Ressource 

Zum Erstellen einer Ressource können Sie Folgendes verwenden:

* Azure CLI
* [Azure portal](https://ms.portal.azure.com/#create/Microsoft.MySQLServer)
* [@azure/arm-mysql](https://www.npmjs.com/package/@azure/arm-mysql)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mysql-db.md)]

## <a name="view-and-use-your-mysql-on-azure"></a>Anzeigen und Verwenden von MySQL in Azure
Verwenden Sie beim Entwickeln Ihrer MySQL-Datenbank mit JavaScript eines der folgenden Tools:

* _mysql_-Befehlszeilenschnittstelle von [Azure Cloud Shell](https://shell.azure.com/)
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* Visual Studio Code-[Erweiterung](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql)

## <a name="use-sdk-packages-to-develop-your-mysql-on-azure"></a>Verwenden von SDK-Paketen zum Entwickeln Ihrer MySQL-Instanz in Azure

Azure MySQL verwendet bereits verfügbare npm-Pakete, etwa:

* [MySQL](https://www.npmjs.com/package/MySQL)
* [Promise-mysql](https://www.npmjs.com/package/promise-mysql)

## <a name="use-promise-mysql-sdk-to-connect-to-mysql-on-azure"></a>Verwenden des Promise-mysql SDK zum Herstellen einer Verbindung mit MySQL in Azure

Gehen Sie wie folgt vor, um mit JavaScript eine Verbindung mit Ihrer MySQL-Instanz in Azure herzustellen und diese zu verwenden.

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir MySQLDemo && \
        cd MySQLDemo && \
        npm init -y && \
        npm install promise-mysql && \
        touch index.js && \
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `MySQLDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Installieren des npm-Pakets „promise-mysql“ für die Verwendung von async/await
    * Erstellen der Skriptdatei `index.js`
    * Öffnen des Projekts in Visual Studio Code

1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install promise-mysql

    // get MySQL SDK
    const MySQL = require('promise-mysql');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await MySQL.createConnection(config);

      // show databases on server
      const databases = await connection.query('SHOW DATABASES;');
      console.log(databases);

      // show tables in the mysql database
      const tables = await connection.query('SHOW TABLES FROM mysql;');
      console.log(tables);

      // show users configured for the server
      const rows = await connection.query('select User from mysql.user;');
      console.log(rows);

      // close connection
      connection.end();
    };

    const config = {
      host: 'YOUR-RESOURCE_NAME.mysql.database.azure.com',
      user: 'YOUR-ADMIN-NAME@YOUR-RESOURCE_NAME',
      password: 'YOUR-ADMIN-PASSWORD',
      port: 3306,
    };

    query(config)
      .then(() => console.log('done'))
      .catch((err) => console.log(err));
    ```

1. Ersetzen Sie den Host, den Benutzer und das Kennwort durch Ihre Werte im Skript für das Verbindungskonfigurationsobjekt `config`. 

1. Führen Sie das Skript aus.

    ```bash
    [
      RowDataPacket { Database: 'information_schema' },
      RowDataPacket { Database: 'defaultdb' },
      RowDataPacket { Database: 'dbproducts' },
      RowDataPacket { Database: 'mysql' },
      RowDataPacket { Database: 'performance_schema' },
      RowDataPacket { Database: 'sys' }
    ]
    [
      RowDataPacket { Tables_in_mysql: '__az_action_history__' },
      RowDataPacket { Tables_in_mysql: '__az_changed_static_configs__' },
      RowDataPacket { Tables_in_mysql: '__az_replica_information__' },
      RowDataPacket { Tables_in_mysql: '__az_replication_current_state__' },
      RowDataPacket { Tables_in_mysql: '__firewall_rules__' },
      RowDataPacket { Tables_in_mysql: '__querystore_event_wait__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_metrics__' },
      RowDataPacket { Tables_in_mysql: '__querystore_query_text__' },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_errors__'
      },
      RowDataPacket {
        Tables_in_mysql: '__querystore_wait_stats_procedure_status__'
      },
      RowDataPacket { Tables_in_mysql: '__recommendation__' },
      RowDataPacket { Tables_in_mysql: '__recommendation_session__' },
      RowDataPacket { Tables_in_mysql: '__script_version__' },
      RowDataPacket { Tables_in_mysql: 'columns_priv' },
      RowDataPacket { Tables_in_mysql: 'db' },
      RowDataPacket { Tables_in_mysql: 'engine_cost' },
      RowDataPacket { Tables_in_mysql: 'event' },
      RowDataPacket { Tables_in_mysql: 'func' },
      RowDataPacket { Tables_in_mysql: 'general_log' },
      RowDataPacket { Tables_in_mysql: 'gtid_executed' },
      RowDataPacket { Tables_in_mysql: 'help_category' },
      RowDataPacket { Tables_in_mysql: 'help_keyword' },
      RowDataPacket { Tables_in_mysql: 'help_relation' },
      RowDataPacket { Tables_in_mysql: 'help_topic' },
      RowDataPacket { Tables_in_mysql: 'innodb_index_stats' },
      RowDataPacket { Tables_in_mysql: 'innodb_table_stats' },
      RowDataPacket { Tables_in_mysql: 'ndb_binlog_index' },
      RowDataPacket { Tables_in_mysql: 'plugin' },
      RowDataPacket { Tables_in_mysql: 'proc' },
      RowDataPacket { Tables_in_mysql: 'procs_priv' },
      RowDataPacket { Tables_in_mysql: 'proxies_priv' },
      RowDataPacket { Tables_in_mysql: 'query_store' },
      RowDataPacket { Tables_in_mysql: 'query_store_wait_stats' },
      RowDataPacket { Tables_in_mysql: 'recommendation' },
      RowDataPacket { Tables_in_mysql: 'server_cost' },
      RowDataPacket { Tables_in_mysql: 'servers' },
      RowDataPacket { Tables_in_mysql: 'slave_master_info' },
      RowDataPacket { Tables_in_mysql: 'slave_relay_log_info' },
      RowDataPacket { Tables_in_mysql: 'slave_worker_info' },
      RowDataPacket { Tables_in_mysql: 'slow_log' },
      RowDataPacket { Tables_in_mysql: 'tables_priv' },
      RowDataPacket { Tables_in_mysql: 'time_zone' },
      RowDataPacket { Tables_in_mysql: 'time_zone_leap_second' },
      RowDataPacket { Tables_in_mysql: 'time_zone_name' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition' },
      RowDataPacket { Tables_in_mysql: 'time_zone_transition_type' },
      RowDataPacket { Tables_in_mysql: 'user' }
    ]
    [
      RowDataPacket { User: 'mySqlAdmin' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'azure_superuser' },
      RowDataPacket { User: 'mysql.session' },
      RowDataPacket { User: 'mysql.sys' }
    ]
    done
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer JavaScript-Web-App](../deploy-web-app.md)
* [Azure Database for MySQL](/azure/mysql/)
* [Migration durch Sichern und Wiederherstellen](/azure/mysql/concepts-migrate-dump-restore)
* [Migration mit MySQL Workbench](/azure/mysql/concepts-migrate-import-export)