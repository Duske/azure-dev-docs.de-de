---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/04/2021
ms.openlocfilehash: 0edd926e2acf016eb6389fe58c7f1d5e78628dbf
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004725"
---
## <a name="create-an-azure-database-for-mysql-resource-with-azure-cli"></a>Erstellen einer Azure Database for MySQL-Ressource mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue MySQL-Ressource für Ihre Datenbank zu erstellen. 

```azurecli
az mysql server create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOURRESOURCENAME \
    --location eastus \
    --admin-user YOUR-ADMIN-NAME \
    --ssl-enforcement Disabled \
    --enable-public-network Enabled 
```

Die Ausführung dieses Befehls kann einige Minuten dauern. Dabei wird eine öffentlich verfügbare Ressource in der Region `eastus` erstellt. 

Die Antwort enthält die Konfigurationsdetails Ihres Servers, einschließlich: 
* automatisch generiertes Kennwort für das Administratorkonto
* Befehl zum Herstellen einer Verbindung mit dem Server mithilfe der MySQL-Befehlszeilenschnittstelle

```text
{
  "additionalProperties": {},
  "administratorLogin": "mySqlAdmin",
  "byokEnforcement": "Disabled",
  "connectionString": "mysql defaultdb --host mysqldb-2.mysql.database.azure.com --user mySqlAdmin@mysqldb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T16:48:01.673000+00:00",
  "fullyQualifiedDomainName": "mysqldb-2.mysql.database.azure.com",
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DBforMySQL/servers/mysqldb-2",
  "identity": null,
  "infrastructureEncryption": "Disabled",
  "location": "westus",
  "masterServerId": "",
  "minimalTlsVersion": "TLSEnforcementDisabled",
  "name": "mysqldb-2",
  "password": "123456789",
  "privateEndpointConnections": [],
  "publicNetworkAccess": "Enabled",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "my-resource-group",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

Bevor Sie programmgesteuert eine Verbindung mit dem Server herstellen können, müssen Sie die Firewallregeln so konfigurieren, dass Ihre Client-IP-Adresse zugelassen wird. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mysql-resource"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse zur MySQL-Ressource

Die Firewallregeln sind nicht standardmäßig konfiguriert. Sie müssen Ihre Client-IP-Adresse hinzufügen, damit die Clientverbindung mit dem Server mit JavaScript erfolgreich hergestellt werden kann.

```azurecli
az mysql server firewall-rule create \
--resource-group YOUR-RESOURCE-GROUP \
--server YOURRESOURCENAME \
--name AllowMyIP --start-ip-address 123.123.123.123 \
--end-ip-address 123.123.123.123
```

Wenn Sie Ihre Client-IP-Adresse nicht kennen, verwenden Sie eine der folgenden Methoden:
* Verwenden Sie das Azure-Portal, um die Firewallregeln anzuzeigen und zu ändern. Dazu gehört das Hinzufügen der erkannten Client-IP-Adresse.
* Führen Sie den Node.js-Code aus. Der Fehler im Zusammenhang mit dem Verstoß gegen die Firewallregeln enthält Ihre Client-IP-Adresse.

Sie können zwar den vollständigen Internetadressbereich (0.0.0.0-255.255.255.255) als Firewallregel hinzufügen, dadurch ist Ihr Server jedoch anfällig für Angriffe. 

## <a name="create-a-database-on-the-server-with-azure-cli"></a>Erstellen einer Datenbank auf dem Server mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az mysql db create](/cli/azure/mysql/db#az_mysql_db_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue MySQL-Datenbank auf Ihrem Server zu erstellen. 

```azurecli
az mysql db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```


## <a name="get-the-mysql-connection-string-with-azure-cli"></a>Abrufen der MySQL-Verbindungszeichenfolge über die Azure CLI

Rufen Sie mit dem Befehl [az mysql server show-connection-string](/cli/azure/mysql/server#az_mysql_server_show_connection_string) die MySQL-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az mysql server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME
```

Dadurch werden die Verbindungszeichenfolgen für die gängigen Sprachen als JSON-Objekt zurückgegeben. Sie müssen `{database}`, `{username}` und `{password}` durch eigene Werte ersetzen, damit Sie die Verbindungszeichenfolge verwenden können. 

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mysql.database.azure.com; Port=3306; Database={database}; Uid={username}@YOURRESOURCENAME; Pwd={password}",
    "jdbc": "jdbc:mysql://YOURRESOURCENAME.mysql.database.azure.com:3306/{database}?user={username}@YOURRESOURCENAME&password={password}",
    "mysql_cmd": "mysql {database} --host YOURRESOURCENAME.mysql.database.azure.com --user {username}@YOURRESOURCENAME --password={password}",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mysql.database.azure.com', user: '{username}@YOURRESOURCENAME',password: {password}, database: {database}, port: 3306});",
    "php": "host=YOURRESOURCENAME.mysql.database.azure.com port=3306 dbname={database} user={username}@YOURRESOURCENAME password={password}",
    "python": "cnx = mysql.connector.connect(user='{username}@YOURRESOURCENAME', password='{password}', host='YOURRESOURCENAME.mysql.database.azure.com', port=3306, database='{database}')",
    "ruby": "client = Mysql2::Client.new(username: '{username}@YOURRESOURCENAME', password: '{password}', database: '{database}', host: 'YOURRESOURCENAME.mysql.database.azure.com', port: 3306)"
  }
}
``` 