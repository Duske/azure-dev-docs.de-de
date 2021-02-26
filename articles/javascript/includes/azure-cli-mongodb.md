---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: b2877a43eda86868eb3ee2841cedfd8c4d242340
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019028"
---
## <a name="create-a-cosmos-db-resource-for-mongodb"></a>Erstellen einer Cosmos DB-Ressource für MongoDB

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue CosmosDB-Ressource für eine MongoDB-Datenbank zu erstellen. 

```azurecli
az cosmosdb create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --locations regionName=eastus \
    --kind MongoDB \
    --enable-public-network true \
    --ip-range-filter 123.123.123.123 
```

Ersetzen `123.123.123.123` Sie durch Ihre eigene Client-IP-Adresse, oder entfernen Sie den Parameter ganz. 

Die Ausführung dieses Befehls kann einige Minuten dauern. Dabei wird eine öffentlich verfügbare Ressource in der Region `eastus` erstellt. 

```text
{
  "apiProperties": {
    "serverVersion": "3.6"
  },
  "capabilities": [
    {
      "name": "EnableMongo"
    }
  ],
  "connectorOffer": null,
  "consistencyPolicy": {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "cors": [],
  "databaseAccountOfferType": "Standard",
  "disableKeyBasedMetadataWriteAccess": false,
  "documentEndpoint": "https://mongo-2.documents.azure.com:443/",
  "enableAnalyticalStorage": false,
  "enableAutomaticFailover": false,
  "enableCassandraConnector": null,
  "enableFreeTier": false,
  "enableMultipleWriteLocations": false,
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "mongodb-2",
      "locationName": "East US"
    }
  ],
  "id": "/subscriptions/.../resourceGroups/my-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/mongo-2",
  "ipRules": [
    {
      "ipAddressOrRange": "123.123.123.123"
    }
  ],
  "isVirtualNetworkFilterEnabled": false,
  "keyVaultKeyUri": null,
  "kind": "MongoDB",
  "location": "Central US",
  "locations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "name": "mongo-2",
  "privateEndpointConnections": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "readLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "my-resource-group",
  "systemData": {
    "createdAt": "2021-02-08T20:21:05.9519342Z"
  },
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "virtualNetworkRules": [],
  "writeLocations": [
    {
      "documentEndpoint": "https://mongodb-2.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "mongodb-2",
      "isZoneRedundant": false,
      "locationName": "East US",
      "provisioningState": "Succeeded"
    }
  ]
}
```

## <a name="add-firewall-rule-for-your-client-ip-address-to-mongodb-resource"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse zur MongoDB-Ressource

Die Firewallregeln sind nicht standardmäßig konfiguriert. Sie müssen Ihre Client-IP-Adresse hinzufügen, damit die Clientverbindung mit dem Server mit JavaScript erfolgreich hergestellt werden kann.

Verwenden Sie den Befehl [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update), um die Firewallregeln zu aktualisieren.

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123
```

Verwenden Sie eine durch Trennzeichen getrennte Liste, um mehrere IP-Adressen zu konfigurieren.

```azurecli
az cosmosdb update \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --ip-range-filter 123.123.123.123,456.456.456.456
```

## <a name="get-the-mongodb-connection-string-for-your-resource"></a>Abrufen der MongoDB-Verbindungszeichenfolge für Ihre Ressource

Rufen Sie mit dem Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) die MongoDB-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az cosmosdb keys list \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --type connection-strings 
```

Dadurch werden vier Verbindungszeichenfolgen zurückgegeben: zwei für Lese-/Schreibzugriff und zwei für reinen Lesezugriff. Es gibt zwei Verbindungszeichenfolgen, damit Sie zwei unterschiedlichen Systemen oder Entwicklern eine Verbindungszeichenfolge zur individuellen Verwendung zur Verfügung stellen können. 

Stellen Sie mit einer Verbindungszeichenfolge eine Verbindung mit der MongoDB-Datenbank her. Stellen Sie sicher, dass der Dienst auf eine der folgenden Arten verfügbar ist:

* Öffentlich verfügbar
* Mit Firewalleinstellungen für die IP-Adresse Ihres Clients

## <a name="configure-your-azure-web-app-with-the-connection-string"></a>Konfigurieren der Azure-Web-App mit der Verbindungszeichenfolge

Fügen Sie die Umgebungsvariable **MONGODB_URL** einer Azure-Web-App mit [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) hinzu, sodass die Web-App eine Verbindung mit der Cosmos DB-Ressource herstellt:

```azurecli
az webapp config appsettings set \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --settings MONGODB_URL=YOUR-CONNECTION-STRING
```
