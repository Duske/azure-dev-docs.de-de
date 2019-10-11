---
title: Erstellen der Azure App Service-Instanz zum Hosten der App mit der Azure-Befehlszeilenschnittstelle
description: 'Teil 3 des Tutorials: Erstellen der App Service-Instanz'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: f8349d352d275abcc8126bb9dd1794d408045b29
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686136"
---
# <a name="create-the-app-service"></a>Erstellen der App Service-Instanz

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-azure-cli-node-02.md)

In diesem Schritt erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) die Azure App Service-Instanz, die Ihren App-Code hostet.

1. Geben Sie an einem Terminal oder einer Eingabeaufforderung den folgenden Befehl ein, um eine **Ressourcengruppe** für die App Service-Instanz zu erstellen. Bei einer Ressourcengruppe handelt es sich im Wesentlichen um eine benannte Sammlung der Ressourcen einer App in Azure, z. B. eine Website, eine Datenbank, Azure Functions usw.

    ```bash
    az group create --name myResourceGroup --location westus
    ```

    Der obige Befehl erstellt eine Ressourcengruppe namens `myResourceGroup` im Rechenzentrum `westus`. Sie können diese Werte wie gewünscht ändern.

    Nachdem der Befehl erfolgreich ausgeführt wurde, wird eine JSON-Ausgabe mit den Details der Ressourcengruppe angezeigt.

1. Führen Sie den folgenden Befehl aus, um die Standardressourcengruppe und -region für nachfolgende Befehle festzulegen. Auf diese Weise müssen Sie die Werte nicht jedes Mal angeben. (Bei diesem Befehl wird bei erfolgreicher Ausführung keine Ausgabe angezeigt.)

    ```bash
    az configure --defaults group=myResourceGroup location=westus
    ```

1. Führen Sie den folgenden Befehl aus, um einen **App Service-Plan** zu erstellen, der die zugrunde liegende VM festlegt, die von der App Service-Instanz verwendet wird:

    ```bash
    az appservice plan create --name myPlan --sku F1
    ```

    Der obige Befehl legt einen kostenlosen Hostingplan (`--sku F1`) mit einer freigegebenen VM und dem Namen `myPlan` fest. Auch hier zeigt der Befehl bei erfolgreicher Ausführung eine JSON-Ausgabe an.

1. Führen Sie den folgenden Befehl aus, um die App Service-Instanz zu erstellen. Ersetzen Sie dabei `<your_app_name>` durch einen eindeutigen Namen, aus dem die URL erstellt wird (`http://<your_app_name>.azurewebsites.net`). Der PowerShell-Befehl sieht etwas anders aus. Das Argument `--runtime "node|6.9"` weist Azure an, die Node-Version 6.9.x auf dem Server zu verwenden.

    ```bash
    az webapp create --name <your_app_name> --plan myPlan --runtime "node|6.9"
    ```

    > [!TIP]
    > Sie können die gewünschte Node-Version auch in Ihrer Datei `package.json` angeben. Azure wendet diese Einstellung während der Bereitstellung an. Der folgende Eintrag in `package.json` weist Azure beispielsweise an, Node 7.0.0 oder höher zu verwenden:
    >
    > ``` json
    > "engines": {
    >     "node": ">7.0.0"
    > },
    > ```

1. Führen Sie den folgenden Befehl aus, um die neu erstellte App Service-Instanz in einem Browser zu öffnen. Ersetzen Sie `<your_app_name>` dabei wieder durch den Namen, den Sie verwendet haben:

    ```bash
    az webapp browse --name <your_app_name>
    ```

1. Da Sie keinen benutzerdefinierten Code für die App bereitgestellt haben, sollte im Browser eine Standardseite angezeigt werden:

    ![App Service-Standardseite](media/azure-cli/azure-default-page.png)

> [!div class="nextstepaction"]
> [Ich habe die App Service-Instanz erstellt.](tutorial-vscode-azure-cli-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
