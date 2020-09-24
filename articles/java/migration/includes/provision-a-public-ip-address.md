---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 92caae7f50ddb0e58f14e8c99a4cd598a72f884f
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738135"
---
### <a name="provision-a-public-ip-address"></a>Bereitstellen einer öffentlichen IP-Adresse

Wenn für Ihre Anwendung der Zugriff von außerhalb Ihrer internen oder virtuellen Netzwerke möglich sein soll, benötigen Sie eine öffentliche statische IP-Adresse. Sie sollten diese IP-Adresse in der Knotenressourcengruppe Ihres Clusters bereitstellen. Dies wird im folgenden Beispiel veranschaulicht:

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
