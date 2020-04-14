---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 59eea52aab3f2b1941a3accb5848bc4ad92d2992
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740496"
---
1. Navigieren Sie in einem Terminal oder an einer Eingabeaufforderung zu einem Speicherort, an dem Sie den App-Ordner erstellen möchten.

1. Führen Sie den folgenden Befehl aus, um mit dem Express-Generator eine neue Express-App mit dem Namen *myexpressapp* zu erstellen. Durch die Parameter `--git --view pug` wird der Generator angewiesen, eine *GITIGNORE*-Datei zu erstellen und die [pug](https://pugjs.org/api/getting-started.html)-Vorlagen-Engine (ehemals Jade) zu verwenden.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Navigieren Sie zum App-Ordner:

    ```bash
    cd myexpressapp
    ```

1. Installieren Sie die Abhängigkeiten der Anwendung:

    ```bash
    npm install
    ```

1. Starten Sie den Server:

    ```bash
    npm start
    ```

1. Testen Sie die App, indem Sie [http://localhost:3000](http://localhost:3000) in einem Browser öffnen. Die Website sollte wie folgt angezeigt werden:

    ![Ausführen der Express-Anwendung](../media/deploy-azure/express.png)

1. Drücken Sie im Terminal **STRG**+**C**, um den Server zu beenden.
