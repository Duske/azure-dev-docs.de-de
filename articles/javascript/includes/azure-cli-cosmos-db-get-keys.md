---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: e3a7f10e6db4a805c136e0d04289da5333e77bc7
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102118753"
---
## <a name="get-the-cosmos-db-keys-for-your-resource"></a>Abrufen der Cosmos DB-Schlüssel für Ihre Ressource

Rufen Sie die Schlüssel für diese Instanz mit dem Befehl [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) ab:

```azurecli
az cosmosdb keys list \
    --subscription YOUR-SUBSCRIPTION-ID-OR-NAME \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-RESOURCE-NAME
```

Es werden vier Schlüssel zurückgegeben: zwei für Lese-/Schreibzugriff und zwei für reinen Lesezugriff. Es werden jeweils zwei Schlüssel bereitgestellt, damit Sie für zwei unterschiedliche Systeme oder Entwickler einen Schlüssel zur Verfügung stellen können, die dann individuell nutzbar und recycelbar sind. 