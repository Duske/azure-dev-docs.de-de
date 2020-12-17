---
title: Erstellen der statischen Node.js-App in Visual Studio Code
description: 'Teil 2 des Tutorials zu statischen Web-Apps: Erstellen der Beispiel-App'
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 93698a7a79c883cef1ad6d98979c5e46bad2a29f
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609365"
---
# <a name="create-the-app"></a>Erstellen der App

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-static-website-node-01.md)

In diesem Schritt verwenden Sie die Befehlszeilenschnittstelle (Command Line Interface, CLI) für [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app), [Vue](https://cli.vuejs.org/) oder [Svelte](https://github.com/sveltejs/template), um eine einfache App zu erstellen, die in Azure bereitgestellt werden kann. Alternativ können Sie ein beliebiges anderes JavaScript-Framework, das statische Dateien generiert, oder einen Ordner verwenden, der HTML-, CSS- oder JavaScript-Dateien enthält. Wenn Sie bereits eine App haben, die Sie bereitstellen möchten, können Sie diesen Teil überspringen und mit [Erstellen eines Azure Storage-Kontos](tutorial-vscode-static-website-node-03.md) fortfahren.

# <a name="angular"></a>[Angular](#tab/angular)

1. Erstellen Sie mit der CLI ein Gerüst für eine neue App namens „my-static-app“. Führen Sie dazu den folgenden Befehl aus:

    ```bash
    npx @angular/cli new my-static-app
    ```

    Werden Sie von der CLI zur Eingabe von Konfigurationswerten aufgefordert, drücken Sie die EINGABETASTE, um die Standardoptionen auszuwählen.

1. Erstellen Sie die Anwendung, indem Sie zum neuen Ordner wechseln und `npm run build` ausführen:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Sie sollten nun einen Ordner _dist_ im Ordner _my-static-app_ haben. Innerhalb des Ordners _dist_ befindet sich ein Ordner mit dem gleichen Namen wie Ihr Projekt: _my-static-app_. Der Ordner _build/my-static-app_ enthält die HTML-, CSS- und JavaScript-Dateien, die Sie in Azure Storage bereitstellen.

1. Führen Sie die App mit dem folgenden Befehl aus:

    ```bash
    npm start
    ```

1. Öffnen Sie in einem Browser `http://localhost:4200`, um zu überprüfen, ob die App ausgeführt wird:

    ![Ausgeführte Angular-Beispiel-App](../../media/static-website/local-app-angular.png)

1. Halten Sie den Server an, indem Sie im Terminal oder an der Eingabeaufforderung **STRG**+**C** drücken.

# <a name="react"></a>[React](#tab/react)

1. Erstellen Sie mit der CLI ein Gerüst für eine neue App namens „my-static-app“. Führen Sie dazu den folgenden Befehl aus:

    ```bash
    npx create-react-app my-static-app
    ```

1. Erstellen Sie die Anwendung, indem Sie zum neuen Ordner wechseln und `npm run build` ausführen:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Sie sollten nun einen Ordner _build_ im Ordner _my-static-app_ haben. Der Ordner _build_ enthält die HTML-, CSS- und JavaScript-Dateien, die Sie in Azure Storage bereitstellen.

1. Führen Sie die App mit dem folgenden Befehl aus:

    ```bash
    npm start
    ```

1. Öffnen Sie in einem Browser `http://localhost:3000`, um zu überprüfen, ob die App ausgeführt wird:

    ![Ausgeführte React-Beispiel-App](../../media/static-website/local-app-react.png)

1. Halten Sie den Server an, indem Sie im Terminal oder an der Eingabeaufforderung **STRG**+**C** drücken.

# <a name="vue"></a>[Vue](#tab/vue)

1. Erstellen Sie mit der CLI ein Gerüst für eine neue App namens „my-static-app“. Führen Sie dazu den folgenden Befehl aus:

    ```bash
    npx @vue/cli create my-static-app
    ```

Werden Sie von der CLI zur Eingabe von Konfigurationswerten aufgefordert, drücken Sie die EINGABETASTE, um die Standardoptionen auszuwählen.

1. Erstellen Sie die Anwendung, indem Sie zum neuen Ordner wechseln und `npm run build` ausführen:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Sie sollten nun einen Ordner _dist_ im Ordner _my-static-app_ haben. Der Ordner _dist_ enthält die HTML-, CSS- und JavaScript-Dateien, die Sie in Azure Storage bereitstellen.

1. Führen Sie die App mit dem folgenden Befehl aus:

     ```bash
     npm run serve
     ```

1. Öffnen Sie in einem Browser `http://localhost:8080`, um zu überprüfen, ob die App ausgeführt wird:

    ![Ausgeführte Vue-Beispiel-App](../../media/static-website/local-app-vue.png)

1. Halten Sie den Server an, indem Sie im Terminal oder an der Eingabeaufforderung **STRG**+**C** drücken.

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Erstellen Sie mit der CLI ein Gerüst für eine neue App namens „my-static-app“. Führen Sie dazu den folgenden Befehl aus:

    ```bash
    npx degit sveltejs/template my-static-app
    ```

1. Wechseln Sie anschließend zum neuen Ordner, und führen Sie den Befehl `npm install` aus:

    ```bash
    cd my-static-app
    npm install
    ```

1. Erstellen Sie die Anwendung mit dem Befehl `npm run build`:

    ```bash
    npm run build
    ```

1. Der Ordner _public_ sollte jetzt den Ordner _build_ enthalten. Der Ordner _build_ enthält die HTML-, CSS- und JavaScript-Dateien, die Sie in Azure Storage bereitstellen.

1. Führen Sie die App mit dem folgenden Befehl aus:

     ```bash
     npm run dev
     ```

1. Öffnen Sie in einem Browser `http://localhost:5000`, um zu überprüfen, ob die App ausgeführt wird:

    ![Ausgeführte Svelte-Beispiel-App](../../media/static-website/local-app-svelte.png)

1. Halten Sie den Server an, indem Sie im Terminal oder an der Eingabeaufforderung **STRG**+**C** drücken.

---

> [!div class="nextstepaction"]
> [Ich habe die App erstellt.](tutorial-vscode-static-website-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
