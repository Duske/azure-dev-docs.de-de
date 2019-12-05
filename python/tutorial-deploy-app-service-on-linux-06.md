---
title: 'Tutorial: Streamen von Protokollen aus Azure App Service in VS Code'
description: 'Tutorialschritt 6: Streamen von App-Protokollen in Visual Studio Code'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 56cbd468771af8d60fce20d88b8c984f95d4b9b9
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466068"
---
# <a name="tutorial-stream-logs-from-azure-app-service-into-visual-studio-code"></a>Tutorial: Streamen von Protokollen aus Azure App Service in Visual Studio Code

[Vorheriger Schritt: Bereitstellen der App](tutorial-deploy-app-service-on-linux-05.md)

Verwenden Sie dieses Verfahren, um Protokolle aus einer Azure App Service-Instanz in Visual Studio Code zu streamen.

1. Öffnen Sie in Visual Studio Code den **Azure App Service**-Explorer, klicken Sie mit der rechten Maustaste auf die App Service-Instanz, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

   ![„Start Streaming Logs“ (Streamen der Protokolle starten) im App Service-Explorer](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

1. Wenn Sie zum Aktivieren der Dateiprotokollierung und zum Neustart der Web-App aufgefordert werden, wählen Sie **Ja** aus Während des Neustarts der App wird der Fortschritt im **Ausgabefenster** in VS Code angezeigt. Das Aktivieren der Protokollierung ist ein einmaliger Vorgang.

1. Wenn die Protokollierung aktiviert ist, klicken Sie mit der rechten Maustaste auf die App Service-Instanz, und wählen Sie erneut **Start streaming logs** (Streamen der Protokolle starten) aus. Im **Ausgabefenster** in VS Code wird „Starting Live Log Stream“ (Liveprotokollstreaming wird gestartet) angezeigt. Anschließend beginnt die Anzeige der Protokollausgabe. Aktualisieren Sie die Web-App im Browser, um weitere Protokollinformationen zu generieren.

1. Um das Streamen der Protokolle zu beenden (ohne die Protokollierung zu deaktivieren), klicken Sie mit der rechten Maustaste auf die App im **Azure App Service**-Explorer, und wählen Sie **Streamingprotokolle beenden** aus.

> [!div class="nextstepaction"]
> [Die Protokolle werden angezeigt.](tutorial-deploy-app-service-on-linux-07.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
