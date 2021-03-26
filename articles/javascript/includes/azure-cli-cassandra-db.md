---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: e37f278149f8173c3addddda085f37112929bf33
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102118771"
---
## <a name="create-a-cosmos-db-resource-for-cassandra-db"></a>Erstellen einer Cosmos DB-Ressource für eine Cassandra-Datenbank

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue Ressource für Ihre Cassandra-Datenbank zu erstellen. 

```azurecli
az cosmosdb create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE_NAME \
    --capabilities EnableCassandra
```

Die Ausführung dieses Befehls kann einige Minuten dauern. Hierbei wird eine öffentlich verfügbare Ressource erstellt. Sie müssen keine Firewallregeln konfigurieren, um Ihre Client-IP-Adresse zuzulassen. 

Die Antwort enthält die Konfigurationsdetails Ihres Servers, einschließlich: 

* `location`: Wird für den Wert `localDataCenter` im JavaScript-Code mit dem cassandra-driver verwendet. 

```json
{
  "apiProperties": null,
  "capabilities": [
    {
      "name": "EnableCassandra"
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
  "documentEndpoint": "https://YOUR-RESOURCE_NAME.documents.azure.com:443/",
  "enableAnalyticalStorage": false,
  "enableAutomaticFailover": false,
  "enableCassandraConnector": null,
  "enableFreeTier": false,
  "enableMultipleWriteLocations": false,
  "failoverPolicies": [
    {
      "failoverPriority": 0,
      "id": "YOUR-RESOURCE_NAME-centralus",
      "locationName": "Central US"
    }
  ],
  "id": "/subscriptions/YOUR-SUBSCRIPTION-ID-OR-NAME/resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.DocumentDB/databaseAccounts/YOUR-RESOURCE_NAME",
  "ipRules": [],
  "isVirtualNetworkFilterEnabled": false,
  "keyVaultKeyUri": null,
  "kind": "GlobalDocumentDB",
  "location": "Central US",
  "locations": [
    {
      "documentEndpoint": "https://YOUR-RESOURCE_NAME-centralus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "YOUR-RESOURCE_NAME-centralus",
      "isZoneRedundant": false,
      "locationName": "Central US",
      "provisioningState": "Succeeded"
    }
  ],
  "name": "YOUR-RESOURCE_NAME",
  "privateEndpointConnections": null,
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "readLocations": [
    {
      "documentEndpoint": "https://YOUR-RESOURCE_NAME-centralus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "YOUR-RESOURCE_NAME-centralus",
      "isZoneRedundant": false,
      "locationName": "Central US",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "systemData": {
    "createdAt": "2021-02-16T23:00:23.0375775Z"
  },
  "tags": {},
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "virtualNetworkRules": [],
  "writeLocations": [
    {
      "documentEndpoint": "https://YOUR-RESOURCE_NAME-centralus.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "YOUR-RESOURCE_NAME-centralus",
      "isZoneRedundant": false,
      "locationName": "Central US",
      "provisioningState": "Succeeded"
    }
  ]
}
```


## <a name="create-a-keyspace-on-the-server-with-azure-cli"></a>Erstellen eines Keyspace auf dem Server mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) in [Azure Cloud Shell](https://shell.azure.com), um auf Ihrem Server einen neuen Cassandra-Keyspace zu erstellen. 

```azurecli
az cosmosdb cassandra keyspace create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --account-name YOUR-RESOURCE_NAME \
    --name YOUR-KEYSPACE-NAME
```

## <a name="create-a-table-on-the-keyspace-with-azure-cli"></a>Erstellen einer Tabelle im Keyspace mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) in [Azure Cloud Shell](https://shell.azure.com), um auf Ihrem Server eine neue Cassandra-Tabelle zu erstellen. 

```azurecli
az cosmosdb cassandra table create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --account-name YOUR-RESOURCE_NAME \
    --keyspace-name YOUR-KEYSPACE-NAME \
    --name YOUR-TABLE-NAME \
    --schema @schema.json
```

Mit dem JSON-Code der Schemadatei werden die Spalten, Datentypen und der Partitionsschlüssel für die Tabelle definiert:

```json
{
    "columns": [
        {
            "name": "Name",
            "type": "Ascii"
        },
        {
            "name": "Alias",
            "type": "Ascii"
        },
        {
            "name": "Region",
            "type": "Ascii"
        }        
    ],
    "partitionKeys": [
        {
            "name": "Region"
        }
    ]
}
```

## <a name="get-the-cassandra-connection-string-with-azure-cli"></a>Abrufen der Cassandra-Verbindungszeichenfolge über die Azure CLI
Rufen Sie mit dem Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) die MongoDB-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az cosmosdb keys list \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --type connection-strings 
```

Ihre Verbindungszeichenfolgen werden zurückgegeben. Der folgende JSON-Code ist ein Beispielergebnis, in dem die Sicherheitsinformationen wie folgt ersetzt werden:

* `YOUR-RESOURCE-NAME`: Für Benutzername und einen Teil des Hostnamens oder Kontoendpunkts
* `YOUR-USERNAME`: Der gleiche Wert wie `YOUR-RESOURCE-NAME`
* `PASSWORD-1`
* `PASSWORD-2`
* `ACCOUNT-KEY-1`
* `ACCOUNT-KEY-2`

```json
{
    "connectionStrings": [
      {
        "connectionString": "AccountEndpoint=https://YOUR-RESOURCE-NAME.documents.azure.com:443/;AccountKey=PASSWORD-1;",
        "description": "Primary SQL Connection String"
      },
      {
        "connectionString": "AccountEndpoint=https://YOUR-RESOURCE-NAME.documents.azure.com:443/;AccountKey=PASSWORD-2;",
        "description": "Secondary SQL Connection String"
      },
      {
        "connectionString": "AccountEndpoint=https://YOUR-RESOURCE-NAME.documents.azure.com:443/;AccountKey=ACCOUNT-KEY-1;",
        "description": "Primary Read-Only SQL Connection String"
      },
      {
        "connectionString": "AccountEndpoint=https://YOUR-RESOURCE-NAME.documents.azure.com:443/;AccountKey=ACCOUNT-KEY-2;",
        "description": "Secondary Read-Only SQL Connection String"
      },
      {
        "connectionString": "HostName=YOUR-RESOURCE-NAME.cassandra.cosmos.azure.com;Username=YOUR-RESOURCE-NAME;Password=PASSWORD-1;Port=10350",
        "description": "Primary Cassandra Connection String"
      },
      {
        "connectionString": "HostName=YOUR-RESOURCE-NAME.cassandra.cosmos.azure.com;Username=YOUR-RESOURCE-NAME;Password=PASSWORD-2;Port=10350",
        "description": "Secondary Cassandra Connection String"
      },
      {
        "connectionString": "HostName=YOUR-RESOURCE-NAME.cassandra.cosmos.azure.com;Username=YOUR-RESOURCE-NAME;Password=ACCOUNT-KEY-1;Port=10350",
        "description": "Primary Read-Only Cassandra Connection String"
      },
      {
        "connectionString": "HostName=YOUR-RESOURCE-NAME.cassandra.cosmos.azure.com;Username=YOUR-RESOURCE-NAME;Password=ACCOUNT-KEY-2;Port=10350",
        "description": "Secondary Read-Only Cassandra Connection String"
      }
    ]
  }
```

Stellen Sie die Verbindung mit der Cassandra-Datenbank mit einer Verbindungszeichenfolge her. Ihr Cassandra-Benutzername ist der Ressourcenname. 