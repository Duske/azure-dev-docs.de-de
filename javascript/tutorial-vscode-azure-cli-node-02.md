---
title: Erstellen einer Node.js-App zur Bereitstellung in Azure mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 2 des Tutorials: Erstellen des App-Codes.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: a39e187db3feb165cfa469176adbcfcab2c6a886
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686172"
---
# <a name="create-the-app-code-using-express"></a>Erstellen des App-Codes mit Express

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-azure-cli-node-01.md)

In diesem Schritt erstellen Sie mithilfe des [Express-Generators](https://expressjs.com/en/starter/generator.html) eine einfache Node.js-App mit [Express](https://www.expressjs.com).

1. Verwenden Sie den folgenden Befehl, um den Express-Generator auszuführen und das Gerüst für eine neue Express-App namens „myExpressApp“ zu erstellen. (Die `--view pug --git`-Parameter weisen den Generator an, die Vorlagenengine [pug](https://pugjs.org/api/getting-started.html) (früher als Jade bezeichnet) zu verwenden und eine *.gitignore*-Datei zu erstellen.)

    ```bash
    npx express-generator myExpressApp --view pug –git
    ```

1. Navigieren Sie zum App-Ordner, und installieren Sie die Abhängigkeiten der App, indem Sie die folgenden Befehle ausführen:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Führen Sie den folgenden Befehl aus, um den App-Server zu starten:

    ```bash
    npm start
    ```

1. Öffnen Sie in einem Browser [http://localhost:3000](http://localhost:3000), um die ausgeführte App anzuzeigen:

    ![Lokales Ausführen der Express-App](media/azure-cli/local-app.png)

1. Wenn Sie mit dem Testen der App fertig sind, halten Sie den Server an, indem Sie im Terminal, in dem Sie `npm start` ausgeführt haben, **STRG**+**C** drücken.

> [!div class="nextstepaction"]
> [Ich habe die App erstellt.](tutorial-vscode-azure-cli-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=express)
