---
title: Streamen von Protokollen aus einer Node.js-Container-App in Visual Studio Code
description: 'Teil 5 des Tutorials: Streamen von Protokollen in Visual Studio Code'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2ac930996bd910014565c4e329bec93015bd2a3a
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466519"
---
# <a name="stream-logs-into-visual-studio-code"></a>Streamen von Protokollen in Visual Studio Code

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-docker-node-05.md)

In diesem Schritt erfahren Sie, wie Sie eine von der ausgeführten Website generierte Ausgabe durch Aufrufe von `console.log` anzeigen oder „am Ende anfügen“. Diese Ausgabe wird im Fenster **Ausgabe** in Visual Studio Code angezeigt.

1. Klicken Sie im **Azure App Service**-Explorer mit der rechten Maustaste auf den Knoten der App, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

    ![Anzeigen von Streamingprotokollen](media/deploy-containers/stream-logs-command.png)

1. Wenn Sie dazu aufgefordert werden, aktivieren Sie die Protokollierung, und starten Sie die Anwendung neu.

    ![Eingabeaufforderung zum Aktivieren der Protokollierung und Neustarten](media/deploy-azure/enable-restart.png)

1. Nachdem die App neu gestartet wurde, wird der Bereich **Ausgabe** in Visual Studio Code mit einer Verbindung mit dem Protokollstream geöffnet, der mit der Meldung `Starting Live Log Stream` beginnt.

> [!div class="nextstepaction"]
> [Ich sehe die Protokolle.](tutorial-vscode-docker-node-07.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
