---
title: Bereinigen der Ressourcen nach dem Bereitstellen einer Node.js-App in Azure mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 7 des Tutorials: Bereinigen der Ressourcen'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 6d0b9c671c4ffd0fb5d521e6247e9964d6d93980
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686106"
---
# <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-docker-node-06.md)

Die von Ihnen erstellte App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Führen Sie zum Bereinigen der Ressourcen den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung aus:

```bash
az group delete --name myResourceGroup
```

Sie können auch das [Azure-Portal](https://portal.azure.com) aufrufen, **Ressourcengruppen** im Navigationsbereich auf der linken Seite auswählen, die im Rahmen dieses Tutorials erstellte Ressourcengruppe auswählen und dann den Befehl **Ressourcengruppe löschen** verwenden.

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
