---
title: Erstellen eines Azure Storage-Kontos für eine statische Node.js-Website in Visual Studio Code
description: 'Teil 3 des Tutorials: Erstellen eines Azure Storage-Kontos'
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: f0aa6a8cbfc0a5d286d0b7c93a12cbd48b68d620
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218347"
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

    > [!NOTE]
    > Dadurch werden ein Speicherkonto und eine Ressourcengruppe mit dem gleichen Namen erstellt. Das Speicherkonto wird automatisch in der Region „USA, Westen“ platziert. Wenn Sie die Ressourcengruppe und den Standort angeben möchten, wählen Sie im Kontextmenü die Option „Speicherkonto erstellen (erweitert)“ aus.

1. Während der Erstellung des Storage-Kontos wird der Fortschritt im Bereich **Ausgabe** von VS Code angezeigt:

    ![VS Code-Ausgabefenster ](media/static-website/output-storage.png)

1. Nach Abschluss der Speicherkontoerstellung wird eine Meldung mit dem Hinweis angezeigt, dass das Hosten statischer Websites für das Speicherkonto aktiviert wurde.

    ![Erstellen eines Speicherkontos](media/static-website/static-website-enabled-notification.png)

    > [!IMPORTANT]
    > Wir verwenden *index.html* für das Fehlerdokument, da moderne Single-Page-Apps (SPAs) wie React, Angular und Vue Routenfehler im Client behandeln. Verwenden Sie für klassische statische Websites eine benutzerdefinierte 404-Fehlerseite.

> [!div class="nextstepaction"]
> [Ich habe einen Speichercontainer erstellt.](tutorial-vscode-static-website-node-04.md) [Bei mir ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
