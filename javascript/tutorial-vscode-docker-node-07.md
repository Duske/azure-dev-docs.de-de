---
title: Streamen von Protokollen aus einer Node.js-Container-App in Visual Studio Code
description: 'Teil 7 des Tutorials: Streamen von Protokollen in Visual Studio Code'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 10ccf13cddfc7bb1ed7f226629072cb9baeea3a1
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80740640"
---
# <a name="stream-logs-into-visual-studio-code"></a>Streamen von Protokollen in Visual Studio Code

[Vorheriger Schritt: Vornehmen von Änderungen und erneutes Bereitstellen](tutorial-vscode-docker-node-06.md)

In diesem Schritt erfahren Sie, wie Sie eine von der ausgeführten Website generierte Ausgabe durch Aufrufe von `console.log` anzeigen oder „am Ende anfügen“. Diese Ausgabe wird im Fenster **Ausgabe** in Visual Studio Code angezeigt.

1. Klicken Sie im **Azure App Service**-Explorer mit der rechten Maustaste auf den Knoten der App, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

    ![Anzeigen von Streamingprotokollen](media/deploy-containers/stream-logs-command.png)

1. Wenn Sie dazu aufgefordert werden, aktivieren Sie die Protokollierung, und starten Sie die Anwendung neu.

    ![Eingabeaufforderung zum Aktivieren der Protokollierung und Neustarten](media/deploy-azure/enable-restart.png)

1. Nachdem die App neu gestartet wurde, wird der Bereich **Ausgabe** in Visual Studio Code mit einer Verbindung mit dem Protokollstream geöffnet, der mit der Meldung `Starting Live Log Stream` beginnt.

> [!div class="nextstepaction"]
> [Ich sehe die Protokolle.](tutorial-vscode-docker-node-08.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
