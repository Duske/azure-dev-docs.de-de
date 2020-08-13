---
title: 'Schritt 3: Erneutes Bereitstellen eines Containers in Azure App Service, nachdem Änderungen in Visual Studio Code vorgenommen wurden'
description: 'Tutorialschritt 3: einfache Schritte zum erneuten Erstellen und Bereitstellen eines Containerimages'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: c23676dd0d8b3741881f38d1a3bbee367f7d6c58
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982882"
---
# <a name="2-redeploy-a-container-to-azure-app-service-after-making-changes"></a>2: Erneutes Bereitstellen eines Containers in Azure App Service, nachdem Änderungen vorgenommen wurden

[Vorheriger Schritt: Bereitstellen des Images in Azure](tutorial-deploy-containers-02.md)

In diesem Artikel erfahren Sie, wie Sie einen Container erneut in Azure App Service bereitstellen, nachdem Änderungen in Visual Studio Code vorgenommen wurden.

Da Sie unweigerlich Änderungen an Ihrer App vornehmen, müssen Sie den Container mehrmals neu erstellen und neu bereitstellen. Glücklicherweise ist dieser Prozess ganz einfach:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor, und testen Sie sie. (Dieser Schritt und die beiden folgenden Schritte werden im Tutorial [Erstellen eines Python-Containers in VS Code](https://code.visualstudio.com/docs/python/tutorial-create-containers) erläutert.)

1. Erstellen Sie das Docker-Image neu. Wenn Sie nur App-Code ändern, sollte der Build nur einige Sekunden dauern.

1. Pushen Sie Ihr Image in die Registrierung. Wenn Sie nur App-Code ändern, muss nur diese kleine Ebene gepusht werden, sodass der Prozess in der Regel in wenigen Sekunden abgeschlossen ist.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer die entsprechende App Service-Instanz aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Neu starten** aus. Beim Neustarten einer App Service-Instanz wird automatisch das neueste Containerimage aus der Registrierung gepullt.

1. Navigieren Sie nach ungefähr 15–20 Sekunden erneut zu der App Service-URL, um die Änderungen zu überprüfen.

> [!div class="nextstepaction"]
> [Ich habe Änderungen vorgenommen und eine erneute Bereitstellung durchgeführt: Fahren Sie mit Schritt 4 fort. >>>](tutorial-deploy-containers-04.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=03-make-changes-redeploy)
