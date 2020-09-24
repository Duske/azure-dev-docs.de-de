---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c8cd0672bfedf640077e9ae3b9272b12d8ce0b61
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738136"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Bereitstellen von Azure Container Registry und Azure Kubernetes Service

Verwenden Sie die folgenden Befehle zum Erstellen einer Containerregistrierung und eines Azure Kubernetes-Clusters mit einem Dienstprinzipal, der für die Registrierung über die Rolle „Leser“ verfügt. Achten Sie darauf, dass Sie für die Netzwerkanforderungen Ihres Clusters [das richtige Netzwerkmodell auswählen](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model).

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
