---
title: Erstellen eines Azure Storage-Kontos für eine statische Node.js-Website in Visual Studio Code
description: 'Teil 3 des Tutorials: Erstellen eines Azure Storage-Kontos'
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 42badfc649d7cc43eb1a58ab20c8ff639eff5354
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466506"
---
# <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-static-website-node-02.md)

In diesem Schritt erstellen Sie ein Azure Storage-Konto, das als einfacher Dateispeicher oder Content Delivery Network (CDN) mit einem integrierten Webserver fungiert. Durch diesen integrierten Server eignet sich Azure Storage hervorragend für das schnelle Hosting statischer Websites.

1. Starten Sie Visual Studio Code in dem im vorherigen Schritt erstellten Ordner `my-static-app`, damit dieser automatisch geöffnet wird:

    ```bash
    code .
    ```

1. Klicken Sie in VS Code auf das Azure-Logo, um den **Azure**-Explorer zu öffnen. Klicken Sie unter **Azure Storage** mit der rechten Maustaste auf Ihr Azure-Abonnement, und wählen Sie **Speicherkonto erstellen** aus:

    ![Erstellen des Storage-Kontos in VS Code](media/static-website/create-storage-account.png)

1. Geben Sie an der Eingabeaufforderung „Enter the name of the new storage account“ (Geben Sie den Namen des neuen Speicherkontos ein) einen global eindeutigen Namen für Ihr Storage-Konto ein, und drücken Sie die EINGABETASTE. Gültige Zeichen für App-Namen sind die Buchstaben a – z und die Ziffern 0 – 9.

1. Wählen Sie an der Eingabeaufforderung „Ressourcengruppe auswählen“ die Option **Neue Ressourcengruppe erstellen** aus, und übernehmen Sie den Standardnamen.

1. Wählen Sie an der Eingabeaufforderung „Standort auswählen“ eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe aus.

1. Während der Erstellung des Storage-Kontos wird der Fortschritt im Bereich **Ausgabe** von VS Code angezeigt:

1. Klicken Sie nach der Erstellung des Storage-Kontos mit der rechten Maustaste auf das Konto, und wählen Sie **Configure Static Website** (Statische Website konfigurieren) aus. Wenn Sie das Hosting statischer Websites aktivieren, verarbeitet Azure Storage automatisch Ihr Indexdokument und alle anderen statischen Ressourcen.

    ![Speicherkonto erstellen](media/static-website/configure-static-website.png)

1. Geben Sie sowohl für den Namen des Indexdokuments als auch den Namen des 404-Fehlerdokuments *index.html* ein, wenn Sie dazu aufgefordert werden. Wir verwenden *index.html* für das Fehlerdokument, da moderne Single-Page-Apps (SPAs) wie React, Angular und Vue Fehler auf dem Client behandeln. Verwenden Sie für klassische statische Websites eine benutzerdefinierte 404-Fehlerseite.

> [!div class="nextstepaction"]
> [Ich habe einen Speichercontainer erstellt.](tutorial-vscode-static-website-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
