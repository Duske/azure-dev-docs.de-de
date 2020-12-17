---
title: Bereitstellen von Änderungen an einer statischen Node.js-Website in Visual Studio Code
description: 'Teil 5 des Tutorials zu statischen Web-Apps: Vornehmen von Änderungen und erneutes Bereitstellen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 73ec6d03bce6b92c473e9d5271180ce137bd7e72
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609324"
---
# <a name="part-5-make-changes-and-redeploy"></a>Teil 5: Vornehmen von Änderungen und erneutes Bereitstellen

[Vorheriger Schritt: Bereitstellen in Azure Storage](tutorial-vscode-static-website-node-04.md)

In diesem Schritt nehmen Sie eine einfache Änderung am Quellcode der App vor und stellen die Website erneut bereit, um den End-to-End-Bereitstellungsworkflow kennen zu lernen.

# <a name="angular"></a>[Angular](#tab/angular)

1. Öffnen Sie in Visual Studio Code die Datei _src/app/app.component.html_, und ändern Sie Zeile 305 wie folgt:

    ```html
    <span>Welcome To Azure</span>
    ```

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung `npm run build` aus.

1. Klicken Sie in VS Code mit der rechten Maustaste auf den aktualisierten Ordner _dist/my-static-site_, und wählen Sie erneut **Deploy to Static Website** (Auf statischer Website bereitstellen) aus. Wählen Sie Ihr Storage-Konto aus, und bestätigen Sie, dass Sie die Änderungen bereitstellen möchten. (Um Probleme beim Zwischenspeichern zu vermeiden, löscht die Azure-Erweiterung vor der Bereitstellung von Änderungen automatisch alte Dateien.)

1. Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen:

    ![Angular: Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen.](../../media/static-website/updated-azure-app-angular.png)

# <a name="react"></a>[React](#tab/react)

1. Öffnen Sie in Visual Studio Code die Datei _src/app.js_, und ändern Sie Zeile 11 wie folgt:

    ```html
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung `npm run build` aus.

1. Klicken Sie in VS Code mit der rechten Maustaste auf den aktualisierten Ordner _build_, und wählen Sie erneut **Deploy to Static Website** (Auf statischer Website bereitstellen) aus. Wählen Sie Ihr Storage-Konto aus, und bestätigen Sie, dass Sie die Änderungen bereitstellen möchten. (Um Probleme beim Zwischenspeichern zu vermeiden, löscht die Azure-Erweiterung vor der Bereitstellung von Änderungen automatisch alte Dateien.)

1. Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen:

    ![React: Änderungen in der App nach der erneuten Bereitstellung](../../media/static-website/updated-azure-app-react.png)

# <a name="vue"></a>[Vue](#tab/vue)

1. Öffnen Sie in Visual Studio Code die Datei _src/App.vue_, und ändern Sie Zeile 11 wie folgt:

    ```html
    <HelloWorld msg="Welcome to Azure!" />
    ```

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung `npm run build` aus.

1. Klicken Sie in VS Code mit der rechten Maustaste auf den aktualisierten Ordner _dist_, und wählen Sie erneut **Deploy to Static Website** (Auf statischer Website bereitstellen) aus. Wählen Sie Ihr Storage-Konto aus, und bestätigen Sie, dass Sie die Änderungen bereitstellen möchten. (Um Probleme beim Zwischenspeichern zu vermeiden, löscht die Azure-Erweiterung vor der Bereitstellung von Änderungen automatisch alte Dateien.)

1. Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen:

    ![Vue: Änderungen in der App nach der erneuten Bereitstellung](../../media/static-website/updated-azure-app-vue.png)

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Öffnen Sie in Visual Studio Code die Datei _src/main.js_, und ändern Sie Zeile 6 wie folgt:

    ```js
    import App from './App.svelte';

    const app = new App({
        target: document.body,
        props: {
            name: 'Welcome to Azure!'
        }
    });

    export default app;
    ```

2. Öffnen Sie nun die Datei _src/App.svelte_, und ändern Sie Zeile 6 wie folgt:

    ```html
    <h1>{name}</h1>
    ```

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung `npm run build` aus.

1. Klicken Sie in VS Code mit der rechten Maustaste auf den aktualisierten Ordner _public_, und wählen Sie erneut **Deploy to Static Website** (Auf statischer Website bereitstellen) aus. Wählen Sie Ihr Storage-Konto aus, und bestätigen Sie, dass Sie die Änderungen bereitstellen möchten. (Um Probleme beim Zwischenspeichern zu vermeiden, löscht die Azure-Erweiterung vor der Bereitstellung von Änderungen automatisch alte Dateien.)

1. Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen:

    ![Svelte: Änderungen in der App nach der erneuten Bereitstellung](../../media/static-website/updated-azure-app-svelte.png)

---

> [!div class="nextstepaction"]
> [Ich habe Änderungen bereitgestellt.](tutorial-vscode-static-website-node-06.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
