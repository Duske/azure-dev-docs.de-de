---
title: Entfernen einer Linux-VM-Ressource
description: Bereinigen Sie Azure-Ressourcen, indem Sie die Ressourcengruppe mit einem Azure CLI-Befehl entfernen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: ac61f1b73e873ee1c1c6cd343792a79a88e76123
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98560986"
---
# <a name="7-clean-up-resources"></a>7. Bereinigen von Ressourcen

Nach dem Abschließen dieses Tutorials müssen Sie die Ressourcengruppe entfernen, die alle Ressourcen enthält, um sicherzustellen, dass Ihnen keine weitere Nutzung in Rechnung gestellt wird. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Entfernen aller Ressourcen durch Entfernen der Ressourcengruppe

Verwenden Sie im gleichen Terminal den [Azure CLI-Befehl](/cli/azure/group#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name rg-demo-vm-eastus -y
```

Die Ausführung dieses Befehls dauert einige Minuten. 

## <a name="next-step"></a>Nächster Schritt

* Weitere Informationen zu [Azure Linux-VMs](/azure/virtual-machines)
