---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 8353ffea6f141b2bf25c726bfadd7f8926e552ce
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102118752"
---
## <a name="add-firewall-rule-for-your-client-ip-address"></a>Hinzufügen einer Firewallregel für Ihre Client-IP-Adresse

Gehen Sie wie unten angegeben vor, wenn Sie Ihrer Ressource nach der Erstellung eine Client-IP-Adresse hinzufügen müssen, damit Ihre Clientverbindung mit dem Server per JavaScript erfolgreich ist. Verwenden Sie den Befehl [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update), um die Firewallregeln zu aktualisieren.


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