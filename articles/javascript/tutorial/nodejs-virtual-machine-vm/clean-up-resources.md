---
title: Entfernen einer Linux-VM-Ressource
description: Bereinigen Sie Azure-Ressourcen, indem Sie die Ressourcengruppe mit einem Azure CLI-Befehl entfernen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5c8c0bb8a1413da72cb2c32d9ce541bee1c36cac
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94872821"
---
# <a name="7-clean-up-resources"></a>7. Bereinigen von Ressourcen

Nach dem Abschließen dieses Tutorials müssen Sie die Ressourcengruppe entfernen, die alle Ressourcen enthält, um sicherzustellen, dass Ihnen keine weitere Nutzung in Rechnung gestellt wird. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Entfernen aller Ressourcen durch Entfernen der Ressourcengruppe

Verwenden Sie im gleichen Terminal den [Azure CLI-Befehl](/cli/azure/group?view=azure-cli-latest#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name rg-demo-vm-eastus -y
```

Die Ausführung dieses Befehls dauert einige Minuten. 

## <a name="next-step"></a>Nächster Schritt

* Weitere Informationen zu [Azure Linux-VMs](/azure/virtual-machines)
