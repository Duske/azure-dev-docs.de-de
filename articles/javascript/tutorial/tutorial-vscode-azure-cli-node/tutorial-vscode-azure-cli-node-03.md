---
title: Erstellen der Azure App Service-Instanz zum Hosten der App mit der Azure-Befehlszeilenschnittstelle
description: 'Teil 3 des Azure CLI-Tutorials: Erstellen der App Service-Instanz'
ms.topic: tutorial
ms.date: 01/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ad8e0836d62102521b557fd45b24291d52db447c
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561516"
---
# <a name="create-the-app-service"></a>Erstellen der App Service-Instanz

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-azure-cli-node-02.md)

In diesem Schritt erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) die Azure App Service-Instanz, die Ihren App-Code hostet.

<a name="create-resource-group"></a>

## <a name="create-resource-group-and-set-as-default-value"></a>Erstellen einer Ressourcengruppe und Festlegen als Standardwert

1. Geben Sie an einem Terminal oder einer Eingabeaufforderung den folgenden Befehl ein, um eine **Ressourcengruppe** für die App Service-Instanz zu erstellen. Bei einer Ressourcengruppe handelt es sich im Wesentlichen um eine benannte Sammlung der Ressourcen einer App in Azure, z. B. eine Website, eine Datenbank, Azure Functions usw.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    Der obige Azure CLI-Befehl [`az group create`](/cli/azure/group#az_group_create) erstellt eine Ressourcengruppe namens `myResourceGroup` im Rechenzentrum `westus`. Sie können diese Werte wie gewünscht ändern.

    Nachdem der Befehl erfolgreich ausgeführt wurde, wird eine JSON-Ausgabe mit den Details der Ressourcengruppe angezeigt.

1. Führen Sie den folgenden Azure CLI-Befehl [`az configure`](/cli/azure/config) aus, um die Standardressourcengruppe und -region für nachfolgende Befehle festzulegen. Auf diese Weise müssen Sie die Werte nicht jedes Mal angeben. (Bei diesem Befehl wird bei erfolgreicher Ausführung keine Ausgabe angezeigt.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

## <a name="create-and-deploy-web-app-service-with-azure-cli-command"></a>Erstellen und Bereitstellen des Web-App-Diensts mit einem Azure CLI-Befehl

Führen Sie den nachfolgenden Azure CLI-Befehl [`az webapp up`](/cli/azure/webapp#az_webapp_up) aus, um die App Service-App zu erstellen und bereitzustellen. Ersetzen Sie `<your_app_name>` durch einen eindeutigen Namen, der zur URL (`http://<your_app_name>.azurewebsites.net`) wird. 

```azurecli
az webapp up --name <your_app_name> --logs --launch-browser
```

Mit dem Befehl `--logs` wird der Protokolldatenstrom direkt nach dem Start der Web-App angezeigt. Mit dem Befehl `--launch-browser` wird die neue App im Standardbrowser geöffnet. Sie können denselben Befehl verwenden, um die gesamte App erneut bereitzustellen. 

## <a name="troubleshooting"></a>Problembehandlung

* Sollte ein Fehler im Zusammenhang mit einem fehlenden, aber erforderlichen Parameter (`--resource-group`) auftreten, kehren Sie zum Anfang des Artikels zurück, und legen Sie die Standardwerte fest, oder geben Sie den Parameter und den Wert an. 

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mithilfe der [webapp](/cli/azure/webapp)-Befehlsgruppe oder der [Azure App Service](/cli/azure/appservice)-Befehlsgruppe weitere Befehle für Ihre Web-App kennen. 

> [!div class="nextstepaction"]
> [Ich habe die App Service-Instanz erstellt.](tutorial-vscode-azure-cli-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
