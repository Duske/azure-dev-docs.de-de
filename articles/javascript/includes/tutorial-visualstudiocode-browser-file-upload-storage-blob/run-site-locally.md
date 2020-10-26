---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: include file run-site-locally.md
description: include file run-site-locally.md
ms.openlocfilehash: 129546c7f6a845c335405c6fc615f907848138f6
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344145"
---
In diesem Abschnitt des Tutorials laden Sie die Beispielanwendung auf Ihren lokalen Computer herunter und führen sie über das Visual Studio Code-Terminal aus. Zeigen Sie dann die lokal ausgeführte App in Ihrem Browser an.

## <a name="clone-and-run-the-initial-react-app"></a>Klonen und Ausführen der anfänglichen React-App

Die vollständige React-App wird für Sie bereitgestellt. In diesem Verfahren klonen Sie die App, installieren die Abhängigkeiten und führen die App aus. Die anfängliche App versucht, eine Verbindung mit Azure Storage herzustellen, wenn sie im Code entsprechend konfiguriert ist, oder es wird eine Meldung `Storage is not configured` angezeigt, wenn sie nicht verfügbar ist. 

1. Öffnen Sie Visual Studio Code.
1. Klonen Sie das GitHub-Repository, indem Sie das Git-Symbol und dann **Klonen** auswählen. Für diesen Vorgang müssen Sie sich bei GitHub anmelden. Verwenden Sie die Repository-URL `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`, und wählen Sie dann einen Ordner auf dem lokalen Computer aus, in den das Beispiel geklont werden soll. Wenn Sie dazu aufgefordert werden, öffnen Sie das geklonte Repository. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Klonen Sie das GitHub-Repository, indem Sie das Git-Symbol und dann „Repository klonen“ auswählen.":::

1. Öffnen Sie in Visual Studio Code ein Terminalfenster, und führen Sie den folgenden Befehl aus, um die Abhängigkeiten des Beispiels zu installieren.

    ```javascript
    npm install
    ```

1. Führen Sie im gleichen Terminalfenster den Befehl zum Ausführen der Web-App aus.

    ```javascript
    npm start
    ```

1. Öffnen Sie einen Webbrowser, und verwenden Sie die folgende URL, um die Web-App auf Ihrem lokalen Computer anzuzeigen.

    ```url
    http://localhost:3000/
    ```

    Wenn die einfache Web-App in Ihrem Browser mit dem Text angezeigt wird, dass der Speicher nicht konfiguriert ist, haben Sie diesen Abschnitt des Tutorials erfolgreich durchgearbeitet.

    :::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="Klonen Sie das GitHub-Repository, indem Sie das Git-Symbol und dann „Repository klonen“ auswählen.":::

1. Beenden Sie den Code mit STRG+C im Visual Studio Code-Terminal.
