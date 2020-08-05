---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9336229d7dfcf65277b9bd0f9a0a01e444609d5e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401469"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Konfigurieren des Runner-Ziels und der Datei „Info.plist“

1. **STRG** + **Klicken** Sie in **Visual Studio Code** auf den Ordner **ios**, und wählen Sie dann **In Xcode öffnen** aus.

1. Klicken Sie in **Xcode** auf **Runner** (die **xcodeproj**-Datei oben, nicht auf den Ordner), wählen Sie dann das **Runner**-Ziel und **Signieren und Funktionen** aus. Wählen Sie Ihr Entwicklerkonto für das **Team** bei ausgewählter Buildkonfiguration **Alle** aus. Vergewissern Sie sich, dass das Kontrollkästchen für automatische Signierungsverwaltung aktiviert ist und dass Ihr Signaturzertifikat und Ihr Bereitstellungsprofil automatisch ausgewählt werden.

    > [!NOTE]
    > Wenn der neue Wert für das Bereitstellungsprofil nicht angezeigt wird, aktualisieren Sie die Profile für die Signierungsidentität, indem Sie nacheinander auf **Xcode** > **Einstellungen** > **Konto** klicken und dann die Schaltfläche **Manuelle Profile herunterladen** auswählen, um die Profile herunterzuladen.

1. Klicken Sie auf **+ Funktion**, und suchen Sie dann nach **Pushbenachrichtigungen**. **Doppelklicken** Sie auf **Pushbenachrichtigungen**, um diese Funktion hinzuzufügen.

1. Öffnen Sie **Info.plist**, und legen Sie **Minimale Systemversion** auf **13.0** fest.

    > [!NOTE]
    > Für die Zwecke dieses Tutorials werden nur Geräte mit **iOS 13.0 und höher** unterstützt. Sie können diese Einstellung jedoch erweitern, um Geräte mit älteren Versionen zu unterstützen.

1. Öffnen Sie **Runner.entitlements**, und vergewissern Sie sich, dass die Einstellung **APS-Umgebung** auf **Entwicklung** festgelegt ist.

### <a name="handle-push-notifications-for-ios"></a>Behandeln von Pushbenachrichtigungen für iOS

1. **STRG** + **Klicken** Sie auf den Ordner **Runner** (innerhalb des Runner-Projekts), und wählen Sie dann **Neue Gruppe** mithilfe von **Dienste** als Name aus.

1. **STRG** + **Klicken** Sie auf den Ordner **Services**, und wählen Sie dann **Neue Datei...** aus. Wählen Sie anschließend **Swift-Datei** aus, und klicken Sie dann auf **Weiter**. Geben Sie **DeviceInstallationService** als Namen an, und klicken Sie dann auf **Erstellen**.

1. Implementieren Sie **DeviceInstallationService.swift** mit dem folgenden Code.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/deviceinstallation`-Kanal. Dies wurde in **DeviceInstallationService.dart** im Flutter-Teil der App definiert. In diesem Fall erfolgen die Aufrufe vom allgemeinen Code zum nativen Host. Stellen Sie sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.
    >
    > Diese Klasse bietet eine eindeutige ID (unter Verwendung des Werts [UIDevice.IdentifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor)) als Teil der Nutzlast der Benachrichtigungshubregistrierung.

1. Fügen Sie dem Ordner **Services** eine weitere **Swift-Datei** namens *NotificationRegistrationService* hinzu, und fügen Sie dann den folgenden Code hinzu.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/notificationregistration`-Kanal. Dies wurde in **NotificationRegistrationService.dart** im Flutter-Teil der App definiert. In diesem Fall erfolgen die Aufrufe vom nativen Host zum allgemeinen Code. Stellen Sie auch hier sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.

1. Fügen Sie dem Ordner **Services** eine weitere **Swift-Datei** namens *NotificationActionService* hinzu, und fügen Sie dann den folgenden Code hinzu.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/notificationaction`-Kanal. Dies wurde in **NotificationActionService.dart** im Flutter-Teil der App definiert. In diesem Fall können Aufrufe in beide Richtungen erfolgen. Stellen Sie sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.

1. Fügen Sie in **AppDelegate.swift** Variablen zum Speichern eines Verweises auf die Dienste hinzu, die Sie zuvor erstellt haben.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Fügen Sie eine Funktion namens **processNotificationActions** hinzu, um die Benachrichtigungsdaten zu verarbeiten. Lösen Sie diese Aktion bedingt aus, oder speichern Sie sie zur späteren Verwendung, wenn die Aktion während des App-Starts verarbeitet wird.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Überschreiben Sie die **didRegisterForRemoteNotificationsWithDeviceToken**-Funktion, indem Sie den **token**-Wert für den **DeviceInstallationService** festlegen. Rufen Sie dann **refreshRegistration** für **NotificationRegistrationService** auf.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Überschreiben Sie die **didReceiveRemoteNotification**-Funktion, wobei Sie das **userInfo**-Argument an die **ProcessNotificationActions**-Funktion übergeben.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Überschreiben Sie die **didFailToRegisterForRemoteNotificationsWithError**-Funktion, um den Fehler zu protokollieren.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Dies dient als Platzhalter. Für Produktionsszenarien werden Sie eine ordnungsgemäße Protokollierung und Fehlerbehandlung implementieren wollen.

1. Instanziieren Sie in **didFinishLaunchingWithOptions** die Variablen **deviceInstallationService**, **notificationRegistrationService** und **notificationActionService**.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Fordern Sie in der gleichen Funktion bedingt Autorisierung an, und registrieren Sie sich für Remotebenachrichtigungen.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Wenn **launchOptions** den Schlüssel **remoteNotification** enthält, rufen Sie **processNotificationActions** am Ende der **didFinishLaunchingWithOptions**-Funktion auf. Übergeben Sie das sich ergebende **userInfo**-Objekt, und verwenden Sie *true* für das *launchAction*-Argument. Der Wert *true* Wert gibt an, dass die Aktion beim Start der App verarbeitet wird.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
