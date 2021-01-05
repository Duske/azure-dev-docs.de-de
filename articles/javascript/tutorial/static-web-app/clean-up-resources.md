---
title: Entfernen von Azure-Ressourcen
description: Hier erfahren Sie, wie Sie abrechenbare Ressourcen bereinigen, indem Sie die Ressourcengruppe mithilfe eines Azure CLI-Befehls entfernen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 132ccc26a4ddf17eb38be1573f462492fe1f7f0c
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687496"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. Bereinigen von Ressourcen für die statische Web-App

Nach Abschluss dieses Tutorials müssen Sie die Ressourcengruppe entfernen, die die Ressource für maschinelles Sehen sowie die statische Web-App enthält, um sicherzustellen, dass Ihnen keine weitere Nutzung in Rechnung gestellt wird. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Entfernen aller Ressourcen durch Entfernen der Ressourcengruppe

Verwenden Sie im gleichen Terminal den [Azure CLI-Befehl](/cli/azure/group?view=azure-cli-latest#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name rg-demo  -y
```

Die Ausführung dieses Befehls kann mehrere Minuten dauern. 
