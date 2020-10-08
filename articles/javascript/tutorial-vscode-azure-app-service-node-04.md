---
title: Streamen von Protokollen aus Azure App Service in Visual Studio Code
description: 'Teil 4 des Node.js-Tutorials: Anzeigen oder Anfügen von Protokollen'
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: eed2663f32571dc40fb402553424c1303d43f7b9
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365233"
---
# <a name="stream-logs-from-azure-app-service"></a>Streamen von Protokollen aus Azure App Service

[Vorheriger Schritt: Bereitstellen der Website](tutorial-vscode-azure-app-service-node-03.md)

In diesem Schritt erfahren Sie, wie Sie eine von der ausgeführten App generierte Ausgabe durch Aufrufe von `console.log` anzeigen oder „am Ende anfügen“. Diese Ausgabe wird im Fenster **Ausgabe** in Visual Studio Code angezeigt.

1. Klicken Sie im **Azure App Service**-Explorer mit der rechten Maustaste auf den Knoten der App, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

    ![Anzeigen von Streamingprotokollen](media/deploy-azure/start-streaming-logs.png)

1. Wenn Sie dazu aufgefordert werden, aktivieren Sie die Protokollierung, und starten Sie die Anwendung neu.

    ![Eingabeaufforderung zum Aktivieren der Protokollierung und Neustarten](media/deploy-azure/enable-restart.png)

1. Nachdem die App neu gestartet wurde, wird das Fenster **Ausgabe** in VS Code mit einer Verbindung mit dem Protokollstream geöffnet, der die Ausgabe zeigt.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Aktualisieren Sie die Webseite im Browser einige Male, um die zusätzliche Protokollausgabe anzuzeigen.

> [!div class="nextstepaction"]
> [Ich sehe die Protokolle.](tutorial-vscode-azure-app-service-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
