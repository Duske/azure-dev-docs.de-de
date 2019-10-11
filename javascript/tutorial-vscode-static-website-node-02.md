---
title: Erstellen der statischen Node.js-App in Visual Studio Code
description: 'Teil 2 des Tutorials: Erstellen der Beispiel-App'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 566d166a69bbbee59726b8e381bee4a24077d8c6
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685976"
---
# <a name="create-the-app"></a>Erstellen der App

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-static-website-node-01.md)

In diesem Schritt erstellen Sie mit der Befehlszeilenschnittstelle (Command Linie Interface, CLI) des Hilfsprogramms React ([create-react-app](https://github.com/facebook/create-react-app)) eine einfache React-App, die in Azure bereitgestellt werden kann. Alternativ können Sie Angular, Vue, ein anderes Framework oder einen beliebigen Ordner verwenden, der einige HTML-Dateien enthält. Wenn Sie bereits eine App haben, die Sie bereitstellen möchten, können Sie diesen Teil überspringen und mit [Erstellen eines Azure Storage-Kontos](tutorial-vscode-static-website-node-03.md) fortfahren.

1. Führen Sie den folgenden Befehl aus, um mit dem Tool „create-react-app“ ein Gerüst für eine neue React-App namens `my-react-app` zu erstellen:

    ```bash
    npm create react-app my-react-app
    ```

1. Erstellen Sie die Anwendung, indem Sie zum neuen Ordner wechseln und `npm run build` ausführen:

    ```bash
    cd my-react-app
    npm run build
    ```

1. Sie sollten nun einen Ordner *build* im Ordner *my-react-app* haben. Der Ordner *build* enthält die HTML-, CSS- und JavaScript-Dateien, die Sie in Azure Storage bereitstellen.

1. Führen Sie die App mit dem folgenden Befehl aus:

    ```bash
    npm start
    ```

1. Öffnen Sie in einem Browser [http://localhost:3000](http://localhost:3000), um zu überprüfen, ob die App ausgeführt wird:

    ![Ausgeführte React-Beispiel-App](media/static-website/local-app.png)

1. Halten Sie den Server an, indem Sie im Terminal oder an der Eingabeaufforderung **STRG**+**C** drücken.

> [!div class="nextstepaction"]
> [Ich habe die App erstellt.](tutorial-vscode-static-website-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
