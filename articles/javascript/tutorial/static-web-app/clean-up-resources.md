---
title: Entfernen von Azure-Ressourcen
description: Hier erfahren Sie, wie Sie abrechenbare Ressourcen bereinigen, indem Sie die Ressourcengruppe mithilfe eines Azure CLI-Befehls entfernen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: bc68f550d0a2c1bc1550eb755e6ef33bae6ae9b7
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561636"
---
# <a name="7-clean-up-resources-for-static-web-app"></a>7. Bereinigen von Ressourcen für die statische Web-App

Nach Abschluss dieses Tutorials müssen Sie die Ressourcengruppe entfernen, die die Ressource für maschinelles Sehen sowie die statische Web-App enthält, um sicherzustellen, dass Ihnen keine weitere Nutzung in Rechnung gestellt wird. 

## <a name="remove-all-the-resources-by-removing-resource-group"></a>Entfernen aller Ressourcen durch Entfernen der Ressourcengruppe

Verwenden Sie im gleichen Terminal den [Azure CLI-Befehl](/cli/azure/group#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name rg-demo  -y
```

Die Ausführung dieses Befehls kann mehrere Minuten dauern. 
