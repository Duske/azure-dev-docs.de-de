---
title: Bereinigen der Ressourcen nach dem Bereitstellen einer Node.js-App in Azure mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 7 des Tutorials: Bereinigen der Ressourcen'
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 183539b8e2f0246bd812e5fa364a885b75626819
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709857"
---
# <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-docker-node-06.md)

Die von Ihnen erstellte App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Führen Sie zum Bereinigen der Ressourcen den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung aus:

```azurecli
az group delete --name myResourceGroup
```

Sie können auch das [Azure-Portal](https://portal.azure.com) aufrufen, **Ressourcengruppen** im Navigationsbereich auf der linken Seite auswählen, die im Rahmen dieses Tutorials erstellte Ressourcengruppe auswählen und dann den Befehl **Ressourcengruppe löschen** verwenden.

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
