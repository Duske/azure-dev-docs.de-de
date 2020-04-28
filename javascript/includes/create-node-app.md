---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 6cb841a9cf9563c3b7b3db86d907eac757c5d31d
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072665"
---
1. Navigieren Sie in einer Terminaleingabeaufforderung zu einem Speicherort, an dem Sie den App-Ordner erstellen möchten.

1. Führen Sie den folgenden Befehl aus, um mit dem Express-Generator eine neue Express-App mit dem Namen `myexpressapp` zu erstellen. Die `--git --view pug`-Parameter weisen den Generator an, eine GITIGNORE-Datei zu erstellen und die [Pug](https://pugjs.org/api/getting-started.html)-Vorlagenengine zu verwenden, die früher als Jade bezeichnet wurde.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Wechseln Sie in den App-Ordner:

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

1. Testen Sie die App, indem Sie einem Browser öffnen und zu [http://localhost:3000](http://localhost:3000) navigieren. Die Website sollte wie hier dargestellt aussehen:

    ![Ausführen der Express-Anwendung](../media/deploy-azure/express.png)

1. Wählen Sie im Terminal **STRG**+**C** aus, um den Server zu beenden.
 
