---
title: 'Tutorial: Erneutes Bereitstellen eines Containers in Azure App Service, nachdem Änderungen in Visual Studio Code vorgenommen wurden'
description: 'Tutorialschritt 3: einfache Schritte zum erneuten Erstellen und Bereitstellen eines Containerimages'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 0c57729fd3ab1f671b2533f3e15056aec99cb6c9
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172449"
---
# <a name="tutorial-redeploy-a-container-to-azure-app-service-after-making-changes"></a>Tutorial: Erneutes Bereitstellen eines Containers in Azure App Service, nachdem Änderungen vorgenommen wurden

[Vorheriger Schritt: Bereitstellen des Images in Azure](tutorial-deploy-containers-02.md)

Da Sie unweigerlich Änderungen an Ihrer App vornehmen, müssen Sie den Container mehrmals neu erstellen und neu bereitstellen. Glücklicherweise ist dieser Prozess ganz einfach:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor, und testen Sie sie. (Dieser Schritt und die beiden folgenden Schritte werden im Tutorial [Erstellen eines Python-Containers in VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container) erläutert.)

1. Erstellen Sie das Docker-Image neu. Wenn Sie nur App-Code ändern, sollte der Build nur einige Sekunden dauern.

1. Pushen Sie Ihr Image in die Registrierung. Wenn Sie nur App-Code ändern, muss nur diese kleine Ebene gepusht werden, sodass der Prozess in der Regel in wenigen Sekunden abgeschlossen ist.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer die entsprechende App Service-Instanz aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Neu starten** aus. Beim Neustarten einer App Service-Instanz wird automatisch das neueste Containerimage aus der Registrierung gepullt.

1. Navigieren Sie nach ungefähr 15–20 Sekunden erneut zu der App Service-URL, um die Änderungen zu überprüfen.

> [!div class="nextstepaction"]
> [Ich habe Änderungen vorgenommen und eine erneute Bereitstellung durchgeführt.](tutorial-deploy-containers-04.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=03-make-changes-redeploy)
