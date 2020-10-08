---
title: Streamen von Protokollen aus Azure App Service
description: 'Teil 5 des Azure CLI-Tutorials: Anzeigen von Protokollen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 0cb28cc7bafd7d0d713fc980c72a7a41474d5060
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365143"
---
# <a name="stream-logs-from-app-service"></a>Streamen von Protokollen aus App Service

[Vorheriger Schritt: Bereitstellen der App](tutorial-vscode-azure-cli-node-04.md)

In diesem Schritt zeigen Sie die Protokolle der ausgeführten App Service-Instanz an (bzw. Sie „fügen sie am Ende an“). Alle Aufrufe von `console.log` im Websitecode werden im Terminal angezeigt.

1. Führen Sie den folgenden Befehl aus, um die Protokollierung zu starten, und ersetzen Sie `<your_app_name>` durch den Namen Ihrer App Service-Instanz:

    ```azurecli
    az webapp log tail --name <your_app_name>
    ```

1. Nach einigen Sekunden sollte in der Ausgabe eine Meldung mit dem Hinweis angezeigt werden, dass eine Verbindung mit dem Protokollstreamingdienst hergestellt wurde.

    <pre>
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Aktualisieren Sie die Seite im Browser einige Male, um die zusätzliche Ausgabe zu generieren:

    <pre>
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    </pre>

1. Drücken Sie **STRG**+**C**, um die Protokollierungssitzung zu beenden.

> [!div class="nextstepaction"]
> [Ich sehe die Protokolle.](tutorial-vscode-azure-cli-node-06.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
