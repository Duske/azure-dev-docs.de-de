---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/18/2021
ms.openlocfilehash: 079ffe7f07b6038a1f6e320ca37eed3aafb41ec2
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118781"
---
## <a name="create-an-azure-cache-for-redis-resource-with-azure-cli"></a>Erstellen einer Azure Cache for Redis-Ressource mit der Azure CLI

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az redis create](/cli/azure/redis#az_redis_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue Redis-Ressource für Ihre Datenbank zu erstellen. 

```azurecli
az redis create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --location eastus \
    --sku Basic \
    --vm-size c0 \
    --enable-non-ssl-port
```

Die Ausführung dieses Befehls kann einige Minuten dauern. Dabei wird eine öffentlich verfügbare Ressource in der Region `eastus` erstellt. 

Die Antwort enthält die Konfigurationsdetails Ihres Servers, einschließlich: 
* Version von Redis: `redisVersion`
* Ports: `sslPort` und `port`

```text
{
  "accessKeys": null,
  "enableNonSslPort": true,
  "hostName": "YOUR-RESOURCE-NAME.redis.cache.windows.net",
  "id": "/subscriptions/YOUR-SUBSCRIPTION-ID-OR-NAME/resourceGroups/YOUR-RESOURCE-GROUP/providers/Microsoft.Cache/Redis/YOUR-RESOURCE-NAME",
  "instances": [
    {
      "isMaster": false,
      "nonSslPort": 13000,
      "shardId": null,
      "sslPort": 15000,
      "zone": null
    }
  ],
  "linkedServers": [],
  "location": "East US",
  "minimumTlsVersion": null,
  "name": "YOUR-RESOURCE-NAME",
  "port": 6379,
  "provisioningState": "Creating",
  "redisConfiguration": {
    "maxclients": "256",
    "maxfragmentationmemory-reserved": "12",
    "maxmemory-delta": "2",
    "maxmemory-reserved": "2"
  },
  "redisVersion": "4.0.14",
  "replicasPerMaster": null,
  "resourceGroup": "YOUR-RESOURCE-GROUP",
  "shardCount": null,
  "sku": {
    "capacity": 0,
    "family": "C",
    "name": "Basic"
  },
  "sslPort": 6380,
  "staticIp": null,
  "subnetId": null,
  "tags": {},
  "tenantSettings": {},
  "type": "Microsoft.Cache/Redis",
  "zones": null
}
```

## <a name="add-firewall-rule-for-your-client-ip-address-to-redis-resource"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse zur Redis-Ressource

Fügen Sie mit dem Befehl [az redis firewall-rules create](/cli/azure/redis/firewall-rules#az_redis_firewall_rules_create) Firewallregeln hinzu, um den Zugriff auf Ihren Cache über Ihre Client-IP-Adresse oder die IP-Adresse Ihrer App zu definieren.

```azurecli
az redis firewall-rules create \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME \
    --rule-name AllowMyIP \
    --start-ip 123.123.123.123 \
    --end-ip 123.123.123.123
```

Wenn Sie Ihre Client-IP-Adresse nicht kennen, verwenden Sie eine der folgenden Methoden:
* Verwenden Sie das Azure-Portal, um die Firewallregeln anzuzeigen und zu ändern. Hierzu gehört das Hinzufügen der erkannten Client-IP-Adresse.
* Beim Ausführen Ihres Node.js-Codes erhalten Sie einen Fehler aufgrund eines Verstoßes gegen Ihre Firewallregeln, in dem Ihre Client-IP-Adresse angezeigt wird. Kopieren Sie die IP-Adresse, und verwenden Sie sie zum Festlegen Ihrer Firewallregel.

## <a name="get-the-redis-keys-with-azure-cli"></a>Abrufen des Redis-Schlüssels mit der Azure CLI

Rufen Sie mit dem Befehl [az redis list-keys](/cli/azure/redis#az_redis_list_keys) die Redis-Verbindungszeichenfolge für diese Instanz ab:

```azurecli
az redis list-keys \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME
```

Die beiden Schlüssel werden zurückgegeben:

```json
{
    "primaryKey": "5Uey0GHWtCs9yr1FMUMcu1Sv17AJA2QMqPm9CyNKjAA=",
    "secondaryKey": "DEPr+3zWbL6d5XwxPajAriXKgoSeCqraN8SLSoiMWhM="
  }
```

## <a name="connect-azure-cache-for-redis-to-your-app-service-web-app"></a>Herstellen einer Verbindung für Azure Cache for Redis mit Ihrer App Service-Web-App

Fügen Sie Ihrer App Service-Web-App mit dem Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) Verbindungsinformationen hinzu.

```azurecli
az webapp config appsettings set \
--subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
--resource-group YOUR-RESOURCE-GROUP \
--name YOUR-APP-SERVICE-RESOURCE-NAME \
--settings "REDIS_URL=YOUR-REDIS-HOST-NAME" "REDIS_PORT=YOUR-REDIS-PORT" "REDIS_KEY=YOUR-REDIS-KEY"
```

Ersetzen Sie im obigen Code die Angaben für die folgenden Einstellungen:

* YOUR-SUBSCRIPTION-ID-OR-NAME
* YOUR-RESOURCE-GROUP
* YOUR-APP-SERVICE-RESOURCE-NAME
* YOUR-REDIS-HOST-NAME
* YOUR-REDIS-PORT
* YOUR-REDIS-KEY
