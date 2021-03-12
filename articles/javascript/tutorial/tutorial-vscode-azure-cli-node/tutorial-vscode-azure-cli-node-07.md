---
title: Bereinigen der Ressourcen nach dem Bereitstellen einer Node.js-App in Azure mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 7 des Azure CLI-Tutorials: Bereinigen von Ressourcen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d2b45ae60a7ad1270547289d1ea8480d14fedd95
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117964"
---
# <a name="part-7-clean-up-resources"></a>Teil 7: Bereinigen von Ressourcen

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-azure-cli-node-05.md)

Die von Ihnen erstellte App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Führen Sie zum Bereinigen der Ressourcen den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung aus:

```azurecli
az group delete --name myResourceGroup
```

> [!div class="nextstepaction"]
> [Ich bin fertig.](../../how-to/deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)