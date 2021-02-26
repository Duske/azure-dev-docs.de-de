---
title: Verwenden von JavaScript in Azure MariaDB
description: Sie benötigen eine MariaDB-Ressource, um Ihre MariaDB-Datenbank in Azure zu erstellen oder zu verschieben.
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 320397584f78809e39e7eeaf7c1c6805755a82de
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004746"
---
# <a name="develop-a-javascript-application-with-mariadb-on-azure"></a>Entwickeln einer JavaScript-Anwendung mit MariaDB in Azure

Sie benötigen eine **Azure Database for MariaDB**-Ressource, um eine MariaDB-Datenbank in Azure zu erstellen, zu verschieben oder zu verwenden. Hier erfahren Sie, wie Sie die Ressource erstellen und Ihre Datenbank verwenden.

## <a name="create-an-azure-database-for-mariadb-resource"></a>Erstellen einer Azure Database for MariaDB-Ressource 

Zum Erstellen einer Ressource können Sie Folgendes verwenden:

* Azure CLI
* [Azure portal](https://ms.portal.azure.com/#create/Microsoft.MariaDBServer)
* [@azure/arm-mariadb](https://www.npmjs.com/package/@azure/arm-mariadb)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-mariadb.md)]

## <a name="view-and-use-your-mariadb-on-azure"></a>Anzeigen und Verwenden Ihrer MariaDB-Instanz in Azure
Verwenden Sie beim Entwickeln Ihrer MariaDB-Datenbank mit JavaScript eines der folgenden Tools:

* _mysql_-Befehlszeilenschnittstelle von [Azure Cloud Shell](https://shell.azure.com/)
* [MySQL Workbench](https://www.mysql.com/products/workbench/)
* Visual Studio Code-[Erweiterung](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql)

## <a name="use-sdk-packages-to-develop-your-mariadb-on-azure"></a>Verwenden von SDK-Paketen zum Entwickeln Ihrer MariaDB-Instanz in Azure

Azure MariaDB verwendet bereits verfügbare npm-Pakete, etwa:

* [mariadb](https://www.npmjs.com/package/mariadb)

## <a name="use-mariadb-sdk-to-connect-to-mariadb-on-azure"></a>Verwenden des MariaDB SDK zum Herstellen einer Verbindung mit MariaDB in Azure

Gehen Sie wie folgt vor, um mit JavaScript eine Verbindung mit Ihrer MariaDB-Instanz in Azure herzustellen und diese zu verwenden.

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir mariaDbDemo && \
        cd mariaDbDemo && \
        npm init -y && \
        npm install mariadb && \
        touch index.js && \
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `mariaDbDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Installieren des npm-Pakets „mariadb“
    * Erstellen der Skriptdatei `index.js`
    * Öffnen des Projekts in Visual Studio Code

1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:

    ```nodejs
    // To install npm package,
    // run following command at terminal
    // npm install mariadb

    // get mariadb SDK
    const mariadb = require('mariadb');

    // query server and close connection
    const query = async (config) => {
      // creation connection
      const connection = await mariadb.createConnection(config);

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
      host: 'YOUR-RESOURCE_NAME.mariadb.database.azure.com',
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
      { Database: 'information_schema' },
      { Database: 'mysql' },
      { Database: 'performance_schema' },
      { Database: 'quickstartdb' },
      { Database: 'tutorial' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 256,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { Tables_in_mysql: '__az_action_history__' },
      { Tables_in_mysql: '__az_changed_static_configs__' },
      { Tables_in_mysql: '__az_replica_information__' },
      { Tables_in_mysql: '__az_replication_current_state__' },
      { Tables_in_mysql: '__az_slave_relay_log_info__' },
      { Tables_in_mysql: '__firewall_rules__' },
      { Tables_in_mysql: '__script_version__' },
      { Tables_in_mysql: 'column_stats' },
      { Tables_in_mysql: 'columns_priv' },
      { Tables_in_mysql: 'db' },
      { Tables_in_mysql: 'event' },
      { Tables_in_mysql: 'func' },
      { Tables_in_mysql: 'general_log' },
      { Tables_in_mysql: 'gtid_slave_pos' },
      { Tables_in_mysql: 'help_category' },
      { Tables_in_mysql: 'help_keyword' },
      { Tables_in_mysql: 'help_relation' },
      { Tables_in_mysql: 'help_topic' },
      { Tables_in_mysql: 'host' },
      { Tables_in_mysql: 'index_stats' },
      { Tables_in_mysql: 'innodb_index_stats' },
      { Tables_in_mysql: 'innodb_table_stats' },
      { Tables_in_mysql: 'plugin' },
      { Tables_in_mysql: 'proc' },
      { Tables_in_mysql: 'procs_priv' },
      { Tables_in_mysql: 'proxies_priv' },
      { Tables_in_mysql: 'roles_mapping' },
      { Tables_in_mysql: 'servers' },
      { Tables_in_mysql: 'slow_log' },
      { Tables_in_mysql: 'table_stats' },
      { Tables_in_mysql: 'tables_priv' },
      { Tables_in_mysql: 'time_zone' },
      { Tables_in_mysql: 'time_zone_leap_second' },
      { Tables_in_mysql: 'time_zone_name' },
      { Tables_in_mysql: 'time_zone_transition' },
      { Tables_in_mysql: 'time_zone_transition_type' },
      { Tables_in_mysql: 'transaction_registry' },
      { Tables_in_mysql: 'user' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 292,
          columnType: 253,
          flags: 1,
          scale: 0,
          type: 'VAR_STRING'
        }
      ]
    ]
    [
      { User: 'azureAdmin' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      { User: 'azure_superuser' },
      meta: [
        ColumnDef {
          _parse: [StringParser],
          collation: [Collation],
          columnLength: 320,
          columnType: 254,
          flags: 16515,
          scale: 0,
          type: 'STRING'
        }
      ]
    ]
    done
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer JavaScript-Web-App](../deploy-web-app.md)
* [Azure Database for MariaDB](/azure/mariadb/)
* [Leitfaden für die Migration zu Azure Database for MariaDB](/azure/mariadb/howto-migrate-dump-restore)