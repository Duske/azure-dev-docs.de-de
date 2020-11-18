---
title: Lokale Ausführung der Azure Functions-Anwendung in Visual Studio Code
description: Führen Sie das Azure Functions-Projekt lokal aus, um es vor der Bereitstellung in Azure zu testen. Legen Sie direkt vor der Antwortrückgabe durch die serverlose Funktion einen Haltepunkt fest.
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js, contperfq2
ms.openlocfilehash: 031c7a3d2d7ec6cf9c1afadf1d77d67d826853fd
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338546"
---
# <a name="3-test-the-function-locally"></a>3. Lokales Testen der Funktion

[Vorheriger Schritt: Erstellen der Funktions-App](tutorial-vscode-serverless-node-create-local.md)

In diesem Schritt führen Sie das Azure Functions-Projekt lokal aus, um es vor der Bereitstellung in Azure zu testen. Legen Sie direkt vor der Antwortrückgabe durch die serverlose Funktion einen Haltepunkt fest.

Beim Erstellen der Funktions-App wurde dem Projekt von der Azure Functions-Erweiterung automatisch eine VS Code-Startkonfiguration hinzugefügt (Datei *.vscode/launch.json*). Diese Konfiguration führt die gleiche Runtime aus wie Azure. Sie können daher vor der Bereitstellung in der Cloud sicher sein, dass Ihr Quellcode funktioniert.

## <a name="run-the-local-serverless-function"></a>Ausführen der lokalen serverlosen Funktion

1. Drücken Sie in Visual Studio Code **F5** (oder verwenden Sie den Menübefehl **Debuggen** > **Debuggen starten**), um den Debugger zu starten und an den Azure Functions-Host anzufügen. Dieser Befehl verwendet automatisch die von Azure Functions für Sie erstellte Debugkonfiguration.

1. Die Ausgabe der Functions Core Tools wird in VS Code im Bereich **Terminal** angezeigt. Nachdem der Host gestartet wurde, drücken Sie die **ALT-Taste**, und klicken Sie gleichzeitig auf die lokale URL in der Ausgabe, um den Browser zu öffnen und die Funktion auszuführen:

    ![Ausgabe im VS Code-Bereich „Terminal“ beim lokalen Debuggen](../media/functions-extension/local-test-output.png)

1. Der von der standardmäßigen HTTP-Triggervorlage erstellte Code analysiert einen `name`-Abfrageparameter, um die Antwort anzupassen. Fügen Sie in Ihrem Browser `?name=<yourname>` zur URL im Browser hinzu, um die Antwortausgabe korrekt anzuzeigen:

    ![Analyse der URL-Parameter durch die HTTP-Triggerfunktion](../media/functions-extension/local-test-browser.png)

## <a name="set-and-stop-at-break-point-in-serverless-app"></a>Festlegen eines Haltepunkts und Beenden beim Haltepunkt in einer serverlosen App

1. Bei der lokalen Ausführung Ihrer Funktion können Sie Breakpoints für verschiedene Teile des Codes festlegen. Öffnen Sie die Datei *index.js*, und klicken Sie dann im Editor-Fenster auf den Rand links neben Zeile 11. Es erscheint ein kleiner roter Punkt, der einen Breakpoint anzeigt. Entfernen Sie nun das `?name=`-Argument aus der URL im Browser. Wenn der Browser die Anforderung sendet, hält VS Code den Funktionscode an diesem Breakpoint an:

    ![An einem Breakpoint angehaltene Ausführung in VS Code](../media/functions-extension/debugging-breakpoint.png)

    Weitere Informationen zu Breakpoints und zum Debuggen in VS Code finden Sie unter [Debuggen](https://code.visualstudio.com/docs/editor/debugging).

> [!Note]
>
> Wenn bei diesem Vorgang ein Ausführungsrichtlinienfehler auftritt, deinstallieren Sie `azure-functions-core-tools@3` mit npm, und installieren Sie anschließend das Paket im Terminal mit erhöhten Rechten.

> [!div class="nextstepaction"]
> [Ich habe die Funktions-App lokal ausgeführt.](tutorial-vscode-serverless-node-deploy-hosting.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=run-app)
