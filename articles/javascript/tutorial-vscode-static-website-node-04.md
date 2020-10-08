---
title: Bereitstellen der Dateien der statischen Node.js-App in Azure Storage in Visual Studio Code
description: 'Teil 4 des Tutorials zu statischen Web-Apps: Bereitstellen der Dateien in Azure Storage'
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: e7c1ca3390f8161c7323103868c591e8c02f539e
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365293"
---
# <a name="deploy-the-website-to-azure-storage"></a>Bereitstellen der Website in Azure Storage

[Vorheriger Schritt: Erstellen eines Storage-Kontos](tutorial-vscode-static-website-node-03.md)

In diesem Schritt stellen Sie die in den vorherigen Schritten erstellten Dateien der statischen Website mithilfe von Visual Studio Code im Azure Storage-Dienst bereit, der diese Dateien dann hostet und verarbeitet.

# <a name="angular"></a>[Angular](#tab/angular)

1. Wechseln Sie in Visual Studio Code zum **Azure Storage**-Explorer, erweitern Sie Ihr Abonnement, den Knoten für das Azure Storage-Konto, das Sie im vorherigen Schritt erstellt haben, und dann den Knoten **Blobcontainer**. Im Container `$web` stellen Sie Ihren App-Code bereit.

   ![Azure Storage-Knoten im Azure Storage-Explorer](media/static-website/storage-nodes.png)

1. Wählen Sie den **Datei**-Explorer aus, klicken Sie mit der rechten Maustaste auf Ihren Ordner _dist/my-static-app_, und wählen Sie **Deploy to Static Website** (Auf statischer Website bereitstellen) aus:

    ![Angular: Befehl „Deploy to Static Website“ (Auf statischer Website bereitstellen)](media/static-website/deploy-build-angular.png)

1. Wählen Sie das zuvor erstellte Storage-Konto aus, wenn Sie zur Eingabe aufgefordert werden.

1. Wenn die Bereitstellung abgeschlossen ist, wird eine Meldung mit einer Schaltfläche **Browse to website** (Zur Website wechseln) angezeigt. Klicken Sie auf diese Schaltfläche, um den primären Endpunkt des bereitgestellten App-Codes zu öffnen.

    ![Angular: Meldung bei Abschluss der Bereitstellung](media/static-website/deployment-complete.png)

    ![Angular: In Azure ausgeführte statische Website](media/static-website/azure-app-angular.png)

# <a name="react"></a>[React](#tab/react)

1. Wechseln Sie in Visual Studio Code zum **Azure Storage**-Explorer, erweitern Sie Ihr Abonnement, den Knoten für das Azure Storage-Konto, das Sie im vorherigen Schritt erstellt haben, und dann den Knoten **Blobcontainer**. Im Container `$web` stellen Sie Ihren App-Code bereit.

   ![React: Azure Storage-Knoten im Azure Storage-Explorer](media/static-website/storage-nodes.png)

1. Wählen Sie den **Datei**-Explorer aus, klicken Sie mit der rechten Maustaste auf Ihren Ordner _build_, und wählen Sie **Deploy to Static Website** (Auf statischer Website bereitstellen) aus:

    ![React: Befehl „Deploy to Static Website“ (Auf statischer Website bereitstellen)](media/static-website/deploy-build-react.png)

1. Wählen Sie das zuvor erstellte Storage-Konto aus, wenn Sie zur Eingabe aufgefordert werden.

1. Wenn die Bereitstellung abgeschlossen ist, wird eine Meldung mit einer Schaltfläche **Browse to website** (Zur Website wechseln) angezeigt. Klicken Sie auf diese Schaltfläche, um den primären Endpunkt des bereitgestellten App-Codes zu öffnen.

    ![React: Meldung bei Abschluss der Bereitstellung](media/static-website/deployment-complete.png)

    ![React: In Azure ausgeführte statische Website](media/static-website/azure-app-react.png)

# <a name="vue"></a>[Vue](#tab/vue)

1. Wechseln Sie in Visual Studio Code zum **Azure Storage**-Explorer, erweitern Sie Ihr Abonnement, den Knoten für das Azure Storage-Konto, das Sie im vorherigen Schritt erstellt haben, und dann den Knoten **Blobcontainer**. Im Container `$web` stellen Sie Ihren App-Code bereit.

   ![Vue: Azure Storage-Knoten im Azure Storage-Explorer](media/static-website/storage-nodes.png)

1. Wählen Sie den **Datei**-Explorer aus, klicken Sie mit der rechten Maustaste auf Ihren Ordner _dist_, und wählen Sie **Deploy to Static Website** (Auf statischer Website bereitstellen) aus:

    ![Vue: Befehl „Deploy to Static Website“ (Auf statischer Website bereitstellen)](media/static-website/deploy-build-vue.png)

1. Wählen Sie das zuvor erstellte Storage-Konto aus, wenn Sie zur Eingabe aufgefordert werden.

1. Wenn die Bereitstellung abgeschlossen ist, wird eine Meldung mit einer Schaltfläche **Browse to website** (Zur Website wechseln) angezeigt. Klicken Sie auf diese Schaltfläche, um den primären Endpunkt des bereitgestellten App-Codes zu öffnen.

    ![Vue: Meldung bei Abschluss der Bereitstellung](media/static-website/deployment-complete.png)

    ![Vue: In Azure ausgeführte statische Website](media/static-website/azure-app-vue.png)

# <a name="svelte"></a>[Svelte](#tab/svelte)

1. Wechseln Sie in Visual Studio Code zum **Azure Storage**-Explorer, erweitern Sie Ihr Abonnement, den Knoten für das Azure Storage-Konto, das Sie im vorherigen Schritt erstellt haben, und dann den Knoten **Blobcontainer**. Im Container `$web` stellen Sie Ihren App-Code bereit.

   ![Svelte: Azure Storage-Knoten im Azure Storage-Explorer](media/static-website/storage-nodes.png)

1. Wählen Sie den **Datei-Explorer** aus, klicken Sie mit der rechten Maustaste auf Ihren Ordner _public_, und wählen Sie **Deploy to Static Website** (Auf statischer Website bereitstellen) aus:

    ![Svelte: Befehl „Deploy to Static Website“ (Auf statischer Website bereitstellen)](media/static-website/deploy-build-svelte.png)

1. Wählen Sie das zuvor erstellte Storage-Konto aus, wenn Sie zur Eingabe aufgefordert werden.

1. Wenn die Bereitstellung abgeschlossen ist, wird eine Meldung mit einer Schaltfläche **Browse to website** (Zur Website wechseln) angezeigt. Klicken Sie auf diese Schaltfläche, um den primären Endpunkt des bereitgestellten App-Codes zu öffnen.

    ![Svelte: Meldung bei Abschluss der Bereitstellung](media/static-website/deployment-complete-svelte.png)

    ![Svelte: In Azure ausgeführte statische Website](media/static-website/azure-app-svelte.png)

---

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt.](tutorial-vscode-static-website-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
