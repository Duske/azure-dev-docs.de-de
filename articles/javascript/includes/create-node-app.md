---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: c95177d6b4cb101b764acd8f8dad54f937a495eb
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85792041"
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

1. Testen Sie die App, indem Sie einen Browser öffnen und zu `http://localhost:3000` navigieren. Die Website sollte wie hier dargestellt aussehen:

    ![Ausführen der Express-Anwendung](../media/deploy-azure/express.png)

1. Wählen Sie im Terminal **STRG**+**C** aus, um den Server zu beenden.
 
