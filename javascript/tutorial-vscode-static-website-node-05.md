---
title: Bereitstellen von Änderungen an einer statischen Node.js-Website in Visual Studio Code
description: 'Teil 5 des Tutorials: Vornehmen von Änderungen und erneutes Bereitstellen'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 986d2a0f8999d79dfd1d856ed20a053c495a3765
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685942"
---
# <a name="make-changes-and-redeploy"></a>Vornehmen von Änderungen und erneutes Bereitstellen

[Vorheriger Schritt: Bereitstellen in Azure Storage](tutorial-vscode-static-website-node-04.md)

In diesem Schritt nehmen Sie eine einfache Änderung am Quellcode der App vor und stellen die Website erneut bereit, um den End-to-End-Bereitstellungsworkflow kennen zu lernen.

1. Öffnen Sie in Visual Studio Code die Datei *src/app.js*, und ändern Sie Zeile 11 wie folgt:

    ```js
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung `npm run build` aus.

1. Klicken Sie in VS Code mit der rechten Maustaste auf den aktualisierten Ordner *build*, und wählen Sie erneut **Deploy to Static Website** (Auf statischer Website bereitstellen) aus. Wählen Sie Ihr Storage-Konto aus, und bestätigen Sie, dass Sie die Änderungen bereitstellen möchten. (Um Probleme beim Zwischenspeichern zu vermeiden, löscht die Azure-Erweiterung vor der Bereitstellung von Änderungen automatisch alte Dateien.)

1. Aktualisieren Sie nach Abschluss der Bereitstellung die Website im Browser, um die Änderungen anzuzeigen:

    ![Änderungen in der App nach der erneuten Bereitstellung](media/static-website/updated-azure-app.png)

> [!div class="nextstepaction"]
> [Ich habe Änderungen bereitgestellt.](tutorial-vscode-static-website-node-06.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
