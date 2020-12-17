---
title: Bereitstellen von Deno-Apps in Azure App Service über die Azure CLI
description: In diesem Tutorial stellen Sie eine Deno-Anwendung mithilfe der Azure CLI für Azure App Service (unter Linux oder Windows) bereit.
ms.topic: tutorial
ms.date: 10/13/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: ba2e0a42b6d2dedd2192629562a8415a0d6d7167
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96772615"
---
# <a name="deploy-deno-apps-to-azure-app-service-from-the-azure-cli"></a>Bereitstellen von Deno-Apps in Azure App Service über die Azure CLI

Stellen Sie mithilfe der Azure CLI eine Deno-Anwendung in Azure App Service (unter Linux oder Windows) bereit. Die Deno-Runtime für Azure App Service wird mit einem experimentellen Docker-Image bereitgestellt. 

![Ausführen des Demoservers](../media/deploy-azure/deno-hello-world.png)

## <a name="1-prepare-your-environment"></a>1. Vorbereiten der Umgebung

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenloses Konto erstellen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).
- Installieren Sie [Deno](https://deno.land/#installation).
- Verwenden Sie [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) unter Nutzung der Bash-Umgebung.

   [![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)   
- Wenn Sie möchten, können Sie auch die Azure CLI [installieren](/cli/azure/install-azure-cli), um CLI-Verweisbefehle auszuführen.
   - Wenn Sie eine lokale Installation verwenden, melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei der Azure CLI an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.  Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).
  - Installieren Sie die Azure CLI-Erweiterungen bei der ersten Verwendung, wenn Sie dazu aufgefordert werden.  Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Führen Sie [az version](/cli/azure/reference-index?#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="2-sign-in-to-azure-cli"></a>2. Anmelden bei der Azure-Befehlszeilenschnittstelle

Wenn Sie die Azure CLI über Ihre lokale Befehlszeile verwenden, müssen Sie sich vor der Verwendung von CLI-Befehlen mit [az login](/cli/azure/reference-index#az-login) anmelden.

[!INCLUDE [interactive-login](../../azure-cli/includes/interactive-login.md)]

3. Nach der Anmeldung sehen Sie eine Liste der mit Ihrem Azure-Konto verknüpften Abonnements. Die Abonnementinformationen mit `isDefault: true` ist das momentan aktivierte Abonnement nach der Anmeldung. 

4. Wenn Sie ein anderes Abonnement auswählen müssen, verwenden Sie den Befehl [az account set](/cli/azure/account#az-account-set) mit der ID des Abonnements, zu dem Sie wechseln möchten. Weitere Informationen zur Abonnementauswahl finden Sie unter [Verwenden mehrerer Azure-Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli).

## <a name="3-create-local-deno-api-app"></a>3. Erstellen einer lokalen Deno-API-App

Erstellen Sie mit dem integrierten Webserver von Deno eine Deno-App. Sie führen die App anschließend lokal aus.

1. Navigieren Sie in einem Terminal oder an einer Eingabeaufforderung zu einem Speicherort, an dem Sie den App-Ordner erstellen möchten, und erstellen Sie einen neuen Ordner namens `deno-demo`.

1. Erstellen Sie eine neue Datei namens `demo.ts`.
1. Von Deno wird die direkte Ausführung von Code aus URLs akzeptiert. Schreiben Sie einen HTTP-Server, der alle Anforderungen mit „Hallo Welt“ beantwortet. Verwenden Sie den folgenden Code:

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. Führen Sie die App aus, indem Sie das folgende Skript ausführen:

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. Testen Sie die App, indem Sie `http://localhost:80` in einem Browser öffnen. Die Website sollte wie folgt angezeigt werden:

    ![Ausführen des Demoservers](../media/deploy-azure/deno-hello-world.png)

    Sie können diesen Code auch ausführen, indem Sie `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts` eingeben.

1. Drücken Sie im Terminal **STRG**+**C**, um den Server zu beenden.

## <a name="4-deploy-deno-app-to-azure"></a>4. Bereitstellen einer Deno-App in Azure

Stellen Sie Ihre Deno-App mithilfe der Azure CLI in Azure bereit.

1. Erstellen Sie mit folgendem Befehl eine Ressourcengruppe namens `deno-quickstart`:

    ```azurecli
    az group create --name deno-quickstart --location eastus
    ```

    Wenn Sie den Namen der Ressourcengruppe ändern möchten, achten Sie darauf, alle `-g`-Flags in den folgenden Schritten zu aktualisieren.

1. Erstellen Sie mit diesem Befehl einen AppService-Plan mit dem Namen `deno-plan`, der Ihre Website enthält:

    ```azurecli
    az appservice plan create --resource-group deno-quickstart --name deno-plan --is-linux
    ```

1. Im nächsten Schritt erstellen Sie die Web-App selbst. Mit diesem Befehl wird ein neuer AppService erstellt und an den zuvor erstellten Plan gebunden. Ändern Sie das `<your-app-name>`-Tag in den Namen, den Sie Ihrer Web-App geben möchten, und denken Sie dabei daran, dass er eindeutig sein muss!

    ```azurecli
    az webapp create -n <your-app-name> --resource-group deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    Dieser AppService führt das Docker-Image `anthonychu/azure-webapps-deno:1.0.2` aus, das die Basisfunktionen für die Ausführung von Deno-Code bereitstellt. Dieser Vorgang kann einige Sekunden dauern.

## <a name="5-configure-the-azure-app-service-webapp"></a>5. Konfigurieren der Azure App Service-Web-App

1. Weisen Sie die Web-App an, wo das Docker-Containerimage für den Namen des experimentellen Deno-Images abgerufen werden soll:

    ```azurecli
    az webapp config container set --name <your-app-name> --resource-group deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true
    ```

1. Legen Sie für die Web-App keine Startdatei fest:

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file ''

1. Set the webapp to have runtime environment variables:

    ```azurecli
    az webapp config appsettings set --name <your-app-name> --resource-group deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

## <a name="6-configure-deno-app-deployment-to-web-app"></a>6. Konfigurieren der Deno-App-Bereitstellung in Web-Apps 

Die Azure-Web-App ist konfiguriert und einsatzbereit, verfügt aber noch nicht über das Deno-Paket. Packen Sie die App in ein Paket vom Typ `.zip`, informieren Sie die Web-App, dass sich die Datei auf dem lokalen Computer befindet, und legen Sie dann die Startdatei fest, die in der ZIP-Datei enthalten ist. 

1. Navigieren Sie zum Ordner `deno-demo`.

    ```bash
    cd deno-demo
    ```

1. Führen Sie den Befehl `zip` aus:

    ```bash
    zip demo demo.ts
    ```

    Das Ergebnis dieses Befehls ist eine Datei mit dem Namen `demo.zip` im selben Ordner wie die Datei `demo.ts`.

1. Konfigurieren Sie das Paket als Codequelle für die Bereitstellung:

    ```azurecli
    az webapp deployment source config-zip --name <your-app-name> --resource-group deno-quickstart --src ./demo.zip
    ```

1. Konfigurieren Sie die Datei im Paket, um Folgendes auszuführen:

    ```azurecli
    az webapp config set --name <your-app-name> --resource-group deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. Testen Sie die Anwendung, indem Sie zu `https://<your-app-name>.azurewebsites.net` navigieren. 

## <a name="7-clean-up-resources"></a>7. Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl. Dadurch werden auch die Web-App-Ressourcen gelöscht:

```azurecli
az group delete deno-quickstart
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:
* [Bereitstellen in App Service](../tutorial-vscode-azure-app-service-node-01.md) mit Visual Studio Code-Erweiterungen
* [Bereitstellen auf einem virtuellen Computer](./nodejs-virtual-machine-vm/introduction.md)
* [Bereitstellen einer Deno-Funktion](https://github.com/anthonychu/azure-functions-deno-worker) als [benutzerdefinierten Handler](/azure/azure-functions/functions-custom-handlers)