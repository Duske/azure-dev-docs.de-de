---
title: Bereitstellen des App-Codes in Azure App Service mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 4 des Tutorials: Bereitstellen der Website'
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4911ccdf4003b44359d40c58d1b924e6bf88c829
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467177"
---
# <a name="deploy-the-app-to-app-service"></a>Bereitstellen der App in App Service

[Vorheriger Schritt: Erstellen der App Service-Instanz](tutorial-vscode-azure-cli-node-03.md)

In diesem Schritt stellen Sie den Code Ihrer Node.js-App in Azure App Service bereit, indem Sie Ihr lokales Git-Repository mit einem einfach Prozess in Azure pushen.

1. Führen Sie an einem Terminal oder an einer Eingabeaufforderung die folgenden Befehle aus, um ein lokales Git-Repository zu initialisieren und einen ersten Commit auszuführen. (Der Ordner *node_modules* wird ignoriert, da er in der Datei *.gitignore* angegeben ist, die zuvor beim Ausführen des Express-Generators erstellt wurde.)

    ```bash
    git init
    git add -A
    git commit -m "Initial Commit"
    ```

1. Führen Sie die folgenden Befehle aus, um Anmeldeinformationen für die Bereitstellung in Azure einzurichten. Ersetzen Sie dabei `username` und `pPassword` durch Ihre Anmeldeinformationen. Bei erfolgreicher Ausführung des Befehls wird eine JSON-Ausgabe angezeigt.

    ```bash
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. Führen Sie den folgenden Befehl aus, um den Git-Endpunkt abzurufen, an den Sie den App-Code pushen möchten. Ersetzen Sie dabei `<your_app_name>` durch den Namen, den Sie im vorherigen Schritt beim Erstellen der App Service-Instanz verwendet haben:

    ```bash
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    Die Ausgabe des Befehls sieht in etwa wie folgt aus:

    ```output
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    ```

1. Führen Sie den folgenden Befehl aus, um einen neuen Remotespeicherort in Git mit dem Namen `azure` festzulegen. Verwenden Sie dazu die URL aus dem vorherigen Schritt *ohne den Benutzernamen*. Wenn Sie das Beispiel im vorherigen Schritt verwenden, lautet der Befehl wie folgt:

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. Führen Sie den folgenden Befehl aus, um den App-Code aus dem Git-Repository in der App Service-Instanz bereitzustellen. Der Befehl fordert Sie zur Eingabe Ihrer Anmeldeinformationen auf:

    ```bash
    git push azure master
    ```

1. Während der Ausführung des Befehls wird eine Reihe von Meldungen vom Remotehost angezeigt. Aktualisieren Sie nach Abschluss des Prozesses den Browser, in dem die URL der App geöffnet ist, um den ausgeführten Code anzuzeigen:

    ![In Azure ausgeführter App-Code](media/azure-cli/remote-app.png)

> [!TIP]
> Wenn der Fehler `Object #<eventemitter> has no method 'hrtime'` auftritt, müssen Sie wahrscheinlich die Version der Node-Runtime für die Website festlegen. Mit dem folgenden Befehl wird die Website angewiesen, die Node-Version `6.9.1` zu verwenden. Falls Ihre Website eine andere oder höhere Node-Version erfordert, geben Sie die Version mit der vollständigen Semantik `major.minor.patch` an.
>
> ```bash
> az webapp config appsettings set --name <your_app_name> --settings
> ```

> [!div class="nextstepaction"]
> [Ich habe die App bereitgestellt.](tutorial-vscode-azure-cli-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
