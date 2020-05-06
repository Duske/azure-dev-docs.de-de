---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e72cefaab3ccdbbaae01992cc11285944fb09a36
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673206"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Bereitstellen von Azure Container Registry und Azure Kubernetes Service

Verwenden Sie die folgenden Befehle zum Erstellen einer Containerregistrierung und eines Azure Kubernetes-Clusters mit einem Dienstprinzipal, der für die Registrierung über die Rolle „Leser“ verfügt. Achten Sie darauf, dass Sie für die Netzwerkanforderungen Ihres Clusters [das richtige Netzwerkmodell auswählen](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model).

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
