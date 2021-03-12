---
title: Text-zu-Sprache-Konvertierung durch Express.js-App mit Cognitive Services Speech
description: Verwenden Sie Cognitive Services Speech für die Text-zu-Sprache-Konvertierung. Der Vorgang wird auf dem Client und auf dem Server demonstriert.
ms.topic: tutorial
ms.date: 01/20/2021
ms.custom: languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 028871f55a7be2e38191efef37a65ad41ab9c3dc
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117884"
---
# <a name="expressjs-app-converts-text-to-speech-with-cognitive-services-speech"></a>Text-zu-Sprache-Konvertierung durch Express.js-App mit Cognitive Services Speech

In diesem Tutorial fügen Sie Cognitive Services Speech einer vorhandenen Express.js-App hinzu, um die Text-zu-Sprache-Konvertierung mit dem Cognitive Services Speech-Dienst zu ermöglichen. Bei der Text-zu-Sprache-Konvertierung erhalten Sie Audiodaten, ohne dass Sie diese manuell generieren müssen. 

In diesem Tutorial werden drei verschiedene Vorgehensweisen der Text-zu-Sprache-Konvertierung mit Azure Cognitive Services Speech veranschaulicht:

* Direktes Abrufen der Audiodaten durch den JavaScript-Code des Clients 
* Abrufen der Audiodaten durch den JavaScript-Code des Servers aus einer Datei (*.MP3)
* Abrufen der Audiodaten aus In-Memory-arrayBuffer durch den JavaScript-Code des Servers

## <a name="application-architecture"></a>Anwendungsarchitektur

Im Tutorial wird eine minimal ausgestattete Express.js-App verwendet, und die Funktionen werden über eine Kombination der folgenden Elemente hinzugefügt:

* Neue Route für die Server-API für die Text-zu-Sprache-Konvertierung, bei der ein MP3-Stream zurückgegeben wird
* Neue Route für ein HTML-Formular, damit Sie Ihre Informationen eingeben können
* Neues HTML-Formular mit JavaScript zum Durchführen eines clientseitigen Aufrufs des Speech-Diensts

Diese Anwendung verfügt über drei verschiedene Aufrufe für die Sprache-in-Text-Konvertierung:

* Mit dem ersten Serveraufruf wird eine Datei auf dem Server erstellt, die dann an den Client zurückgegeben wird. Dieser Aufruf wird normalerweise für längere oder mehr als einmal bereitzustellende Texte genutzt. 
* Der zweite Serveraufruf ist für Texte bestimmt, die kurzfristig genutzt werden, und die Daten werden im Speicher vorgehalten, bevor sie an den Client zurückgegeben werden. 
* Der Clientaufruf veranschaulicht einen direkten Aufruf des Speech-Diensts per SDK. Sie können diesen Aufruf auch nutzen, wenn Sie eine reine Clientanwendung ohne Server verwenden. 

## <a name="prerequisites"></a>Voraussetzungen


- [Node.js 10.1 oder höher und npm](https://nodejs.org/en/download) (installiert auf Ihrem lokalen Computer)
- [Visual Studio Code](https://code.visualstudio.com/) (installiert auf Ihrem lokalen Computer) 
- [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für VS Code (installiert aus VS Code)
- [Git](https://git-scm.com/downloads) zum Pushen an GitHub, wodurch wiederum die GitHub-Aktion aktiviert wird
- Verwenden Sie die [Azure Cloud Shell](/azure/cloud-shell/quickstart) und [![Eingebetteter Start](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Starten von Azure Cloud Shell") von Bash.](https://shell.azure.com)   
- Wenn Sie möchten, können Sie auch die Azure CLI [installieren](/cli/azure/install-azure-cli), um CLI-Verweisbefehle auszuführen.
   - Wenn Sie eine lokale Installation verwenden, melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei der Azure CLI an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.  Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).
  - Installieren Sie die Azure CLI-Erweiterungen bei der ersten Verwendung, wenn Sie dazu aufgefordert werden.  Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Führen Sie [az version](/cli/azure/reference-index?#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="download-sample-expressjs-repo"></a>Herunterladen des Express.js-Beispielrepositorys 

1. Verwenden Sie Git, um das Express.js-Beispielrepository auf Ihrem lokalen Computer zu klonen. 

    ```bash
    git clone https://github.com/Azure-Samples/js-e2e-express-server
    ```

1. Wechseln Sie in das neue Verzeichnis mit dem Beispiel.

    ```bash
    cd js-e2e-express-server
    ```

1. Öffnen Sie das Projekt in Visual Studio Code.

    ```bash
    code .
    ```

1. Öffnen Sie in Visual Studio Code ein neues Terminal, und installieren Sie die Projektabhängigkeiten.

    ```bash
    npm install
    ```

## <a name="install-cognitive-services-speech-sdk-for-javascript"></a>Installieren des Cognitive Services Speech SDK für JavaScript

Installieren Sie über das Visual Studio Code-Terminal das Azure Cognitive Services Speech SDK.

```bash
npm install microsoft-cognitiveservices-speech-sdk
```

## <a name="create-a-speech-module-for-the-expressjs-app"></a>Erstellen eines Speech-Moduls für die Express.js-App

1. Erstellen Sie im Ordner `src` eine Datei mit dem Namen `azure-cognitiveservices-speech.js`, um das Speech SDK in die Express.js-Anwendung zu integrieren.
1. Fügen Sie den folgenden Code hinzu, um Abhängigkeiten zu pullen und eine Funktion für die Text-zu-Sprache-Konvertierung zu erstellen.

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/azure-cognitiveservices-speech.js" highlight="3,21,32" :::

    * Parameter: Mit der Datei werden die Abhängigkeiten für die Nutzung des SDK, der Streams, der Puffer und des Dateisystems (fs) gepullt. Für die Funktion `textToSpeech` werden vier Argumente verwendet. Wenn ein Dateiname mit einem lokalen Pfad gesendet wird, wird der Text in eine Audiodatei konvertiert. Wenn kein Dateiname gesendet wird, wird ein In-Memory-Audiostream erstellt. 
    * Speech SDK-Methode: Mit der Speech SDK-Methode [synthesizer.speakTextAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#speakTextAsync_string___e__SpeechSynthesisResult_____void___e__string_____void__AudioOutputStream___PushAudioOutputStreamCallback___PathLike_) werden basierend auf der empfangenen Konfiguration unterschiedliche Typen zurückgegeben. 
        Das von der Methode zurückgegebene Ergebnis hängt davon ab, was damit erreicht werden soll:
        * Datei erstellen 
        * Erstellen eines In-Memory-Datenstroms als Array mit Puffern
    * Audioformat: Das ausgewählte Audioformat ist MP3. Es gibt aber auch [andere Formate](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest) und andere [Methoden für die Audiokonfiguration](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest#methods). 

    Mit der lokalen Methode `textToSpeech` wird die SDK-Rückruffunktion umschlossen und in eine Zusage konvertiert. 

## <a name="create-a-new-route-for-the-expressjs-app"></a>Erstellen einer neuen Route für die Express.js-App

1. Öffnen Sie die Datei `src/server.js`. 
1. Fügen Sie das Modul `azure-cognitiveservices-speech.js` oben in der Datei als Abhängigkeit hinzu:

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="3"  :::
    

1. Fügen Sie eine neue API-Route für den Aufruf der **textToSpeech**-Methode hinzu, die im vorherigen Abschnitt des Tutorials erstellt wurde. 

    :::code language="javascript" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/src/server.js" range="30-51" highlight="45-50" :::

    Bei dieser Methode werden die erforderlichen und optionalen Parameter für die `textToSpeech`-Methode aus der Abfragezeichenfolge verwendet. Wenn eine Datei erstellt werden muss, wird ein eindeutiger Dateiname entwickelt. Die `textToSpeech`-Methode wird asynchron aufgerufen, und das Ergebnis wird per Pipe-Zeichen mit dem Antwortobjekt (`res`) verbunden. 

## <a name="update-the-client-web-page-with-a-form"></a>Aktualisieren der Clientwebseite mit einem Formular 

Aktualisieren Sie die HTML-Webseite des Clients mit einem Formular, mit dem die erforderlichen Parameter erfasst werden. Der optionale Parameter wird basierend darauf übergeben, welches Audiosteuerelement der Benutzer auswählt. Da dieses Tutorial ein Verfahren zum Aufrufen des Azure Speech-Diensts über den Client umfasst, ist auch der entsprechende JavaScript-Code angegeben. 

Öffnen Sie die Datei `/public/client.html`, und ersetzen Sie ihren Inhalt durch Folgendes:

:::code language="html" source="~/../js-e2e-express-server-cognitive-services/text-to-speech/public/client.html" highlight="75, 102 137" :::

Hervorgehobene Zeilen in der Datei: 

* Zeile 74: Das Azure Speech SDK wird in die Clientbibliothek gepullt, und die Site `cdn.jsdelivr.net` wird verwendet, um das npm-Paket bereitzustellen. 
* Zeile 102: Mit der `updateSrc`-Methode wird die `src`-URL der Audiosteuerelemente mit der Abfragezeichenfolge aktualisiert (z. B. Schlüssel, Region und Text). 
* Zeile 137: Wenn ein Benutzer die Schaltfläche `Get directly from Azure` auswählt, ruft die Webseite Azure direkt über die Clientseite auf und verarbeitet das Ergebnis. 

## <a name="create-cognitive-services-speech-resource"></a>Erstellen einer Cognitive Services Speech-Ressource

Erstellen Sie die Speech-Ressource mit Azure CLI-Befehlen in einer Azure Cloud Shell-Instanz.


1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com) an. Hierfür müssen Sie sich in einem Browser mit einem Konto authentifizieren, das über die Berechtigung zur Verwendung eines gültigen Azure-Abonnements verfügt. 
1. Erstellen Sie eine Ressourcengruppe für Ihre Speech-Ressource. 

    ```azurecli
    az group create \
        --location eastus \
        --name tutorial-resource-group-eastus
    ```

1. Erstellen Sie in der Ressourcengruppe eine Speech-Ressource.

    ```azurecli
    az cognitiveservices account create \
        --kind SpeechServices \
        --location eastus \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --sku F0
    ```

    Für diesen Befehl tritt ein Fehler auf, falls Ihre kostenlose Speech-Ressource bereits erstellt wurde. 

1. Verwenden Sie den Befehl, um die Schlüsselwerte für die neue Speech-Ressource abzurufen. 

    ```azurecli
    az cognitiveservices account keys list \
        --name tutorial-speech \
        --resource-group tutorial-resource-group-eastus \
        --output table
    ```

1. Kopieren Sie einen der Schlüssel. 

    Sie verwenden den Schlüssel im Web Form für die Authentifizierung beim Azure Speech-Dienst.

## <a name="run-the-expressjs-app-to-convert-text-to-speech"></a>Ausführen der Express.js-App für die Text-zu-Sprache-Konvertierung

1. Starten Sie die App mit dem folgenden Bash-Befehl.

    ```bash
    npm start
    ```

1. Öffnen Sie die Web-App in einem Browser.

    ```
    http://localhost:3000    
    ```

1. Fügen Sie Ihren Speech-Schlüssel in das hervorgehobene Textfeld ein. 

    :::image type="content" source="../media/speech-tutorial/expressjs-webapp-form-with-speech-key-field-highlighted.png" alt-text="Screenshot des Browsers: Web Form mit hervorgehobenem Feld für Eingabe des Speech-Schlüssels":::

1. Wenn Sie möchten, können Sie den Text ändern. 

1. Wählen Sie eine der drei Schaltflächen aus, um die Konvertierung in das Audioformat zu starten:
    * Direktes Abrufen aus Azure: Clientseitiger Aufruf von Azure
    * Audiosteuerelement für Audiodaten aus Datei
    * Audiosteuerelement für Audiodaten aus Puffer

    Unter Umständen kommt es zwischen der Auswahl des Steuerelements und der Wiedergabe der Audiodaten zu einer kurzen Verzögerung. 

## <a name="create-new-azure-app-service-in-visual-studio-code"></a>Erstellen einer neuen Azure App Service-Instanz in Visual Studio Code

1. Geben Sie in der Befehlspalette (**STRG**+**UMSCHALT**+**P**) den Befehl „create web“ein, und wählen Sie **Azure App Service: Create New Web App...Advanced** (Azure App Service: Neue Web-App erstellen...Erweitert) aus. Sie verwenden den erweiterten Befehl anstelle der Linux-Standardeinstellungen, um die vollständige Kontrolle über die Bereitstellung zu haben, einschließlich Ressourcengruppe, App Service-Plan und Betriebssystem.

1. Gehen Sie bei den Aufforderungen wie folgt vor:

    - Wählen Sie Ihr **Abonnementkonto** aus.
    - Geben Sie unter **Geben Sie einen global eindeutigen Namen ein** einen Namen wie `my-text-to-speech-app` ein. 
        - Der eingegebene Name muss innerhalb von Azure eindeutig sein. Verwenden Sie nur alphanumerische Zeichen („A - Z“, „a - z“ und „0 - 9“) und Bindestriche („-“).
    - Wählen Sie `tutorial-resource-group-eastus` als Ressourcengruppe aus.
    - **Wählen Sie als Runtimestapel** die Option `Node 10.1` oder höher aus. 
    - Wählen Sie das Linux-Betriebssystem aus.
    - Klicken Sie auf **Einen neuen App Services-Plan erstellen**, geben Sie einen Namen wie `my-text-to-speech-app-plan` an, und wählen Sie dann den [Tarif](../core/what-is-azure-for-javascript-development.md#free-tier-resources) **F1 Free** aus.
    - Wählen Sie **F1** (Free-Tarif) aus.
    - Wählen Sie für die Application Insights-Ressource die Option **Vorerst überspringen** aus.
    - Wählen Sie den Standort `eastus` aus. 

1. Nach kurzer Zeit erhalten Sie von Visual Studio Code die Benachrichtigung, dass die Erstellung abgeschlossen ist. Schließen Sie die Benachrichtigung mit der Schaltfläche **X**.

## <a name="deploy-local-expressjs-app-to-remote-app-service-in-visual-studio-code"></a>Bereitstellen einer lokalen Express.js-App für eine App Service-Remoteinstanz in Visual Studio Code

1. Da die Web-App nun vorhanden ist, können Sie Ihren Code über den lokalen Computer bereitstellen. Wählen Sie das Azure-Symbol aus, um den **Azure App Service**-Explorer zu öffnen. Erweitern Sie anschließend Ihren Abonnementknoten, klicken Sie mit der rechten Maustaste auf den Namen der gerade erstellten Web-App, und wählen Sie **In Web-App bereitstellen** aus.

1. Wählen Sie in den entsprechenden Aufforderungen den Stammordner der Express.js-App, erneut Ihr **Abonnementkonto** und dann den Namen der zuvor erstellten Web-App `my-text-to-speech-app` aus.

1. Wählen Sie bei der Bereitstellung unter Linux **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Zielserver zu aktualisieren.

    ![Aufforderung zum Aktualisieren der Konfiguration auf dem Linux-Zielserver](../media/deploy-azure/server-build.png)

1. Wählen Sie nach Abschluss der Bereitstellung in der Aufforderung die Option **Website durchsuchen** aus, um Ihre neu bereitgestellte Web-App anzuzeigen. 

1. (Optional): Sie können Änderungen an Ihren Codedateien vornehmen und dann in der Azure App Service-Erweiterung die Option **In Web-App bereitstellen** verwenden, um die Web-App zu aktualisieren.

## <a name="stream-remote-service-logs-in-visual-studio-code"></a>Streamen von Remotedienstprotokollen in Visual Studio Code

Zeigen Sie mithilfe von Aufrufen von `console.log` eine beliebige Ausgabe an, die die ausgeführte App generiert. Diese Ausgabe wird im Fenster **Ausgabe** in Visual Studio Code angezeigt.

1. Klicken Sie im **Azure App Service**-Explorer mit der rechten Maustaste auf Ihren neuen App-Knoten, und wählen Sie **Start Streaming Logs** (Streamen der Protokolle starten) aus.

    <pre>
    Starting Live Log Stream ---
    </pre>

1. Aktualisieren Sie die Webseite im Browser einige Male, um die zusätzliche Protokollausgabe anzuzeigen.


## <a name="clean-up-resources-by-removing-resource-group"></a>Bereinigen der Ressourcen durch das Entfernen der Ressourcengruppe

Nach Abschluss dieses Tutorials müssen Sie die Ressourcengruppe entfernen, in der die Ressource enthalten ist, um sicherzustellen, dass Ihnen hierfür keine weiteren Nutzungskosten in Rechnung gestellt werden. 

Verwenden Sie in Azure Cloud Shell den [Azure CLI-Befehl](/cli/azure/group#az_group_delete), um die Ressourcengruppe zu löschen:

```azurecli
az group delete --name tutorial-resource-group-eastus  -y
```

Die Ausführung dieses Befehls kann mehrere Minuten dauern. 

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Express.js-MongoDB-App in App Service über Visual Studio Code](deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)