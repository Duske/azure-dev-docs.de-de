---
title: Bereitstellen des App-Codes in Azure App Service mit der Azure-Befehlszeilenschnittstelle (Azure CLI)
description: 'Teil 4 des Azure CLI-Tutorials: Bereitstellen der Website'
ms.topic: tutorial
ms.date: 12/14/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 368597fb10397f7e7ba8feb82b29d031c292dab0
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561006"
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

1. Führen Sie den folgenden Befehl aus, um [Bereitstellungsanmeldeinformationen mit der Azure CLI einzurichten](/azure/app-service/deploy-configure-credentials), und ersetzen Sie `username` und `password` dabei durch neue Anmeldeinformationen, die nur spezifisch für die Bereitstellung vorgesehen sind. Diese Anmeldeinformationen sind nicht identisch mit den Anmeldeinformationen Ihres Azure-Abonnements. 

    ```azurecli
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. Führen Sie den folgenden Befehl aus, um [den Git-Endpunkt mithilfe der Azure CLI abzurufen](/cli/azure/webapp/deployment/source), der an den App-Code gepusht werden soll. Ersetzen Sie `<your_app_name>` dabei durch den Namen, den Sie beim Erstellen der App Service-Instanz im vorherigen Schritt verwendet haben:

    ```azurecli
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    Die Ausgabe des Befehls sieht in etwa wie folgt aus:

    <pre>
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    </pre>

1. Führen Sie den folgenden Befehl aus, um einen neuen Remotespeicherort in Git mit dem Namen `azure` festzulegen. Verwenden Sie dazu die URL aus dem vorherigen Schritt *ohne den Benutzernamen*. Wenn Sie das Beispiel im vorherigen Schritt verwenden, lautet der Befehl wie folgt:

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. Führen Sie den folgenden Befehl aus, um den App-Code aus dem Git-Repository in der App Service-Instanz bereitzustellen. Der Befehl fordert Sie zur Eingabe Ihrer Anmeldeinformationen auf:

    ```bash
    git push azure <DEFAULT-BRANCH-NAME>
    ```

1. Während der Ausführung des Befehls wird eine Reihe von Meldungen vom Remotehost angezeigt. Aktualisieren Sie nach Abschluss des Prozesses den Browser, in dem die URL der App geöffnet ist, um den ausgeführten Code anzuzeigen:

    ![In Azure ausgeführter App-Code](../../media/azure-cli/remote-app.png)

> [!div class="nextstepaction"]
> [Ich habe die App bereitgestellt.](tutorial-vscode-azure-cli-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
