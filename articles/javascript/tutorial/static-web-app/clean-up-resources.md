---
title: Entfernen von Azure-Ressourcen
description: Hier erfahren Sie, wie Sie abrechenbare Ressourcen bereinigen, indem Sie die Ressourcengruppe mithilfe eines Azure CLI-Befehls entfernen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b425ce873c53ca95628cfe8c3f68ea4b010aba3
ms.sourcegitcommit: 4dac39849ba2e48034ecc91ef578d11aab796e58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94993465"
---
# <a name="6-clean-up-resources"></a>6. Bereinigen von Ressourcen

Nach Abschluss dieses Tutorials müssen Sie die Ressourcengruppe entfernen, die die Ressource für maschinelles Sehen sowie die statische Web-App enthält, um sicherzustellen, dass Ihnen keine weitere Nutzung in Rechnung gestellt wird. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Entfernen aller Ressourcen durch Entfernen der Ressourcengruppe

Verwenden Sie im gleichen Terminal den [Azure CLI-Befehl](/cli/azure/group?view=azure-cli-latest#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name rg-demo  -y
```

Die Ausführung dieses Befehls kann mehrere Minuten dauern. 
