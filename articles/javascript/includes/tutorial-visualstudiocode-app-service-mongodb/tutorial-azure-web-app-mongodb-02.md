---
title: include file tutorial-azure-web-app-mongodb-02.md
description: include file tutorial-azure-web-app-mongodb-02.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: fafb2b452e9e1bfab34d9c2d9d46adaabf4fd022
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183851"
---
In diesem Abschnitt des Tutorials laden Sie die Beispielanwendung auf Ihren lokalen Computer herunter und führen sie über das Visual Studio Code-Terminal aus. Anschließend können Sie die lokal ausgeführte App in Ihrem Browser anzeigen. 

## <a name="download-and-run-the-initial-expressjs-app"></a>Herunterladen und Ausführen der anfänglichen Express.js-App

Die anfängliche Express.js-Web-App wird als Ausgangspunkt bereitgestellt. In diesem Verfahren laden Sie die App herunter, installieren die Abhängigkeiten und führen die App aus. Die anfängliche App versucht, eine Verbindung mit einer Datenbank herzustellen, wenn verfügbar. Wenn sie nicht verfügbar ist, antwortet die Website trotzdem erfolgreich auf eine Anforderung. 

1. [Laden Sie das gezippte GitHub-Repository](https://github.com/Azure-Samples/js-e2e-express-mongo.git) auf Ihren lokalen Computer herunter, und erweitern Sie es dann zu einem Ordner. 
1. Öffnen Sie den Ordner mit Visual Studio Code. Sie können entweder mit der rechten Maustaste auf den Ordner klicken und **Mit Code öffnen** auswählen oder die CLI-Entsprechung verwenden, wenn Sie sich im Ordner befinden:

    ```console
    code .
    ```

1. Öffnen Sie in Visual Studio Code ein Terminalfenster, und führen Sie den folgenden Befehl aus, um die Abhängigkeiten des Beispiels zu installieren.

    ```javascript
    npm install
    ```

1. Führen Sie im gleichen Terminalfenster den Befehl zum Ausführen der Web-App aus.

    ```javascript
    npm start
    ```

1. Öffnen Sie einen Webbrowser, und verwenden Sie die folgende URL, um die Web-App auf Ihrem lokalen Computer anzuzeigen.

    ```url
    http://localhost:8080/
    ```

    Wenn die einfache Web-App in Ihrem Browser mit dem Text angezeigt wird, dass die Datenbank nicht gefunden wurde, haben Sie diesen Abschnitt des Tutorials erfolgreich durchgearbeitet.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::