---
title: 'Tutorial: Streamen von Protokollen aus Azure App Service in VS Code'
description: 'Tutorialschritt 6: Streamen von App-Protokollen in Visual Studio Code'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: f4aac132e9c01a0c428e243e06e811357defc2aa
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172233"
---
# <a name="tutorial-stream-logs-from-azure-app-service-into-visual-studio-code"></a>Tutorial: Streamen von Protokollen aus Azure App Service in Visual Studio Code

[Vorheriger Schritt: Bereitstellen der App](tutorial-deploy-app-service-on-linux-05.md)

1. Öffnen Sie in Visual Studio Code den **Azure App Service**-Explorer, klicken Sie mit der rechten Maustaste auf die App Service-Instanz, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

   ![Befehl zum Starten des Streamings der Protokolle](media/deploy-azure/start-streaming-logs-command.png)

1. Wenn Sie zum Aktivieren der Dateiprotokollierung und zum Neustart der Web-App aufgefordert werden, wählen Sie **Ja** aus Während des Neustarts der App wird der Fortschritt im **Ausgabefenster** in VS Code angezeigt. Das Aktivieren der Protokollierung ist ein einmaliger Vorgang.

1. Wenn die Protokollierung aktiviert ist, klicken Sie mit der rechten Maustaste auf die App Service-Instanz, und wählen Sie erneut **Start streaming logs** (Streamen der Protokolle starten) aus. Im **Ausgabefenster** in VS Code wird „Starting Live Log Stream“ (Liveprotokollstreaming wird gestartet) angezeigt. Anschließend beginnt die Anzeige der Protokollausgabe. Aktualisieren Sie die Web-App im Browser, um weitere Protokollinformationen zu generieren.

1. Um das Streamen der Protokolle zu beenden (ohne die Protokollierung zu deaktivieren), klicken Sie mit der rechten Maustaste auf die App im **Azure App Service**-Explorer, und wählen Sie **Streamingprotokolle beenden** aus.

> [!div class="nextstepaction"]
> [Die Protokolle werden angezeigt.](tutorial-deploy-app-service-on-linux-07.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
