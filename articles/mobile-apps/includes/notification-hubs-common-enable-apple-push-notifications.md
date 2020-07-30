---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: f83e5ae060ee6b13523af8dbb271906dff02e027
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401500"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registrieren der iOS-App für Pushbenachrichtigungen

Zum Senden von Pushbenachrichtigungen an eine iOS-App registrieren Sie Ihre Anwendung bei Apple, und registrieren Sie sie auch für Pushbenachrichtigungen.  

1. Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Apple Developer Center zum [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456). Melden Sie sich mit Ihrer Apple-ID beim Portal an, navigieren Sie zu **Zertifikate, Bezeichner und Profile**, und wählen Sie dann **Bezeichner** aus. Klicken Sie auf **+** , um eine neue App zu registrieren.

    ![iOS-Bereitstellungsportal: Seite mit App-IDs](../media/notification-hubs-ios-appids.png)

1. Aktivieren Sie auf dem Bildschirm zum **Registrieren eines neuen Bezeichners** das Optionsfeld **App-IDs**. Klicken Sie anschließend auf **Weiter**.

    ![Seite zum Registrieren einer neuen ID im iOS-Bereitstellungsportal](../media/notification-hubs-ios-appids-new.png)

1. Aktualisieren Sie die folgenden drei Werte für Ihre neue App, und wählen Sie dann **Weiter** aus:

   * **Beschreibung**: Geben Sie einen aussagekräftigen Namen für Ihre App ein.

   * **Paket-ID**: Geben Sie eine Bundle-ID im Format **com.<Organisationsbezeichner>.<Produktname>** ein, wie im [Leitfaden zur App-Verteilung](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) erwähnt. Im folgenden Screenshot wird der Wert `mobcat` als Organisationsbezeichner und der Wret **PushDemo** als Produktname verwendet.

      ![Seite zum Registrieren einer App-ID im iOS-Bereitstellungsportal](../media/notification-hubs-new-appid-bundle.png)

   * **Push Notifications** (Pushbenachrichtigungen): Aktivieren Sie die Option **Pushbenachrichtigungen** im Bereich **Funktionen**.

      ![Formular zum Registrieren einer neuen App-ID](../media/notification-hubs-new-appid-push.png)

      Hierdurch wird Ihre App-ID generiert, und Sie werden zur Bestätigung der Daten aufgefordert. Wählen Sie **Weiter** und dann **Registrieren** aus, um die neue App-ID zu bestätigen.

      ![Neue App-ID bestätigen](../media/notification-hubs-new-appid-register.png)

      Nachdem Sie **Registrieren** ausgewählt haben, wird die neue App-ID als Position auf der Seite **Zertifikate, Bezeichner und Profile** angezeigt.

1. Suchen Sie auf der Seite **Zertifikate, Bezeichner und Profile** unter **Bezeichner** das von Ihnen erstellte Zeilenelement „App-ID“. Wählen Sie dann die entsprechende Zeile aus, um den Bildschirm **App-ID-Konfiguration bearbeiten** anzuzeigen.

#### <a name="creating-a-certificate-for-notification-hubs"></a>Erstellen eines Zertifikats für Notification Hubs

Ein Zertifikat ist erforderlich, damit der Benachrichtigungshub mit **Apple Push Notification Services (APNS)** arbeiten und auf eine von zwei Arten bereitgestellt werden kann:

1. [Erstellen eines P12-Pushzertifikats, das direkt in den Benachrichtigungshub hochgeladen werden kann](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*der ursprüngliche Ansatz*)

1. [Erstellen eines P8-Zertifikats, das für die tokenbasierte Authentifizierung verwendet werden kann](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*der neuere und empfohlene Ansatz*)

Die neuere Methode bietet eine Reihe von Vorteilen. Diese sind unter [Tokenbasierte Authentifizierung (HTTP/2) für APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) dokumentiert. Es sind weniger Schritte erforderlich, sie ist zudem aber für bestimmte Szenarien vorgeschrieben. Es werden jedoch die Schritte für beide Ansätze beschrieben, da beide für die Zwecke dieses Tutorials funktionieren.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPTION 1: Erstellen eines P12-Pushzertifikats, das direkt in den Notification Hub hochgeladen werden kann

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Es kann im Ordner **Dienstprogramme** oder im Ordner **Andere** auf dem Launchpad geöffnet werden.

1. Wählen Sie die Option **Schlüsselbundverwaltung** aus, erweitern Sie **Zertifikatsassistent**, und wählen Sie dann **Zertifikat einer Zertifizierungsinstanz anfordern** aus.

    ![Anfordern eines neuen Zertifikats mithilfe der Schlüsselbundverwaltung](../media/notification-hubs-request-certificate-from-certificate-assistant.png)

   > [!NOTE]
   > Die Schlüsselbundverwaltung wählt standardmäßig das erste Element in der Liste aus. Dies kann ein Problem darstellen, wenn Sie sich in der Kategorie **Zertifikate** befinden und **Zertifizierungsstelle der Apple Worldwide Developer Relations** nicht das erste Element in der Liste ist. Stellen Sie sicher, dass Sie über ein Element verfügen, das kein Schlüssel ist, bzw. dass der Schlüssel **Zertifizierungsstelle der Apple Worldwide Developer Relations** ausgewählt ist, bevor Sie die Zertifikatsignieranforderung (Certificate Signing Request, CSR) generieren.

1. Wählen Sie Ihre **E-Mail-Adresse des Benutzers** aus, und geben Sie den Wert für **Allgemeiner Name** ein. Vergewissern Sie sich, dass **Saved to disk** (Auf Datenträger gespeichert) aktiviert ist, und wählen Sie dann **Weiter** aus. Lassen Sie das Feld **CA Email Address** (E-Mail der Zertifizierungsstelle) leer, da dieser Wert nicht erforderlich ist.

    ![Erwartete Zertifikatinformationen](../media/notification-hubs-certificate-signing-request-info.png)

1. Geben Sie unter **Sichern unter** einen Namen für die **CSR-Datei (Zertifikatsignieranforderung)** ein, wählen Sie unter **Ort** den Speicherort und dann **Speichern** aus.

    ![Auswählen eines Dateinamens für das Zertifikat](../media/notification-hubs-save-certificate-signing-request.png)

    Dadurch wird die **CSR-Datei** am ausgewählten Speicherort gespeichert. Der Standardspeicherort lautet **Desktop**. Merken Sie sich den für diese Datei festgelegten Speicherort.

1. Zurück auf der Seite **Zertifikate, Bezeichner und Profile** scrollen Sie im [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456) nach unten bis zur markierten Option **Pushbenachrichtigungen**, und wählen Sie dann **Konfigurieren** aus, um das Zertifikat zu erstellen.

    ![Bearbeiten der Seite für App-IDs](../media/notification-hubs-edit-appid.png)

1. Das Fenster für die **TLS/SSL-Zertifikate von Apple Push Notification Service** wird angezeigt. Wählen Sie im Abschnitt des **TLS/SSL-Zertifikats für die Entwicklung** die Schaltfläche **Zertifikat erstellen** aus.

    ![Schaltfläche zum Erstellen eines Zertifikats für die App-ID](../media/notification-hubs-appid-create-certificate.png)

    Der Bildschirm **Ein neues Zertifikat erstellen** wird angezeigt.

    > [!NOTE]
    > In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

1. Klicken Sie auf **Datei auswählen**, wechseln Sie zum Speicherort der **CSR-Datei**, und doppelklicken Sie dann auf den Namen des Zertifikats, um es zu laden. Klicken Sie anschließend auf **Weiter**.

1. Wählen Sie nach der Erstellung des Zertifikats im Portal die Schaltfläche **Herunterladen** aus. Speichern Sie das Zertifikat, und merken Sie sich den Speicherort.

    ![Downloadseite für das generierte Zertifikat](../media/notification-hubs-appid-download-certificate.png)

    Das Zertifikat wird heruntergeladen und auf Ihrem Computer im Ordner **Downloads** gespeichert.

    ![Suchen der Zertifikatdatei im Ordner „Downloads“](../media/notification-hubs-certificate-downloaded.png)

    > [!NOTE]
    > Standardmäßig heißt das heruntergeladene Entwicklungszertifikat **aps_development.cer**.

1. Doppelklicken Sie auf dem heruntergeladenen Pushzertifikat **aps_development.cer**. Das neue Zertifikat wird im Schlüsselbund installiert, wie in der folgenden Abbildung gezeigt:

    ![Zertifikatliste der Schlüsselbundverwaltung mit neuem Zertifikat](../media/notification-hubs-certificate-in-keychain.png)

    > [!NOTE]
    > Der Name in Ihrem Zertifikat kann anders lauten, ihm wird jedoch **Apple Development iOS Push Services** vorangestellt und der passende Bundlebezeichner zugeordnet.

1. **CONTROL** + **Klicken** Sie in der Schlüsselbundverwaltung auf das neue Pushzertifikat, das Sie in der Kategorie **Zertifikate** erstellt haben. Wählen Sie die Option **Exportieren**, benennen Sie die Datei, wählen Sie das Format **P12** aus, und wählen Sie dann die Option **Speichern**.

    ![Exportieren des Zertifikats im p12-Format](../media/notification-hubs-export-certificate-p12.png)

    Sie können das Zertifikat mit einem Kennwort schützen, aber ein Kennwort ist optional. Wenn Sie die Kennworterstellung umgehen möchten, klicken Sie auf **OK**. Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats, Sie werden zum Aktivieren der Authentifizierung mit APNS verwendet.

    > [!NOTE]
    > Dateiname und Speicherort Ihrer P12-Datei können sich von den in diesem Tutorial abgebildeten Informationen unterscheiden.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPTION 2: Erstellen eines P8-Zertifikats, das für die tokenbasierte Authentifizierung verwendet werden kann

1. Notieren Sie sich folgende Angaben:

    * **App-ID-Präfix** (**Team-ID**)
    * **Bundle-ID**

1. Klicken Sie in **Zertifikate, Bezeichner und Profile** auf **Schlüssel**.

   > [!NOTE]
   > Wenn Sie bereits einen Schlüssel für **APNS** konfiguriert haben, können Sie das P8-Zertifikat wiederverwenden, das Sie direkt nach der Erstellung heruntergeladen haben. In diesem Fall können Sie die Schritte **3** bis **5** überspringen.

1. Klicken Sie auf die Schaltfläche **+** (oder die Schaltfläche **Schlüssel erstellen**), um einen neuen Schlüssel zu erstellen.
1. Geben Sie einen geeigneten Wert für den **Schlüsselnamen** an, aktivieren Sie die Option **Apple Push Notification Service (APNS)** , und klicken Sie dann auf **Weiter** und auf dem nächsten Bildschirm auf **Registrieren**.
1. Klicken Sie auf **Herunterladen**, und verschieben Sie die **P8**-Datei (mit dem Präfix *AuthKey_* ) in ein sicheres lokales Verzeichnis. Klicken Sie dann auf **Fertig**.

   > [!NOTE]
   > Bewahren Sie die P8-Datei an einem sicheren Ort auf (und speichern Sie eine Sicherungskopie). Nach dem Herunterladen kann der Schlüssel nicht erneut heruntergeladen werden, weil die Kopie auf dem Server gelöscht wird.
  
1. Klicken Sie unter **Schlüssel** auf den erstellten Schlüssel (oder auf einen vorhandenen Schlüssel, falls Sie stattdessen einen solchen verwenden möchten).
1. Notieren Sie sich den Wert der **Schlüssel-ID**.
1. Öffnen Sie das P8-Zertifikat in einer geeigneten Anwendung wie z. B. [**Visual Studio Code**](https://code.visualstudio.com). Notieren Sie sich den Schlüsselwert (zwischen **-----BEGIN PRIVATE KEY-----** und **-----END PRIVATE KEY-----** ).

    -----BEGIN PRIVATE KEY-----  
    <Schlüsselwert>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > Dies ist der **Tokenwert**, der später verwendet wird, um den **Notification Hub** zu konfigurieren.

Nach Abschluss dieser Schritte verfügen Sie über folgende Informationen, die Sie später unter [Konfigurieren Ihres Benachrichtigungshubs mit APNS-Informationen](#configure-your-notification-hub-with-apns-information) verwenden werden:

* **Team-ID** (siehe Schritt 1)
* **Bundle-ID** (siehe Schritt 1)
* **Schlüssel-ID** (siehe Schritt 7)
* **Tokenwert** (P8-Schlüsselwert, der in Schritt 8 abgerufen wurde)

### <a name="create-a-provisioning-profile-for-the-app"></a>Erstellen eines Bereitstellungsprofils für die App

1. Wechseln Sie zurück zum [iOS-Bereitstellungsportal](https://go.microsoft.com/fwlink/p/?LinkId=272456), wählen Sie **Zertifikate, Bezeichner und Profile** aus, wählen Sie im Menü auf der linken Seite **Profile** und dann **+** aus, um ein neues Profil zu erstellen. Der Bildschirm zum **Registrieren eines neuen Bereitstellungsprofils** wird angezeigt.

1. Wählen Sie unter **Entwicklung**, die Option **iOS-App-Entwicklung** als Bereitstellungsprofiltyp aus, und wählen Sie anschließend **Weiter**.

    ![Bereitstellungsprofilliste](../media/notification-hubs-new-provisioning-profile.png)

1. Wählen Sie anschließend die von Ihnen erstellte App-ID in der Dropdownliste **App ID** (App-ID) und dann **Weiter** aus.

    ![Auswählen der App-ID](../media/notification-hubs-select-appid-for-provisioning.png)

1. Wählen Sie im Fenster **Select certificates** (Zertifikate auswählen) das Entwicklungszertifikat aus, das Sie für die Codesignierung verwenden, und wählen Sie dann **Continue** (Weiter).

    > [!NOTE]
    > Bei diesem Zertifikat handelt es sich nicht um das im [vorherigen Schritt](#creating-a-certificate-for-notification-hubs) erstellte Pushzertifikat, sondern um Ihr Entwicklungszertifikat. Wenn kein solches vorhanden ist, müssen Sie es erstellen, da dies eine [Voraussetzung](#prerequisites) für dieses Tutorial ist. Entwicklerzertifikate können im [Apple-Entwicklerportal](https://developer.apple.com) über [Xcode-](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) oder in [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) erstellt werden.

1. Wechseln Sie zurück zur Seite **Zertifikate, Bezeichner und Profile**, wählen Sie im Menü auf der linken Seite **Profile** und dann **+** aus, um ein neues Profil zu erstellen. Der Bildschirm zum **Registrieren eines neuen Bereitstellungsprofils** wird angezeigt.

1. Wählen Sie im Fenster **Zertifikate auswählen** das erstellte Entwicklungszertifikat aus. Klicken Sie anschließend auf **Weiter**.

1. Wählen Sie nun die Geräte aus, die zum Testen verwendet werden sollen, und wählen Sie dann **Weiter** aus.

1. Wählen Sie schließlich im Feld **Provisioning Profile Name** (Name des Bereitstellungsprofils) einen Namen für das Profil, und wählen Sie **Generate** (Generieren) aus.

    ![Auswählen eines Bereitstellungsprofilnamens](../media/notification-hubs-provisioning-name-profile.png)

1. Nachdem das neue Bereitstellungsprofil erstellt wurde, wählen Sie **Herunterladen** aus. Merken Sie sich den Speicherort des Profils.

1. Navigieren Sie zum Speicherort des Bereitstellungsprofils, und doppelklicken Sie darauf, um es auf dem Entwicklungscomputer zu installieren.
