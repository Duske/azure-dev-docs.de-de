---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 1fe0e6ef7147040d1a5496f6492cc1e63e2e425e
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004991"
---
## <a name="create-an-azure-database-for-postgresql-server-resource-with-azure-cli"></a>Erstellen einer Azure Database for PostgreSQL-Serverressource mithilfe der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue PostgreSQL-Serverressource für Ihre Datenbank zu erstellen. 

```azurecli
az postgres server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --location eastus \
    --name YOURRESOURCENAME \
    --admin-user YOUR-ADMIN-USER \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

Die Ausführung dieses Befehls kann einige Minuten dauern. Dabei wird eine öffentlich verfügbare Ressource in der Region `eastus` erstellt. 

Die Antwort enthält die Konfigurationsdetails Ihres Servers, einschließlich: 
* automatisch generiertes Kennwort für das Administratorkonto
* Verbindungszeichenfolge

```text
{
  "additionalProperties": {},
  "administratorLogin": "YOUR-ADMIN-USER",
  "byokEnforcement": "Disabled",
  "connectionString": "postgres://YOUR-ADMIN-USER%40YOURRESOURCENAME:123456789@YOURRESOURCENAME.postgres.database.azure.com/postgres?sslmode=require",
  "earliestRestoreDate": "2021-02-08T21:56:20.130000+00:00",
  "fullyQualifiedDomainName": "YOURRESOURCENAME.postgres.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.DBforPostgreSQL/servers/YOURRESOURCENAME",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "eastus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "YOURRESOURCENAME",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Disabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageAutogrow": "Enabled",
    "storageMb": 51200
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

Bevor Sie eine Verbindung mit dem Server herstellen können, müssen Sie die Firewallregeln so konfigurieren, dass Ihre Client-IP-Adresse zugelassen wird. 

## <a name="add-a-firewall-rule-for-your-client-ip-address-to-postgresql-resource"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse zur PostgreSQL-Ressource

Die Firewallregeln sind nicht standardmäßig konfiguriert. Sie müssen Ihre Client-IP-Adresse hinzufügen, damit die Clientverbindung mit dem Server mit JavaScript erfolgreich hergestellt werden kann.

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az postgres server firewall-rule create](/cli/azure/postgres/server#az_postgres_server_firewall_rule_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue Firewallregel für Ihre Datenbank zu erstellen. 


```azurecli
az postgres server firewall-rule create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server YOURRESOURCENAME \
    --name AllowMyIP \
    --start-ip-address 123.123.123.123 \
    --end-ip-address 123.123.123.123
```

Wenn Sie Ihre Client-IP-Adresse nicht kennen, verwenden Sie eine der folgenden Methoden:
* Verwenden Sie das Azure-Portal, um die Firewallregeln anzuzeigen und zu ändern. Dazu gehört das Hinzufügen der erkannten Client-IP-Adresse.
* Führen Sie den Node.js-Code aus. Der Fehler im Zusammenhang mit dem Verstoß gegen die Firewallregeln enthält Ihre Client-IP-Adresse.

Sie können zwar den vollständigen Internetadressbereich (0.0.0.0-255.255.255.255) als Firewallregel hinzufügen, dadurch ist Ihr Server jedoch anfällig für Angriffe. 

## <a name="create-a-postgresql-database-on-the-server-with-azure-cli"></a>Erstellen einer PostgreSQL-Datenbank auf dem Server mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az postgres db create](/cli/azure/postgres/db#az_postgres_db_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue PostgreSQL-Datenbank auf Ihrem Server zu erstellen. 

```azurecli
az postgres db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-postgresql-connection-string-with-azure-cli"></a>Abrufen der PostgreSQL-Verbindungszeichenfolge über die Azure CLI

Rufen Sie mit dem Befehl [az postgres server show-connection-string](/cli/azure/postgres/server#az_postgres_server_show_connection_string) die PostgreSQL-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az postgres server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

Dadurch werden die Verbindungszeichenfolgen für die gängigen Sprachen als JSON-Objekt zurückgegeben. Sie müssen `{database}`, `{username}` und `{password}` durch eigene Werte ersetzen, damit Sie die Verbindungszeichenfolge verwenden können. Ersetzen Sie `YOURRESOURCENAME` durch den Ressourcennamen.

```json
{
  "connectionStrings": {
    "C++ (libpq)": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}YOURRESOURCENAME password={password} sslmode=require",
    "ado.net": "Server=YOURRESOURCENAME.postgres.database.azure.com;Database={database};Port=5432;User Id={username}@YOURRESOURCENAME;Password={password};",
    "jdbc": "jdbc:postgresql://YOURRESOURCENAME.postgres.database.azure.com:5432/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "node.js": "var client = new pg.Client('postgres://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com:5432/{database}');",
    "php": "host=YOURRESOURCENAME.postgres.database.azure.com port=5432 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "psql_cmd": "postgresql://{username}@YOURRESOURCENAME:{password}@YOURRESOURCENAME.postgres.database.azure.com/{database}?sslmode=require",
    "python": "cnx = psycopg2.connect(database='{database}', user='{username}@YOURRESOURCENAME', host='YOURRESOURCENAME.postgres.database.azure.com', password='{password}', port='5432')",
    "ruby": "cnx = PG::Connection.new(:host => 'YOURRESOURCENAME.postgres.database.azure.com', :user => '{username}@YOURRESOURCENAME', :dbname => '{database}', :port => '5432', :password => '{password}')"
  }
}
``` 