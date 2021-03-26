---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 4cca83242756b75161331116a4e0a563e0e0f073
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102118759"
---
## <a name="create-a-cosmos-db-resource-for-sql-api"></a>Erstellen einer Cosmos DB-Ressource für die SQL-API

Verwenden Sie den folgenden Azure CLI-Befehl vom Typ [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) in [Azure Cloud Shell](https://shell.azure.com), um eine neue Cosmos DB-Ressource zu erstellen. Es kann einige Minuten dauern, bis die Ausführung dieses Befehls abgeschlossen ist. 

```azurecli
az cosmosdb create \
--subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
--resource-group YOUR-RESOURCE-GROUP \
--name YOUR-RESOURCE-NAME \
--kind YOUR-DB-KIND \
--ip-range-filter YOUR-CLIENT-IP
```

Ersetzen Sie `123.123.123.123` durch Ihre eigene Client-IP-Adresse, um den Firewallzugriff von Ihrem lokalen Computer auf Ihre Ressource zu ermöglichen. Verwenden Sie eine durch Trennzeichen getrennte Liste, um mehrere IP-Adressen zu konfigurieren.

[!INCLUDE [Azure CLI command - Cosmos DB Update - firewall IP range](azure-cli-cosmos-db-update-with-firewall.md)]

[!INCLUDE [Azure CLI command - Cosmos DB - get keys](azure-cli-cosmos-db-get-keys.md)]
