---
title: Bereitstellen der Dateien der statischen Node.js-App in Azure Storage in Visual Studio Code
description: 'Teil 4 des Tutorials: Bereitstellen der Dateien in Azure Storage'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: b1295fcb9a90ca26880715296e4214c2a1df6a07
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685953"
---
# <a name="deploy-the-website-to-azure-storage"></a>Bereitstellen der Website in Azure Storage

[Vorheriger Schritt: Erstellen eines Storage-Kontos](tutorial-vscode-static-website-node-03.md)

In diesem Schritt stellen Sie die in den vorherigen Schritten erstellten Dateien der statischen Website mithilfe von Visual Studio Code im Azure Storage-Dienst bereit, der diese Dateien dann hostet und verarbeitet.

1. Wechseln Sie in Visual Studio Code zum **Azure Storage**-Explorer, erweitern Sie Ihr Abonnement, den Knoten für das Azure Storage-Konto, das Sie im vorherigen Schritt erstellt haben, und dann den Knoten **Blobcontainer**. Im Container `$web` stellen Sie Ihren App-Code bereit.

    ![Azure Storage-Knoten im Azure Storage-Explorer](media/static-website/storage-nodes.png)

1. Wählen Sie den **Datei**-Explorer aus, klicken Sie mit der rechten Maustaste auf Ihren Ordner *build*, und wählen Sie **Deploy to Static Website** (Auf statischer Website bereitstellen) aus:

    ![Befehl „Deploy to Static Website“ (Auf statischer Website bereitstellen)](media/static-website/deploy-build.png)

1. Wählen Sie das zuvor erstellte Storage-Konto aus, wenn Sie zur Eingabe aufgefordert werden.

1. Wenn die Bereitstellung abgeschlossen ist, wird eine Meldung mit einer Schaltfläche **Browse to website** (Zur Website wechseln) angezeigt. Klicken Sie auf diese Schaltfläche, um den primären Endpunkt des bereitgestellten App-Codes zu öffnen.

    ![Meldung beim Abschluss der Bereitstellung](media/static-website/deployment-complete.png)

    ![In Azure ausgeführte statische Website](media/static-website/azure-app.png)

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt.](tutorial-vscode-static-website-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
