---
title: 'Schritt 6: Streamen von Protokollen aus Azure App Service in VS Code'
description: 'Tutorialschritt 6: Streamen von App-Protokollen in Visual Studio Code'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 01df1219302f4edf21845c999337354a065352ac
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485679"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6: Streamen von Protokollen aus Azure App Service in Visual Studio Code

[Vorheriger Schritt: Bereitstellen der App](tutorial-deploy-app-service-on-linux-05.md)

Verwenden Sie dieses Verfahren, um Protokolle aus einer Azure App Service-Instanz in Visual Studio Code zu streamen.

1. Öffnen Sie in Visual Studio Code den **Azure App Service**-Explorer, klicken Sie mit der rechten Maustaste auf die App Service-Instanz, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

   ![„Start Streaming Logs“ (Streamen der Protokolle starten) im App Service-Explorer](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

   Wenn Sie zum Aktivieren der Dateiprotokollierung und zum Neustart der Web-App aufgefordert werden, wählen Sie **Ja** aus. Während des Neustarts der App wird der Fortschritt im **Ausgabefenster** in VS Code angezeigt. Wählen Sie nach dem Neustart der App erneut den Befehl **Start Streaming Logs** (Streamen der Protokolle starten) aus. Das Aktivieren der Protokollierung ist ein einmaliger Vorgang.

1. Im **Ausgabefenster** in VS Code wird „Starting Live Log Stream“ (Liveprotokollstreaming wird gestartet) angezeigt. Anschließend beginnt die Anzeige der Protokollausgabe. Aktualisieren Sie die Web-App im Browser, um weitere Protokollinformationen zu generieren.

1. Um das Streamen der Protokolle zu beenden (ohne die Protokollierung zu deaktivieren), klicken Sie mit der rechten Maustaste auf die App im **Azure App Service**-Explorer, und wählen Sie **Streamingprotokolle beenden** aus.

> [!div class="nextstepaction"]
> [Die Protokolle werden angezeigt: Fahren Sie mit Schritt 7 fort. >>>](tutorial-deploy-app-service-on-linux-07.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
