---
title: Bereinigen der Ressourcen nach dem Bereitstellen einer Node.js-App in Azure mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 7 des Azure CLI-Tutorials: Bereinigen von Ressourcen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 0c3404067f6324a37d7863568e715b806511a226
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405989"
---
# <a name="part-7-clean-up-resources"></a>Teil 7: Bereinigen von Ressourcen

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-docker-node-06.md)

Die von Ihnen erstellte App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Führen Sie zum Bereinigen der Ressourcen den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung aus:

```azurecli
az group delete --name myResourceGroup
```

Sie können auch das [Azure-Portal](https://portal.azure.com) aufrufen, **Ressourcengruppen** im Navigationsbereich auf der linken Seite auswählen, die im Rahmen dieses Tutorials erstellte Ressourcengruppe auswählen und dann den Befehl **Ressourcengruppe löschen** verwenden.

> [!div class="nextstepaction"]
> [Ich bin fertig.](./how-to/deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)