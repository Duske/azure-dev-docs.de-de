---
title: Erstellen einer Ressource für maschinelles Sehen
description: Hier erfahren Sie, wie Sie Ihre Cognitive Services-Ressource für maschinelles Sehen erstellen und auf Umgebungsvariablen festlegen.
ms.topic: tutorial
ms.date: 12/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 12d55ab384fc7a6a387d832a78883f62de9f38ea
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561026"
---
# <a name="4-create-computer-vision-resource-and-use-in-code"></a>4. Erstellen einer Ressource für maschinelles Sehen und Verwenden der Ressource im Code

In diesem Schritt wird Ihre Ressource für maschinelles Sehen erstellt und auf Umgebungsvariablen festgelegt. 

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Durch die Erstellung einer Ressourcengruppe können Sie Ressourcen ganz einfach finden und wieder löschen, wenn Sie sie nicht mehr benötigen.

Am Ende dieser Schritte benötigen Sie den **Schlüssel und Endpunkt** für Ihre Ressource.

1. Geben Sie an einem Terminal oder an einer Bash-Shell den [Azure CLI-Befehl zum Erstellen einer Azure-Ressourcengruppe](/cli/azure/group#az_group_create) mit dem Namen `rg-demo` ein:

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo 
    ```
1. Führen Sie den folgenden Befehl aus, um [eine Ressource für maschinelles Sehen zu erstellen](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create):


    ```azurecli
    az cognitiveservices account create \
        --name demo-ComputerVision \
        --resource-group rg-demo \
        --kind ComputerVision \
        --sku F0 \
        --location eastus \
        --yes
    ```

1. Suchen Sie in den Ergebnissen nach dem Instrumentierungsschlüssel (`properties.endpoint`), und kopieren Sie ihn. Diese benötigen Sie weiter unten.

    ```json
    ...
    "properties":{
        ...
        "endpoint": "https://eastus.api.cognitive.microsoft.com/",
        ...
    }
    ...
    ```

1. Führen Sie den folgenden [Befehl](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) aus, um Ihre Schlüssel abzurufen: 

    ```azurecli
    az cognitiveservices account keys list \
    --name demo-ComputerVision \
    --resource-group rg-demo
    ```

1. Kopieren Sie einen der Schlüssel für später.

    ```json
    {
      "key1": "8eb7f878bdce4e96b26c89b2b8d05319",
      "key2": "c2067cea18254bdda71c8ba6428c1e1a"
    }
    ```

## <a name="add-environment-variables-to-your-local-environment"></a>Hinzufügen von Umgebungsvariablen zu Ihrer lokalen Umgebung

Um Ihre Ressource verwenden zu können, muss der lokale Code über den Schlüssel und den Endpunkt verfügen. In dieser Codebasis werden diese Angaben in folgenden Umgebungsvariablen gespeichert:

* REACT_APP_COMPUTERVISIONKEY
* REACT_APP_COMPUTERVISIONENDPOINT 

1. Führen Sie den folgenden Befehl aus, um diese Variablen Ihrer Umgebung hinzuzufügen:

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    export REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    export REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    
    # <a name="cmd"></a>[cmd](#tab/cmd)
    
    ```cmd
    set REACT_APP_COMPUTERVISIONKEY="REPLACE-WITH-YOUR-KEY"
    set REACT_APP_COMPUTERVISIONENDPOINT="REPLACE-WITH-YOUR-ENDPOINT"
    ```
    ---

## <a name="add-environment-variables-to-your-remote-environment"></a>Hinzufügen von Umgebungsvariablen zu Ihrer Remoteumgebung

Bei Verwendung statischer Azure-Web-Apps müssen Umgebungsvariablen wie etwa Geheimnisse von der GitHub-Aktion an die statische Web-App übergeben werden. Die GitHub-Aktion erstellt die App (einschließlich des Schlüssels und des Endpunkts für maschinelles Sehen aus den GitHub-Geheimnissen für dieses Repository) und pusht anschließend den Code mit den Umgebungsvariablen an die statische Web-App.

1. Navigieren Sie in einem Webbrowser in Ihrem GitHub-Repository zu **Settings** > **Secrets** > **New repository secret** („Einstellungen“ > „Geheimnisse“ > „Neues Repositorygeheimnis“).

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-create-new-repository-secret.png" alt-text="Screenshot: Browserausschnitt mit React-Beispiel für maschinelles Sehen von Cognitive Services für die Bildanalyse vor dem Festlegen von Schlüssel und Endpunkt":::

1. Geben Sie den Namen und den Wert des Endpunkts aus dem vorherigen Abschnitt ein. Erstellen Sie anschließend ein weiteres Geheimnis mit dem Namen und dem Wert des Schlüssels aus dem vorherigen Abschnitt. 
    
    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-add-secret.png" alt-text="Geben Sie den gleichen Namen und Wert für den Endpunkt ein. Erstellen Sie anschließend ein weiteres Geheimnis mit dem gleichen Namen und Wert für den Schlüssel.":::

## <a name="run-react-app-with-computervision-resource"></a>Ausführen der React-App mit der Ressource für maschinelles Sehen

1. Starten Sie die App über die Befehlszeile erneut:

    ```bash
    npm start
    ```

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-start-up.png" alt-text="Screenshot: Browserausschnitt mit React-Beispiel für maschinelles Sehen von Cognitive Services: Möglichkeit zum Eingeben einer URL oder zum Drücken der EINGABETASTE":::

1. Lassen Sie das Textfeld leer, um ein Bild aus dem Standardkatalog auszuwählen, und wählen Sie die Schaltfläche **Analysieren** aus. 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="Screenshot: Browserausschnitt mit den Ergebnissen des React-Beispiels für maschinelles Sehen von Cognitive Services":::

    Das Bild wird nach dem Zufallsprinzip aus einem Katalog von Bildern ausgewählt, der in `./src/DefaultImages.js` definiert ist. 

1. Wählen Sie die Schaltfläche **Analyze** (Analysieren) wiederholt aus, um die anderen Bilder und Ergebnisse anzuzeigen. 

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Erstellen der statischen Azure-Web-App](create-static-web-app-visual-studio-code-extension.md)