---
title: 'Hochladen eines Images in Blob Storage mit VS Code: App Service/Cosmos DB'
description: Verwenden Sie eine React- oder TypeScript-App, um eine Datei in Azure Storage-Blobs hochzuladen. Dieses Tutorial konzentriert sich auf die Verwendung von lokalen und Remoteumgebungen mit Visual Studio Code-Erweiterungen.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript, azure-sdk-storage-blob-typescript-version-12.2.1
ms.openlocfilehash: 116ca6d03703e96631441a5e130d948065c5c5f1
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118542"
---
# <a name="upload-an-image-to-an-azure-storage-blob"></a>Hochladen eines Images in ein Azure Storage-Blob

Verwenden Sie eine clientseitige React-App, um eine Imagedatei mithilfe eines Azure Storage-nmp-Pakets in ein Azure Storage-Blob hochzuladen. 

Die TypeScript-Programmieraufgaben wurden für Sie erledigt. Dieses Tutorial konzentriert sich auf die erfolgreiche Verwendung der lokalen Umgebung und der Azure-Remoteumgebungen in Visual Studio Code mit Azure-Erweiterungen.

* [**Beispielcode**](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)

## <a name="application-architecture-and-functionality"></a>Anwendungsarchitektur und Funktionalität

Dieses Tutorial enthält mehrere wichtige Azure-Aufgaben für JavaScript-Entwickler:

* Lokales Ausführen einer React- oder TypeScript-App mit Visual Studio Code
* Erstellen einer **Azure Storage Blob**-Ressource und Konfiguration für Dateiuploads
    * Konfigurieren von CORS
    * Erstellen eines SAS-Tokens (Shared Access Signatures)
* Konfigurieren von Code für die Azure SDK-Clientbibliothek zur Verwendung von SAS-Token für die Authentifizierung beim Dienst

Die [React-Beispiel-App](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob), die auf GitHub verfügbar ist, besteht aus den folgenden Elementen:

* **React-App**, gehostet an Port 3000
* **Azure SDK-Clientbibliotheksskript** zum Hochladen in Storage-Blobs

:::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Einfache React-App, die mit Azure Storage-Blobs verbunden ist.":::

## <a name="1-set-up-development-environment"></a>1. Einrichten der Entwicklungsumgebung

- Ein Azure-Benutzerkonto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
- [Node.js 10 und npm:](https://nodejs.org/en/download) Der Node.js-Paket-Manager, der auf dem lokalen Computer installiert ist
- [Visual Studio Code](https://code.visualstudio.com/) auf dem lokalen Computer installiert. 
- Visual Studio Code-Erweiterungen:
    - [Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage): Zum Anzeigen der Storage-Ressource

## <a name="2-clone-and-run-the-initial-react-app"></a>2. Klonen und Ausführen der anfänglichen React-App

1. Öffnen Sie Visual Studio Code.
1. Klonen Sie das GitHub-Repository, indem Sie das Git-Symbol und dann **Klonen** auswählen. Für diesen Vorgang müssen Sie sich bei GitHub anmelden. Verwenden Sie die Repository-URL: 

    `https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob`

1. Wählen Sie einen Ordner auf dem lokalen Computer aus, in den das Beispiel geklont werden soll. Wenn Sie dazu aufgefordert werden, öffnen Sie das geklonte Repository. 

    :::image type="content" source="../media/tutorial-browser-file-upload/vscode-git-clone-repository.png" alt-text="Klonen Sie das GitHub-Repository, indem Sie das Git-Symbol und dann „Repository klonen“ auswählen.":::

1. Öffnen Sie in Visual Studio Code ein Terminalfenster, und führen Sie den folgenden Befehl aus, um die Abhängigkeiten des Beispiels zu installieren.

    ```javascript
    npm install
    ```

1. Führen Sie im gleichen Terminalfenster den Befehl zum Ausführen der Web-App aus.

    ```javascript
    npm start
    ```

1. Öffnen Sie einen Webbrowser, und verwenden Sie die folgende URL, um die Web-App auf Ihrem lokalen Computer anzuzeigen.

    ```url
    http://localhost:3000/
    ```

    Wenn die einfache Web-App in Ihrem Browser mit dem Text angezeigt wird, dass der Speicher nicht konfiguriert ist, haben Sie diesen Abschnitt des Tutorials erfolgreich durchgearbeitet.

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-no-azure-storage-resource-configured.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::

1. Beenden Sie den Code mit STRG+C im Visual Studio Code-Terminal.

## <a name="3-create-storage-resource-with-visual-studio-extension"></a>3. Erstellen einer Storage-Ressource mit der Visual Studio-Erweiterung

1. Navigieren Sie zur Azure Storage-Erweiterung. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann `Create Storage Account...` aus.

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource.png" alt-text="Navigieren Sie zur Azure Storage-Erweiterung. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann „Speicherkonto erstellen“ aus.":::

1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie die Storage-Ressource erstellt wird.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen Namen für die neue Web-App ein.| Geben Sie einen Wert wie `fileuploaddemo` als Name der Storage-Ressource ein.<br><br> Dieser eindeutige Name ist **Ihr Ressourcenname**, der im nächsten Abschnitt verwendet wird. Verwenden Sie nur Zeichen und Zahlen (maximal 24 Zeichen). Dieser **Kontoname** ist in einem späteren Schritt erforderlich.|

1. Nachdem der App-Erstellungsprozess beendet wurde, wird eine Benachrichtigung mit Informationen zur neuen Ressource angezeigt. 

    :::image type="content" source="../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource-complete.png" alt-text="Nachdem der App-Erstellungsprozess abgeschlossen ist, wird eine Benachrichtigung mit Informationen zur neuen Ressource angezeigt.":::

## <a name="4-set-storage-account-name-in-code-file"></a>4. Festlegen des Speicherkontonamens in der Codedatei

Legen Sie den Ressourcennamen aus dem vorherigen Schritt in `src/azure-storage-blob.ts` fest. 

```typescript
const storageAccountName = process.env.storageresourcename || "fileuploaddemo"; 
```

## <a name="5-generate-your-shared-access-signature-sas-token"></a>5. Generieren des SAS-Tokens (Shared Access Signature) 

Generieren Sie das SAS-Token, bevor Sie CORS konfigurieren. 

1. Klicken Sie in der Visual Studio Code-Erweiterung für Storage mit der rechten Maustaste auf die Ressource, und wählen Sie dann **Im Portal öffnen** aus. Dadurch wird das Azure-Portal für Ihre Storage-Ressource geöffnet.
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Shared Access Signature (SAS)** aus. 
1. Konfigurieren Sie das SAS-Token mit den folgenden Einstellungen. 

    | Eigenschaft|Wert|
    |--|--|
    |Zulässige Dienste|Blob|
    |Zulässige Ressourcentypen|Dienst, Container, Objekt|
    |Zulässige Berechtigungen|Lesen, Schreiben, Löschen, Auflisten, Hinzufügen, Erstellen|
    |Löschungen der Version aktivieren|Überprüft|
    |Datum/Uhrzeit für Start und Ablauf|Akzeptieren Sie das Startdatum und die Uhrzeit, und **legen Sie das Enddatum in 24 Stunden fest**. Ihr SAS-Token ist nur 24 Stunden lang gültig.|
    |Nur HTTPS|Ausgewählt|
    |Bevorzugte Routingebene|Basic|
    |Signaturschlüssel|key1 ausgewählt|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-generate-sas-token.png" alt-text="Konfigurieren Sie das SAS-Token wie in der Abbildung gezeigt. Die Einstellungen werden unter der Abbildung erläutert.":::

1. Wählen Sie **SAS und Verbindungszeichenfolge generieren** aus. Kopieren Sie das SAS-Token sofort. Sie können dieses Token nicht auflisten. Wenn Sie es nicht kopiert haben, müssen Sie ein neues SAS-Token generieren. 

## <a name="set-sas-token-in-code-file"></a>Festlegen des SAS-Tokens in der Codedatei

Das SAS-Token wird verwendet, wenn Abfragen an die cloudbasierte Ressource gesendet werden.

1. Wenn das Token mit einem Fragezeichen (`?`) beginnt, entfernen Sie es. Die Codedatei stellt `?` für Sie bereit, sodass Sie das Fragezeichen nicht im Token benötigen.

1. Legen Sie das SAS-Token in `src/azure-storage-blob.ts` für den sasToken-Wert fest, indem Sie das SAS-Token der leeren Zeichenfolge hinzufügen. Lassen Sie den restlichen Code unverändert. 

```typescript
// remove `?` if it is first character of token
const sasToken = process.env.storagesastoken || "";
```

## <a name="6-configure-cors-for-azure-storage-resource"></a>6. Konfigurieren von CORS für die Azure Storage-Ressource

Konfigurieren Sie CORS für Ihre Ressource, damit der clientseitige React-Code auf Ihr Speicherkonto zugreifen kann. 

1. Wählen Sie im Azure-Portal im Abschnitt „Einstellungen“ die Option **CORS** aus. 
1. Konfigurieren Sie CORS wie in der Abbildung gezeigt. Die Einstellungen werden unter der Abbildung erläutert. 

    | Eigenschaft|Wert|
    |--|--|
    |Zulässige Ursprünge|`*`|
    |Zulässige Methoden|Alle außer Patch.|
    |Zulässige Header|`*`|
    |Verfügbar gemachte Header|`*`|
    |Maximales Alter|86.400|

    :::image type="content" source="../media/tutorial-browser-file-upload/azure-portal-storage-blob-cors.png" alt-text="Konfigurieren Sie CORS wie in der Abbildung gezeigt. Die Einstellungen werden unter der Abbildung erläutert.":::

1. Wählen Sie **Speichern** über den Einstellungen aus, um die Einstellung in der Ressource zu speichern. Für den Code sind keine Änderungen erforderlich, um mit diesen CORS-Einstellungen arbeiten zu können. 

## <a name="7-run-project-locally-to-verify-connection-to-storage-account"></a>7. Führen Sie das Projekt lokal aus, um die Verbindung mit dem Storage-Konto zu bestätigen.

Ihr SAS-Token und der Name des Speicherkontos werden in der Datei `src/azure-storage-blob.ts` festgelegt, sodass Sie bereit sind, die Anwendung auszuführen.

1. Wird die App nicht ausgeführt, starten Sie sie erneut:

    ```javascript
    npm start
    ```

1. Öffnen Sie die folgende URL in einem Browser:

    `http://localhost:3000` 

    :::image type="content" source="../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-configured-upload-button-displayed.png" alt-text="Die mit Azure Storage-Blobs verbundene React-Website sollte mit einer Schaltfläche für die Dateiauswahl und einer Schaltfläche für den Dateiupload angezeigt werden.":::

1. Wählen Sie ein Bild aus dem Ordner `images` zum Hochladen und dann **Hochladen** aus. Schaltfläche. 

    Der React-Clientcode des Front-Ends ruft `src/azure-storage-blob.ts` zur Authentifizierung bei Azure auf, erstellt dann einen Speichercontainer (falls noch nicht vorhanden) und lädt die Datei in diesen Container hoch. 

    Sie haben die Schritte des Tutorials abgeschlossen. Die restlichen Abschnitte enthalten eine Erläuterung zur App sowie Informationen zur Problembehandlung.

## <a name="troubleshoot-local-connection-to-storage-account"></a>Beheben von Problemen mit einer lokalen Verbindung mit einem Storage-Konto

Wenn Sie einen Fehler erhalten haben oder die Datei nicht in den Container hochgeladen wird, überprüfen Sie Folgendes:

* Erstellen Sie Ihr SAS-Token neu. Stellen Sie dabei sicher, dass das Token auf der Speicherressourcenebene und nicht auf der Containerebene erstellt wird. Kopieren Sie das neue Token an der richtigen Stelle in den Code.
* Stellen Sie sicher, das die in den Code kopierte Tokenzeichenfolge nicht mit einem Fragezeichen (`?`) beginnt.
* Überprüfen Sie die CORS-Einstellung für Ihre Speicherressource.

## <a name="upload-button-functionality"></a>Funktionalität der Schaltfläche „Hochladen“

Die TypeScript-Datei `src/App.tsx` wird als Teil der App-Erstellung mit create-reag-app bereitgestellt. Die Datei wurde geändert, um eine Schaltfläche für die Dateiauswahl und eine Schaltfläche für den Upload sowie den unterstützenden Code zur Bereitstellung dieser Funktionalität bereitzustellen. 

Der Code, mit dem eine Verbindung mit dem Azure Blob Storage-Code hergestellt wird, ist hervorgehoben. Der Aufruf von `uploadFileToBlob` gibt alle Blobs (Dateien) im Container als unformatierte Liste zurück. Diese Liste wird mit der `DisplayImagesFromContainer`-Funktion angezeigt.

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/App.tsx" highlight="3,28":::

## <a name="upload-file-to-azure-storage-blob-with-azure-sdk-client-library"></a>Hochladen einer Datei in Azure Storage-Blob mit der Azure SDK-Clientbibliothek

Der Code zum Hochladen der Datei in Azure Storage ist frameworkagnostisch. Da der Code für ein Tutorial erstellt wird, wird auf Einfachheit und Verständlichkeit geachtet. Diese Entscheidungen werden erläutert. Sie sollten Ihr eigenes Projekt auf beabsichtigte Nutzung, Sicherheit und Effizienz überprüfen. 

Das Beispiel erstellt und verwendet einen öffentlich zugänglichen Container und Dateien. Wenn Sie Dateien in Ihrem eigenen Projekt sichern möchten, stehen Ihnen viele Ebenen zur Verfügung, auf denen Sie dies steuern können, vom Erfordernis einer allgemeinen Authentifizierung für Ihre Ressource bis hin zu sehr spezifischen Berechtigungen für jedes Blobobjekt. 

### <a name="dependencies-and-variables"></a>Abhängigkeiten und Variablen

Die TypeScript-Datei `azure-storage-blob.ts` lädt die Abhängigkeiten und pullt die erforderlichen Variablen durch Umgebungsvariablen oder hartcodierte Zeichenfolgen.

| Variable | Beschreibung |
|--|--|
|`sasToken`|Dem mit dem Azure-Portal erstellten SAS-Token wird ein `?` vorangestellt. Entfernen Sie dieses, bevor Sie es in der `sasToken`-Variablen festlegen.| 
|`container`|Der Name des Containers im Blobspeicher. Sie können sich diesen als äquivalent zu einem Ordner oder Verzeichnis für ein Dateisystem vorstellen.|
|`storageAccountName`|Der Name Ihrer Ressource.|

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="2,5,16" id="snippet_package":::

### <a name="security-for-azure-credentials"></a>Sicherheit für Azure-Anmeldeinformationen

Überlegen Sie sich in Ihrem eigenen Projekt, wo Sie Geheimnisse wie ein SAS-Token speichern können. Wenn Ihre Anwendung erfordert, dass Sie Ihre Azure-Informationen sichern, ziehen Sie in Betracht, diesen Speichercode in einer [Azure-Funktion](/azure/azure-functions/) anstatt auf dem Client zu hosten, und rufen Sie dann die Azure-Funktion aus der react-App auf.  

### <a name="create-storage-client-and-manage-steps"></a>Erstellen des Storage-Clients und Verwaltungsschritte

Die `uploadFileToBlob`-Funktion ist die Hauptfunktion der Datei. Sie erstellt das Clientobjekt für den Storage-Dienst, erstellt dann den Client für das Containerobjekt, lädt die Datei hoch und ruft dann eine Liste aller Blobs im Container ab. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="5,6,7" id="snippet_uploadFileToBlob":::

### <a name="upload-file-to-blob"></a>Hochladen der Datei in das Blob

Die `createBlobInContainer`-Funktion lädt die Datei mit der `uploadBrowserData`-Clientbibliotheksmethode in den Container hoch. Der Inhaltstyp muss mit der Anforderung gesendet werden, wenn Sie beabsichtigen, Browserfunktionen zu verwenden, die vom Dateityp abhängen, z. B. das Anzeigen eines Bilds. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="10" id="snippet_createBlobInContainer":::

### <a name="get-list-of-blobs"></a>Abrufen einer Liste von Blobs

Die `getBlobsInContainer`-Funktion ruft eine Liste von URLs für die Blobs im Container ab. Die URLs werden erstellt, um als `src` einer Bildanzeige in HTML verwendet zu werden: `<img src={item} alt={item} height="200" />`. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/azure-storage-blob.ts" highlight="10" id="snippet_getBlobsInContainer":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie in Visual Studio Code den Azure-Explorer für Storage, klicken Sie mit der rechten Maustaste auf die Ressource, und wählen Sie dann **Storage-Konto löschen...** aus.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie mit dieser App fortfahren möchten, informieren Sie sich darüber, wie Sie die App zum Hosten mit einer der folgenden Optionen in Azure bereitstellen:

* [Hochladen als statische Web-App](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
* Hochladen in eine Web-App-Ressource mithilfe der [Visual Studio Code-Erweiterung für den App-Dienst](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
* [Hochladen einer App auf einen virtuellen Azure-Computer](nodejs-virtual-machine-vm/introduction.md)
