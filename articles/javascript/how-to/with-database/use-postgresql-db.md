---
title: Verwenden von JavaScript in Azure PostgreSQL
description: Sie benötigen eine PostgreSQL-Ressource, um Ihre PostgreSQL-Datenbank in Azure zu erstellen oder zu verschieben.
ms.topic: how-to
ms.date: 02/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: 2633a8b9e8f120a23af7840097c2930127e294ec
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004987"
---
# <a name="develop-a-javascript-application-with-postgresql-on-azure"></a>Entwickeln einer JavaScript-Anwendung mit PostgreSQL in Azure

Sie benötigen eine Ressource vom Typ **Azure Database for PostgreSQL-Server**, um eine PostgreSQL-Datenbank in Azure zu erstellen, zu verschieben oder zu verwenden. Hier erfahren Sie, wie Sie die Ressource erstellen und Ihre Datenbank verwenden.

## <a name="create-an-azure-database-for-postgresql-resource"></a>Erstellen einer Azure Database for PostgreSQL-Ressource 

Verwenden Sie zum Erstellen einer Ressource Folgendes:

* [Azure-Befehlszeilenschnittstelle](../with-azure-cli/create-postgresql-server-resource.md)
* [Visual Studio Code](../with-visual-studio-code/create-azure-database.md#create-a-postgresql-database)
* [Azure portal](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)
* [@azure/arm-postgresql](https://www.npmjs.com/package/@azure/arm-postgresql)

## <a name="view-and-use-your-postgresql-server-on-azure"></a>Anzeigen und Verwenden Ihres PostgreSQL-Servers in Azure
Verwenden Sie beim Entwickeln Ihrer PostgreSQL-Datenbank mit JavaScript eines der folgenden Tools:

* [Azure Cloud Shell](https://shell.azure.com/): psql-Befehlszeilenschnittstelle verfügbar
* [pgAdmin](https://www.pgadmin.org/)
* Visual Studio Code-[Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="use-sdk-packages-to-develop-your-postgresql-server-on-azure"></a>Verwenden von SDK-Paketen zum Entwickeln Ihres PostgreSQL-Servers in Azure

Azure PostgreSQL verwendet bereits verfügbare npm-Pakete, etwa:

* [pg](https://www.npmjs.com/package/pg)

## <a name="use-pg-sdk-to-connect-to-postgresql-on-azure"></a>Verwenden des pg SDK zum Herstellen einer Verbindung mit PostgreSQL in Azure

Gehen Sie wie folgt vor, um mit JavaScript eine Verbindung mit Ihrer PostgreSQL-Instanz in Azure herzustellen und diese zu verwenden.

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir DbDemo && \
        cd DbDemo && \
        npm init -y && \
        npm install pg && \
        touch index.js && \
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `DbDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Installieren das npm-Pakets „pg“ zur Verwendung von async/await
    * Erstellen der Skriptdatei `index.js`
    * Öffnen des Projekts in Visual Studio Code

1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:

    ```nodejs
    const { Client } = require('pg')
    
    const query = async (connectionString) => {
        
        // create connection
        const connection = new Client(connectionString);
        connection.connect();
        
        // show tables in the postgres database
        const tables = await connection.query('SELECT table_name FROM information_schema.tables where table_type=\'BASE TABLE\';');
        console.log(tables.rows);
    
        // show users configured for the server
        const users = await connection.query('select pg_user.usename FROM pg_catalog.pg_user;');
        console.log(users.rows);
        
        // close connection
        connection.end();
    }
    
    const server='YOURRESOURCENAME';
    const user='YOUR-ADMIN-USER';
    const password='YOUR-PASSWORD';
    const database='postgres';

    const connectionString = `postgres://${user}@${server}:${password}@${server}.postgres.database.azure.com:5432/${database}`;
    
    query(connectionString)
    .then(() => console.log('done'))
    .catch((err) => console.log(err));
    ```

1. Ersetzen Sie `YOUR-ADMIN-USER`, `YOURRESOURCENAME` und `YOUR-PASSWORD` durch Ihre Werte im Skript für Ihre Verbindungszeichenfolge. 

1. Führen Sie das Skript aus, um eine Verbindung mit dem `postgres`-Server herzustellen und die Basistabellen und Benutzer anzuzeigen.

    ```bash
    node index.js
    ```

1. Zeigen Sie die Ergebnisse an. 

    ```bash
    [
      { table_name: 'pg_statistic' },
      { table_name: 'pg_type' },
      { table_name: 'pg_authid' },
      { table_name: 'pg_user_mapping' },
      { table_name: 'pg_attribute' },
      { table_name: 'pg_proc' },
      { table_name: 'pg_class' },
      { table_name: 'pg_attrdef' },
      { table_name: 'pg_constraint' },
      { table_name: 'pg_inherits' },
      { table_name: 'pg_index' },
      { table_name: 'pg_operator' },
      { table_name: 'pg_opfamily' },
      { table_name: 'pg_opclass' },
      { table_name: 'pg_am' },
      { table_name: 'pg_amop' },
      { table_name: 'pg_amproc' },
      { table_name: 'pg_language' },
      { table_name: 'pg_largeobject_metadata' },
      { table_name: 'pg_aggregate' },
      { table_name: 'pg_rewrite' },
      { table_name: 'pg_largeobject' },
      { table_name: 'pg_trigger' },
      { table_name: 'pg_event_trigger' },
      { table_name: 'pg_description' },
      { table_name: 'pg_cast' },
      { table_name: 'pg_enum' },
      { table_name: 'pg_namespace' },
      { table_name: 'pg_conversion' },
      { table_name: 'pg_depend' },
      { table_name: 'pg_database' },
      { table_name: 'pg_db_role_setting' },
      { table_name: 'pg_tablespace' },
      { table_name: 'pg_pltemplate' },
      { table_name: 'pg_auth_members' },
      { table_name: 'pg_shdepend' },
      { table_name: 'pg_shdescription' },
      { table_name: 'pg_ts_config' },
      { table_name: 'pg_ts_config_map' },
      { table_name: 'pg_ts_dict' },
      { table_name: 'pg_ts_parser' },
      { table_name: 'pg_ts_template' },
      { table_name: 'pg_extension' },
      { table_name: 'pg_foreign_data_wrapper' },
      { table_name: 'pg_foreign_server' },
      { table_name: 'pg_foreign_table' },
      { table_name: 'pg_policy' },
      { table_name: 'pg_replication_origin' },
      { table_name: 'pg_default_acl' },
      { table_name: 'pg_init_privs' },
      { table_name: 'pg_seclabel' },
      { table_name: 'pg_shseclabel' },
      { table_name: 'pg_collation' },
      { table_name: 'pg_range' },
      { table_name: 'pg_transform' },
      { table_name: 'sql_features' },
      { table_name: 'sql_implementation_info' },
      { table_name: 'sql_languages' },
      { table_name: 'sql_packages' },
      { table_name: 'sql_parts' },
      { table_name: 'sql_sizing' },
      { table_name: 'sql_sizing_profiles' }
    ]
    [ { usename: 'azure_superuser' }, { usename: 'YOUR-ADMIN-USER' } ]
    done
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer JavaScript-Web-App](../deploy-web-app.md)
* [Azure Database for PostgreSQL-Server](/azure/postgresql/)