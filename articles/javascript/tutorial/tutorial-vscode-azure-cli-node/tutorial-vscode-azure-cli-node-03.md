---
title: Erstellen der Azure App Service-Instanz zum Hosten der App mit der Azure-Befehlszeilenschnittstelle
description: 'Teil 3 des Azure CLI-Tutorials: Erstellen der App Service-Instanz'
ms.topic: tutorial
ms.date: 12/18/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: abd434d2222e5bdd758be42856a569e24def08f8
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687432"
---
# <a name="create-the-app-service"></a>Erstellen der App Service-Instanz

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-azure-cli-node-02.md)

In diesem Schritt erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) die Azure App Service-Instanz, die Ihren App-Code hostet.

1. Geben Sie an einem Terminal oder einer Eingabeaufforderung den folgenden Befehl ein, um eine **Ressourcengruppe** für die App Service-Instanz zu erstellen. Bei einer Ressourcengruppe handelt es sich im Wesentlichen um eine benannte Sammlung der Ressourcen einer App in Azure, z. B. eine Website, eine Datenbank, Azure Functions usw.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    Der obige Azure CLI-Befehl [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) erstellt eine Ressourcengruppe namens `myResourceGroup` im Rechenzentrum `westus`. Sie können diese Werte wie gewünscht ändern.

    Nachdem der Befehl erfolgreich ausgeführt wurde, wird eine JSON-Ausgabe mit den Details der Ressourcengruppe angezeigt.

1. Führen Sie den folgenden Azure CLI-Befehl [`az configure`](/cli/azure/config?view=azure-cli-latest) aus, um die Standardressourcengruppe und -region für nachfolgende Befehle festzulegen. Auf diese Weise müssen Sie die Werte nicht jedes Mal angeben. (Bei diesem Befehl wird bei erfolgreicher Ausführung keine Ausgabe angezeigt.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

1. Führen Sie den folgenden Azure CLI-Befehl [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) aus, um einen **App Service-Plan** zu erstellen, der den zugrunde liegenden virtuellen Computer festlegt, der von der App Service-Instanz verwendet wird:

    ```azurecli
    az appservice plan create --name myPlan --sku F1
    ```

    Der obige Befehl legt einen [kostenlosen Hostingplan](../../core/what-is-azure-for-javascript-development.md#free-tier-resources) (`--sku F1`) mit einer freigegebenen VM und dem Namen `myPlan` fest. 

1. Führen Sie den folgenden Azure CLI-Befehl [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) aus, um die App Service-Instanz zu erstellen. Ersetzen Sie dabei `<your_app_name>` durch einen eindeutigen Namen, aus dem die URL `http://<your_app_name>.azurewebsites.net` mit der [neuesten Node.js-Runtime](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes&preserve-view=false) erstellt wird. 

    ```azurecli
    az webapp create --name <your_app_name> --plan myPlan -g --runtime "node|12-lts"
    ```


1. Führen Sie den folgenden Azure CLI-Befehl [`az webapp browse`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_browse) aus, um die neu erstellte App Service-Instanz in einem Browser zu öffnen. Ersetzen Sie `<your_app_name>` dabei wieder durch den Namen, den Sie verwendet haben:

    ```azurecli
    az webapp browse --name <your_app_name>
    ```

1. Da Sie keinen benutzerdefinierten Code für die App bereitgestellt haben, sollte im Browser eine Standardseite angezeigt werden:

    ![App Service-Standardseite](../../media/azure-cli/azure-default-page.png)

## <a name="troubleshooting"></a>Problembehandlung

* Wurde ein Fehler zu dem fehlenden, aber erforderlichen Parameter `--resource-group` angezeigt, kehren Sie zum Anfang des Artikels zurück, und legen Sie die Standardwerte fest. 

> [!div class="nextstepaction"]
> [Ich habe die App Service-Instanz erstellt.](tutorial-vscode-azure-cli-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
