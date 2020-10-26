---
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
title: include file understand-the-code.md
description: include file understand-the-code.md
ms.openlocfilehash: fd9d2eb2c6c8dd3a39d737cdcdf609b9cf04cf7e
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344169"
---
In diesem Abschnitt des Tutorials erfahren Sie, wie der Code im Beispiel funktioniert, damit die Web-App eine Datei hochladen kann.

## <a name="sample-created-with-create-react-app"></a>Mit create-reag-app erstelltes Beispiel

Das [Beispiel](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob) ist eine einfache React-App, die mit [create-reag-app](https://create-react-app.dev/docs/adding-typescript/) mit der TypeScript-Vorlage erstellt wurde.

```typescript
npx create-react-app my-app --template typescript
```

Das create-reag-app-Framework ist für Folgendes nützlich:
* Automatisches Bereitstellen von Dateibündelung. Dies ist eine Voraussetzung für die Verwendung der Azure-Clientbibliotheken im Browser.
* Bereitstellen von Transpilation und Buildskripts 

## <a name="upload-button-functionality"></a>Funktionalität der Schaltfläche „Hochladen“

Die Datei `src/app` wird als Teil der App-Erstellung mit create-reag-app bereitgestellt. Die Datei wurde geändert, um eine Schaltfläche für die Dateiauswahl und eine Schaltfläche für den Upload sowie den unterstützenden Code zur Bereitstellung dieser Funktionalität bereitzustellen. 

Der Code, der eine Verbindung mit dem Azure Blob Storage-Code herstellt, ist hervorgehoben. Der Aufruf von `uploadFileToBlob` gibt alle Blobs (Dateien) im Container als unformatierte Liste zurück. Diese Liste wird mit der `DisplayImagesFromContainer`-Funktion angezeigt.

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/App.tsx" highlight="3,28":::

## <a name="upload-file-to-azure-storage-blob-with-azure-sdk-client-library"></a>Hochladen einer Datei in Azure Storage-Blob mit der Azure SDK-Clientbibliothek

Der Code zum Hochladen der Datei in Azure Storage ist frameworkagnostisch. Da der Code für ein Tutorial erstellt wird, wird auf Einfachheit und Verständlichkeit geachtet. Diese Entscheidungen werden erläutert. Sie sollten Ihr eigenes Projekt auf beabsichtigte Nutzung, Sicherheit und Effizienz überprüfen. 

Das Beispiel erstellt und verwendet einen öffentlich zugänglichen Container und Dateien. Wenn Sie Dateien in Ihrem eigenen Projekt sichern möchten, stehen Ihnen viele Ebenen zur Verfügung, auf denen Sie dies steuern können, vom Erfordernis einer allgemeinen Authentifizierung für Ihre Ressource bis hin zu sehr spezifischen Berechtigungen für jedes Blobobjekt. 

### <a name="dependencies-and-variables"></a>Abhängigkeiten und Variablen

Die Datei `uploadToBlob.ts` lädt die Abhängigkeiten und pullt die erforderlichen Variablen durch Umgebungsvariablen oder hartcodierte Zeichenfolgen.

| Variable | Beschreibung |
|--|--|
|`sasToken`|Dem mit dem Azure-Portal erstellten SAS-Token wird ein `?` vorangestellt. Entfernen Sie dieses, bevor Sie es in der `sasToken`-Variablen festlegen.| 
|`container`|Der Name des Containers im Blobspeicher. Sie können sich diesen als äquivalent zu einem Ordner oder Verzeichnis für ein Dateisystem vorstellen.|
|`storageAccountName`|Der Name Ihrer Ressource.|

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="2,5,16" id="snippet_package":::

### <a name="security-for-azure-credentials"></a>Sicherheit für Azure-Anmeldeinformationen

Überlegen Sie sich in Ihrem eigenen Projekt, wo Sie Geheimnisse wie ein SAS-Token speichern können. Wenn Ihre Anwendung erfordert, dass Sie Ihre Azure-Informationen sichern, ziehen Sie in Betracht, diesen Speichercode in einer [Azure-Funktion](/azure/azure-functions/) anstatt auf dem Client zu hosten, und rufen Sie dann die Azure-Funktion aus der react-App auf.  

### <a name="create-storage-client-and-manage-steps"></a>Erstellen des Storage-Clients und Verwaltungsschritte

Die `uploadFileToBlob`-Funktion ist die Hauptfunktion der Datei. Sie erstellt das Clientobjekt für den Storage-Dienst, erstellt dann den Client für das Containerobjekt, lädt die Datei hoch und ruft dann eine Liste aller Blobs im Container ab. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="5,6,7" id="snippet_uploadFileToBlob":::

### <a name="upload-file-to-blob"></a>Hochladen der Datei in das Blob

Die `createBlobInContainer`-Funktion lädt die Datei mit der `uploadBrowserData`-Clientbibliotheksmethode in den Container hoch. Der Inhaltstyp muss mit der Anforderung gesendet werden, wenn Sie beabsichtigen, Browserfunktionen zu verwenden, die vom Dateityp abhängen, z. B. das Anzeigen eines Bilds. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_createBlobInContainer":::

### <a name="get-list-of-blobs"></a>Abrufen einer Liste von Blobs

Die `getBlobsInContainer`-Funktion ruft eine Liste von URLs für die Blobs im Container ab. Die URLs werden erstellt, um als `src` einer Bildanzeige in HTML verwendet zu werden: `<img src={item} alt={item} height="200" />`. 

:::code language="typescript" source="~/../js-e2e-browser-file-upload-storage-blob/src/uploadToBlob.ts" highlight="10" id="snippet_getBlobsInContainer":::

