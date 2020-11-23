---
title: include file create-storage-resource.md
description: include file create-storage-resource.md
ms.date: 11/13/2020
ms.topic: include
ms.custom: devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: 19a21dbf557c31f7eeae6afdb4722bfed35c86fe
ms.sourcegitcommit: dc74b60217abce66fe6cc93923e869e63ac86a8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885357"
---
In diesem Abschnitt des Tutorials erstellen Sie die Azure Storage-Ressource mit einer Visual Studio-Erweiterung und konfigurieren die Ressource dann im Azure-Portal. 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](azure-sign-in.md)]

## <a name="create-storage-resource"></a>Erstellen einer Storage-Ressource 

Verwenden Sie die Visual Studio Code-Erweiterung zum Erstellen einer Storage-Ressource. 

1. Navigieren Sie zur Azure Storage-Erweiterung. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann `Create Storage Account...` aus.

    :::image type="content" source="../../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource.png" alt-text="Navigieren Sie zur Azure Storage-Erweiterung. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann „Speicherkonto erstellen“ aus.":::

1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie Ihre Werte verwendet werden.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen Namen für die neue Web-App ein.| Geben Sie einen Wert wie `fileuploadyourname` als Name der Storage-Ressource ein. Ersetzen Sie `yourname` durch Ihren Namen in Kleinbuchstaben oder Ihre eindeutige ID. Dieser eindeutige Name wird auch als Teil der URL verwendet, um in einem Browser auf die Ressource zuzugreifen. Verwenden Sie nur Zeichen und Zahlen (maximal 24 Zeichen). Dieser **Kontoname** ist in einem späteren Schritt erforderlich.|

1. Nachdem der App-Erstellungsprozess beendet wurde, wird eine Benachrichtigung mit Informationen zur neuen Ressource angezeigt. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/visualstudiocode-storage-extension-create-resource-complete.png" alt-text="Nachdem der App-Erstellungsprozess abgeschlossen ist, wird eine Benachrichtigung mit Informationen zur neuen Ressource angezeigt.":::

## <a name="set-storage-account-name-in-code-file"></a>Festlegen des Speicherkontonamens in der Codedatei

Legen Sie den Ressourcennamen in `src/uploadToBlob.ts` für den `storageAccountName`-Wert fest, indem Sie den Namen des Speicherschlüssels der leeren Zeichenfolge hinzufügen. Lassen Sie den restlichen Code unverändert. 

```typescript
const storageAccountName = process.env.storageresourcename || ""; 
```

## <a name="generate-your-shared-access-signature-sas-token"></a>Generieren des SAS-Tokens (Shared Access Signature) 

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
    |Datum/Uhrzeit für Start und Ablauf|Akzeptieren Sie das Startdatum und die Uhrzeit, und legen Sie das Enddatum in 24 Stunden fest. Ihr SAS-Token ist nur 24 Stunden lang gültig.|
    |Nur HTTPS|Ausgewählt|
    |Bevorzugte Routingebene|Basic|
    |Signaturschlüssel|key1 ausgewählt|

    :::image type="content" source="../../media/tutorial-browser-file-upload/azure-portal-storage-blob-generate-sas-token.png" alt-text="Konfigurieren Sie das SAS-Token wie in der Abbildung gezeigt. Die Einstellungen werden unter der Abbildung erläutert.":::

1.  Wählen Sie **SAS und Verbindungszeichenfolge generieren** aus. Kopieren Sie das SAS-Token sofort. Sie können dieses Token nicht auflisten. Wenn Sie es nicht kopiert haben, müssen Sie ein neues SAS-Token generieren. 

## <a name="set-sas-token-in-code-file"></a>Festlegen des SAS-Tokens in der Codedatei

Der SAS-Tokenwert ist eine partielle Abfragezeichenfolge, die in der URL verwendet wird, wenn Abfragen der cloudbasierten Ressource vorgenommen werden.

Das Tokenformat hängt davon ab, welches Tool Sie verwendet haben, um das Token zu erstellen: 
* **Azure-Portal**: Wenn Sie das SAS-Token im Portal erstellen, enthält das Token `?` als erstes Zeichen der Zeichenfolge.
* **Azure CLI**: Wenn Sie das SAS-Token mit dem Azure CLI erstellen, enthält der zurückgegebene Wert nicht `?` als erstes Zeichen der Zeichenfolge. 

1. Entfernen Sie `?`, wenn dies das erste Zeichen des Tokens ist. Die Codedatei stellt `?` für Sie bereit, sodass Sie das Fragezeichen nicht im Token benötigen.

1. Legen Sie das SAS-Token in `src/uploadToBlob.ts` für den sasToken-Wert fest, indem Sie das SAS-Token der leeren Zeichenfolge hinzufügen. Lassen Sie den restlichen Code unverändert. 

```typescript
// remove `?` if it is first character of token
const sasToken = process.env.storagesastoken || "";
```

## <a name="configure-your-azure-storage-resource-for-cors-with-azure-cli"></a>Konfigurieren der Azure Storage-Ressource für CORS mit der Azure CLI

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

    :::image type="content" source="../../media/tutorial-browser-file-upload/azure-portal-storage-blob-cors.png" alt-text="Konfigurieren Sie CORS wie in der Abbildung gezeigt. Die Einstellungen werden unter der Abbildung erläutert.":::

1. Wählen Sie **Speichern** über den Einstellungen aus, um die Einstellung in der Ressource zu speichern. Für den Code sind keine Änderungen erforderlich, um mit diesen CORS-Einstellungen arbeiten zu können. 

## <a name="run-project-locally-to-verify-connection-to-storage-account"></a>Führen Sie das Projekt lokal aus, um die Verbindung mit dem Storage-Konto zu bestätigen.

Ihr SAS-Token und der Name des Speicherkontos werden in der Datei `src/uploadToBlob.ts` festgelegt, sodass Sie bereit sind, die Anwendung auszuführen.

1. Geben Sie im Visual Studio Code-Terminal den folgenden Befehl ein:

    ```javascript
    npm start
    ```

1. Wenn das Terminal die URL anzeigt (z. B. `http://localhost:3000`) ist Ihre App bereit. Öffnen Sie einen Browser, und geben Sie diese URL ein. Die mit Azure Storage-Blobs verbundene Website sollte mit einer Schaltfläche für die Dateiauswahl und einer Schaltfläche für den Dateiupload angezeigt werden. 

    :::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-configured-upload-button-displayed.png" alt-text="Die mit Azure Storage-Blobs verbundene React-Website sollte mit einer Schaltfläche für die Dateiauswahl und einer Schaltfläche für den Dateiupload angezeigt werden.":::

1. Wählen Sie ein Bild aus dem Ordner `images` zum Hochladen aus. `spring-flowers.jpg` sind ein gutes visuelles Element für diesen Test. Wählen Sie dann die Schaltfläche **Hochladen** aus. Schaltfläche. 

    Der React-Clientcode des Front-Ends ruft `src/uploadToBlob.ts` zur Authentifizierung bei Azure auf, erstellt dann einen Speichercontainer (falls noch nicht vorhanden) und lädt die Datei in diesen Container hoch. 

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie einen Fehler erhalten haben oder die Datei nicht in den Container hochgeladen wird, überprüfen Sie Folgendes:

* Erstellen Sie Ihr SAS-Token neu. Stellen Sie dabei sicher, dass das Token auf der Speicherressourcenebene und nicht auf der Containerebene erstellt wird. Kopieren Sie das neue Token an der richtigen Stelle in den Code.
* Stellen Sie sicher, das die in den Code kopierte Tokenzeichenfolge nicht mit einem Fragezeichen (`?`) beginnt.
* Überprüfen Sie die CORS-Einstellung für Ihre Speicherressource.

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren? 

Weitere Möglichkeiten zum Konfigurieren des Storage-Kontos:
* SAS-Token mit [PowerShell](/powershell/module/azure.storage/new-azurestorageblobsastoken)
* SAS-Token mit Portal
* CORS mit [PowerShell](/powershell/module/azure.storage/set-azurestoragecorsrule)
* CORS mit Portal

Weitere Informationen zu [SAS (Shared Access Signature)](/azure/storage/common/storage-sas-overview).
