---
title: Erstellen der Azure App Service-Instanz in Visual Studio Code
description: 'Teil 2 des Tutorials: Erstellen der Node.js-App'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 96c786b7cc8112c36c0aff06761417a97e30bf44
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466798"
---
# <a name="create-your-nodejs-application"></a>Erstellen der Node.js-Anwendung

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-azure-app-service-node-01.md)

In diesem Schritt erstellen Sie mit dem Express-Anwendungsgenerator eine einfache Node.js-App, die Sie dann in Azure bereitstellen können.

Sie können auch die App aus dem [Node.js-Tutorial in Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) verwenden. Fahren Sie in diesem Fall direkt mit [Bereitstellen der App](tutorial-vscode-azure-app-service-node-03.md) fort.

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung den folgenden Befehl aus, um den Express-Generator auszuführen und das Gerüst für eine neue Express-App namens „myExpressApp“ zu erstellen. (Die `--view pug --git`-Parameter weisen den Generator an, die Vorlagenengine [pug](https://pugjs.org/api/getting-started.html) (früher als Jade bezeichnet) zu verwenden und eine *.gitignore*-Datei zu erstellen.)

    ```bash
    npx express-generator myExpressApp --view pug -–git
    ```

1. Installieren Sie die Abhängigkeiten der Anwendung, indem Sie den Befehl `npm install` im App-Ordner ausführen:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Führen Sie `npm start` aus, um den Server zu starten:

    ```bash
    npm start
    ```

1. Testen Sie die App, indem Sie [http://localhost:3000](http://localhost:3000) in einem Browser öffnen. Die Website sollte wie folgt angezeigt werden:

    ![Ausführen der Express-Anwendung](media/deploy-azure/express.png)

> [!div class="nextstepaction"]
> [Ich habe die Node.js-App erstellt.](tutorial-vscode-azure-app-service-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
