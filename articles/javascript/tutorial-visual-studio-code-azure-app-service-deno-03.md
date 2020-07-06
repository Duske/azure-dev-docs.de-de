---
title: Bereitstellen von Deno-Apps in Azure App Service aus Visual Studio Code
description: 'Teil 3 des Tutorials: Bereitstellen der Website'
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: f660683983181fbdc9f8dbbc4c549ba68aa829ed
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791775"
---
# <a name="deploy-deno-apps-to-azure"></a>Bereitstellen von Deno-Apps in Azure

[Vorheriger Schritt: Erstellen der App](tutorial-visual-studio-code-azure-app-service-deno-02.md)

In diesem Schritt stellen Sie Ihre Deno-App mithilfe der Azure CLI in Azure bereit.

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

1. Erstellen Sie mit folgendem Befehl eine Ressourcengruppe namens `deno-quickstart`:

    ```bash
    az groups create -n deno-quickstart -l eastus
    ```

    Wenn Sie den Namen der Ressourcengruppe ändern möchten, achten Sie darauf, alle `-g`-Flags in den folgenden Schritten zu aktualisieren.

1. Erstellen Sie mit diesem Befehl einen AppService-Plan mit dem Namen `deno-plan`, der Ihre Website enthält:

    ```bash
    az appservice plan create -g deno-quickstart -n deno-plan --is-linux
    ```

1. Im nächsten Schritt erstellen Sie die Web-App selbst. Mit diesem Befehl wird ein neuer AppService erstellt und an den zuvor erstellten Plan gebunden. Ändern Sie das `<your-app-name>`-Tag in den Namen, den Sie Ihrer Web-App geben möchten, und denken Sie dabei daran, dass er eindeutig sein muss!

    ```bash
    az webapp create -n <your-app-name> -g deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    Dieser AppService führt ein Docker-Image aus, das die Basisfunktionen für die Ausführung von Deno-Code bereitstellt. Dieser Vorgang kann einige Sekunden dauern.

1. Nach der Erstellung müssen Sie einige Variablen konfigurieren. Hierzu können Sie den folgenden Befehl ausgeben:

    ```bash
    az webapp config container set -n <your-app-name> -g deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file '' && \
    az webapp config appsettings set -n <your-app-name> -g deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

Der AppService ist nun konfiguriert und wartet auf den Empfang der App aus dem vorherigen Schritt. Um sie auszuführen, muss die App jedoch in einem `.zip`-Paket enthalten sein. Dies kann über die folgenden Schritte erfolgen:

1. Navigieren Sie zum Ordner `deno-demo`.

    ```bash
    cd deno-demo
    ```

1. Führen Sie den Befehl `zip` aus:

    ```bash
    zip demo demo.ts
    ```

    Das Ergebnis dieses Befehls ist eine Datei mit dem Namen `demo.zip` im selben Ordner wie die Datei `demo.ts`.

1. Nach der Paketerstellung können Sie die Datei in den AppService hochladen, damit sie ausgeführt wird:

    ```bash
    az webapp deployment source config-zip -n <your-app-name> -g deno-quickstart --src ./demo.zip && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. Testen Sie die Anwendung, indem Sie zu `https://<your-app-name>.azurewebsites.net` navigieren.

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt](tutorial-visual-studio-code-azure-app-service-deno-04.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=deploy-app)

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
