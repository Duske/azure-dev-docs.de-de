---
title: Herunterladen der React-Beispiel-App
description: Die vollständige Beispiel-App wird in einem GitHub-Repository bereitgestellt. Forken Sie das Repository, installieren Sie die Abhängigkeiten, und führen Sie die App lokal aus.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5050aca4c34f20f8b4c7cb7a8460815516abd7c7
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687451"
---
# <a name="3-download-and-run-the-react-cognitive-services-image-analyzer-app"></a>3. Herunterladen und Ausführen der React-App für die Bildanalyse mit Cognitive Services

Die vollständige Beispiel-App wird in einem GitHub-Repository bereitgestellt. Forken Sie das Repository, installieren Sie die Abhängigkeiten, und führen Sie die App lokal aus.

## <a name="fork-the-sample-repo"></a>Forken des Beispielrepositorys

Forken Sie das Repository (anstatt es nur auf Ihrem Computer zu klonen), um über ein eigenes GitHub-Repository zu verfügen, an das Sie Änderungen pushen können. 

1. Öffnen Sie ein separates Browserfenster oder einen separaten Browsertab, und melden Sie sich bei <a href="https://github.com/login" target="_blank">GitHub</a> an. 
1. Navigieren Sie im Webbrowser zum <a href="https://github.com/Azure-Samples/js-e2e-client-cognitive-services" target="_blank">GitHub-Beispielrepository</a>. 

    ```http
    https://github.com/Azure-Samples/js-e2e-client-cognitive-services
    ```

1. Wählen Sie rechts oben auf der Seite die Option **Fork** (Forken) aus. 
1. Wählen Sie **Code** aus, und kopieren Sie anschließend die URL für Ihren Fork. 

    :::image type="content" source="../../media/static-web-app/browser-screenshot-clone-github-sample-repository-fork.png" alt-text="Screenshot: Ausschnitt der GitHub-Website zum Auswählen von **Code** und anschließenden Kopieren des Orts für Ihren Fork":::    

## <a name="create-local-development-environment"></a>Erstellen der lokalen Entwicklungsumgebung

1. Klonen Sie in einem Terminal- oder Bash-Fenster Ihren Fork auf Ihrem lokalen Computer. Ersetzen Sie `YOUR-ACCOUNT-NAME` durch Ihren GitHub-Kontonamen.

    ```bash
    git clone https://github.com/YOUR-ACCOUNT-NAME/js-e2e-client-cognitive-services
    ```

1. Wechseln Sie zu dem neuen Verzeichnis, und installieren Sie die Abhängigkeiten. 

    ```bash
    cd js-e2e-client-cognitive-services && npm install
    ```

## <a name="run-sample"></a>Ausführen des Beispiels

1. Führen Sie das Beispiel aus. 

    ```bash
    npm start
    ```

    :::image type="content" source="../../media/static-web-app/browser-screenshot-react-cognitive-services-app-before-authentication.png" alt-text="Screenshot: Browserausschnitt mit React-Beispiel für maschinelles Sehen von Cognitive Services für die Bildanalyse vor dem Festlegen von Schlüssel und Endpunkt":::    
    
1. Beenden Sie die App. Schließen Sie entweder das Terminalfenster, oder verwenden Sie `control+c` am Terminal. 
    
## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Erstellen einer Ressource für maschinelles Sehen und Verwenden der Ressource im Code](create-computer-vision-resource-use-in-code.md) 