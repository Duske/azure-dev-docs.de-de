---
title: Erstellen der Node.js-Azure App Service-Instanz in Visual Studio Code
description: 'Teil 2 des Tutorials: Erstellen und lokales Ausführen der Node.js-App'
ms.topic: conceptual
ms.date: 03/04/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 66299059f03219dc45ff5f2dbcb51e8b61144340
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218540"
---
# <a name="create-and-run-a-local-nodejs-app"></a>Erstellen und Ausführen einer lokalen Node.js-App

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-azure-app-service-node-01.md)

In diesem Schritt erstellen Sie mit dem Express-Anwendungsgenerator eine einfache Node.js-App. Sie führen die App anschließend lokal aus.

1. Navigieren Sie in einem Terminal oder an einer Eingabeaufforderung zu einem Speicherort, an dem Sie den App-Ordner erstellen möchten.

1. Führen Sie den folgenden Befehl aus, um mit dem Express-Generator eine neue Express-App mit dem Namen *expressApp1* zu erstellen. (Die `--view pug --git`-Parameter weisen den Generator an, die Vorlagenengine [pug](https://pugjs.org/api/getting-started.html) (früher als Jade bezeichnet) zu verwenden und eine *.gitignore*-Datei zu erstellen.)

    ```bash
    npx express-generator expressApp1 --view pug -–git
    ```

1. Navigieren Sie zum App-Ordner:

    ```bash
    cd expressApp1
    ```

1. Installieren Sie die Abhängigkeiten der Anwendung:

    ```bash
    npm install
    ```

1. Starten Sie den Server:

    ```bash
    npm start
    ```

1. Testen Sie die App, indem Sie `http://localhost:3000` in einem Browser öffnen. Die Website sollte wie folgt angezeigt werden:

    ![Ausführen der Express-Anwendung](media/deploy-azure/express.png)

1. Drücken Sie im Terminal **STRG**+**C**, um den Server zu beenden.

> [!div class="nextstepaction"]
> [Ich habe die Node.js-App erstellt](tutorial-vscode-azure-app-service-node-03.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
