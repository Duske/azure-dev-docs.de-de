---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 0c441254e2c053198cc8e41e0d2457adbf3079af
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004738"
---
## <a name="create-a-mariadb-resource-with-azure-cli"></a>Erstellen einer MariaDB-Ressource mithilfe der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue MariaDB-Ressource für Ihre Datenbank zu erstellen. 

```azurecli
az mariadb server create \
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
  "administratorLogin": "mariaAdmin",
  "connectionString": "mysql defaultdb --host mariadb-2.mariadb.database.azure.com --user mariaAdmin@mariadb-2 --password=123456789",
  "databaseName": "defaultdb",
  "earliestRestoreDate": "2021-02-08T19:14:17.317000+00:00",
  "fullyQualifiedDomainName": "mariadb-2.mariadb.database.azure.com",
  "id": "/subscriptions/bb881e62-cf77-4d5d-89fb-29d71e930b66/resourceGroups/my-resource-group/providers/Microsoft.DBforMariaDB/servers/mariadb-2",
  "location": "westus",
  "masterServerId": "",
  "name": "mariadb-2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

Bevor Sie programmgesteuert eine Verbindung mit dem Server herstellen können, müssen Sie die Firewallregeln so konfigurieren, dass Ihre Client-IP-Adresse zugelassen wird. 

## <a name="add-firewall-rule-for-your-client-ip-address-to-mariadb-resource"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse zur MariaDB-Ressource

Die Firewallregeln sind nicht standardmäßig konfiguriert. Sie müssen Ihre Client-IP-Adresse hinzufügen, damit die Clientverbindung mit dem Server mit JavaScript erfolgreich hergestellt werden kann.

```azurecli
az mariadb server firewall-rule create \
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

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az mariadb db create](/cli/azure/mariadb/db#az_mariadb_db_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue MariaDB-Datenbank auf Ihrem Server zu erstellen. 

```azurecli
az mariadb db create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --server-name YOURRESOURCENAME \
    --name YOURDATABASENAME
```

## <a name="get-the-mariadb-connection-string-with-azure-cli"></a>Abrufen der MariaDB-Verbindungszeichenfolge über die Azure CLI

Rufen Sie mit dem Befehl [az mariadb server show-connection-string](/cli/azure/mariadb/server#az_mariadb_server_show_connection_string) die MariaDB-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az mariadb server show-connection-string \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --server-name YOURRESOURCENAME \
    --database-name YOURDATABASENAME \
    --admin-user YOUR-ADMIN-NAME \
    --admin-password YOUR-ADMIN-PASSWORD 
```

Dadurch werden die Verbindungszeichenfolgen für die gängigen Sprachen als JSON-Objekt zurückgegeben:

```json
{
  "connectionStrings": {
    "ado.net": "Server=YOURRESOURCENAME.mariadb.database.azure.com; Port=3306; Database=YOURDATABASENAME; Uid=YOUR-ADMIN-NAME@YOURRESOURCENAME; Pwd=YOUR-ADMIN-PASSWORD",
    "jdbc": "jdbc:mariadb://YOURRESOURCENAME.mariadb.database.azure.com:3306/YOURDATABASENAME?user=YOUR-ADMIN-NAME@YOURRESOURCENAME&password=YOUR-ADMIN-PASSWORD",
    "node.js": "var conn = mysql.createConnection({host: 'YOURRESOURCENAME.mariadb.database.azure.com', user: 'YOUR-ADMIN-NAME@YOURRESOURCENAME',password: YOUR-ADMIN-PASSWORD, database: YOURDATABASENAME, port: 3306});",
    "php": "host=YOURRESOURCENAME.mariadb.database.azure.com port=3306 dbname=YOURDATABASENAME user=YOUR-ADMIN-NAME@YOURRESOURCENAME password=YOUR-ADMIN-PASSWORD",
    "python": "cnx = mysql.connector.connect(user='YOUR-ADMIN-NAME@YOURRESOURCENAME', password='YOUR-ADMIN-PASSWORD', host='YOURRESOURCENAME.mariadb.database.azure.com', port=3306, database='YOURDATABASENAME')",
    "ruby": "client = Mysql2::Client.new(username: 'YOUR-ADMIN-NAME@YOURRESOURCENAME', password: 'YOUR-ADMIN-PASSWORD', database: 'YOURDATABASENAME', host: 'YOURRESOURCENAME.mariadb.database.azure.com', port: 3306)"
  }
}
``` 




