---
title: Streamen von Protokollen aus Azure App Service für einen Container in Visual Studio Code
description: 'Tutorialschritt 4: Anzeigen von Protokollen aus Azure App Service zum Überwachen des Verhaltens'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: b886eee84e6e8daef772c2ba6e7290a604d18409
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019508"
---
# <a name="stream-logs-from-azure-app-service"></a>Streamen von Protokollen aus Azure App Service

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-deploy-containers-03.md)

Sie können in VS Code Protokolle von der ausgeführten Website in Azure App Service anzeigen (nachverfolgen). Dabei werden sämtliche Ausgaben an der Konsole wie die von `print`-Anweisungen erfasst und an den **Ausgabebereich** von VS Code weitergeleitet.

1. Suchen Sie die App im **Azure App Service**-Explorer, klicken Sie mit der rechten Maustaste auf die App, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

1. Antworten Sie mit **Ja**, wenn Sie dazu aufgefordert werden, die Protokollierung zu aktivieren und die App neu zu starten. Nachdem die App neu gestartet wurde, wird der Ausgabebereich von VS Code mit einer Verbindung mit dem Protokollstream geöffnet.

1. Nach einigen Sekunden wird eine Meldung angezeigt, in der Sie darüber informiert werden, dass eine Verbindung mit dem Protokollstreamingdienst hergestellt wurde.

    ```bash
    Connecting to log stream...
    2018-09-27T20:14:26  Welcome, you are now connected to log-streaming service.

    2018-09-27 20:14:59.269 INFO  - Starting container for site

    2018-09-27 20:14:59.270 INFO  - docker run -d -p 24138:8000 --name vsdocs-django-sample-container_0 -e WEBSITES_PORT=8000 -e WEBSITE_SITE_NAME=vsdocs-django-sample-container -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_INSTANCE_ID=02c705ae24eaf5f298e553a9c2724b9fe4485707c2d1c36137cd02931091e561 -e HTTP_LOGGING_ENABLED=1 vsdocsregistry.azurecr.io/python-sample-vscode-django-tutorial:latest

    2018-09-27 20:15:06.216 INFO  - Container vsdocs-django-sample-container_0 for site vsdocs-django-sample-container initialized successfully.
    ```

1. Navigieren Sie in der App, um weitere Ausgaben für verschiedene HTTP-Anforderungen anzuzeigen.

> [!div class="nextstepaction"]
> [Die Protokolle werden angezeigt.](tutorial-deploy-containers-05.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=04-stream-logs)
