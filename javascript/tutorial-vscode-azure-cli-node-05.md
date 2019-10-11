---
title: Streamen von Protokollen aus Azure App Service
description: 'Teil 5 des Tutorials: Anzeigen von Protokollen'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: ce112d535d1f89a7e153f80b9fb80032a977b8d8
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686149"
---
# <a name="stream-logs-from-app-service"></a>Streamen von Protokollen aus App Service

[Vorheriger Schritt: Bereitstellen der App](tutorial-vscode-azure-cli-node-04.md)

In diesem Schritt zeigen Sie die Protokolle der ausgeführten App Service-Instanz an (bzw. Sie „fügen sie am Ende an“). Alle Aufrufe von `console.log` im Websitecode werden im Terminal angezeigt.

1. Führen Sie den folgenden Befehl aus, um die Protokollierung zu starten, und ersetzen Sie `<your_app_name>` durch den Namen Ihrer App Service-Instanz:

    ```bash
    az webapp log tail --name <your_app_name>
    ```

1. Nach einigen Sekunden sollte eine Meldung mit dem Hinweis angezeigt werden, dass eine Verbindung mit dem Protokollstreamingdienst hergestellt wurde.

    ```bash
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    ```

1. Aktualisieren Sie die Seite im Browser einige Male, um die zusätzliche Ausgabe zu generieren:

    ```bash
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    ```

1. Drücken Sie **STRG**+**C**, um die Protokollierungssitzung zu beenden.

> [!div class="nextstepaction"]
> [Ich sehe die Protokolle.](tutorial-vscode-azure-cli-node-06.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
