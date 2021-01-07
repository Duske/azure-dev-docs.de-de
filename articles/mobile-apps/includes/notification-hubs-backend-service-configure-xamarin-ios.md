---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 4dba0dcf6fe960b80e445f865644997669ce9b36
ms.sourcegitcommit: 84f64dec74b4b041b8830a4e7489e22f0e943440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97866137"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Konfigurieren von „Info.plist“ und „Entitlements.plist“

1. Stellen Sie sicher, dass Sie sich bei Ihrem **Apple-Entwicklerkonto** unter **Visual Studio** > **Einstellungen...**  > **Veröffentlichen** > **Apple-Entwicklerkonten** angemeldet haben, und dass das entsprechende *Zertifikat* und *Bereitstellungsprofil* heruntergeladen wurden. Sie sollten diese Ressourcen im Rahmen der vorherigen Schritte erstellt haben.

1. Öffnen Sie in **PushDemo.iOS** die Datei **Info.plist**, und stellen Sie sicher, dass **BundleIdentifier** mit dem Wert übereinstimmt, der für das jeweilige Bereitstellungsprofil im [Apple-Entwicklerportal](https://developer.apple.com) verwendet wurde. **BundleIdentifier** hatte das Format ``com.<organization>.PushDemo``.

1. Legen Sie in derselben Datei **Minimale Systemversion** auf **13.0** fest.

    > [!NOTE]
    > Für die Zwecke dieses Tutorials werden nur Geräte mit **iOS 13.0 und höher** unterstützt. Sie können diese Einstellung jedoch erweitern, um Geräte mit älteren Versionen zu unterstützen.

1. Öffnen Sie die **Projektoptionen** für **PushDemo.iOS** (durch Doppelklicken auf das Projekt).

1. Vergewissern Sie sich in **Projektoptionen** unter **Build > iOS-Bundlesignierung**, dass unter **Team** Ihr Entwicklerkonto ausgewählt ist. Vergewissern Sie sich dann, dass das Kontrollkästchen für die automatische Signierungsverwaltung aktiviert ist und dass Ihr Signaturzertifikat und Ihr Bereitstellungsprofil automatisch ausgewählt wurden.

    > [!NOTE]
    > Wenn Ihr *Signaturzertifikat* und *Bereitstellungsprofil* nicht automatisch ausgewählt wurden, wählen Sie **Manuelle Bereitstellung** aus, und klicken Sie dann auf **signierungsoptionen**. Stellen Sie sicher, dass Ihr *Team* als **Signierungsidentität** und Ihr *PushDemo*-spezifisches Bereitstellungsprofil als **Bereitstellungsprofil** sowohl für **Debug**- als auch für **Freigabe**-Konfigurationen ausgewählt ist, und dass **iPhone** in beiden Fällen als **Plattform** ausgewählt ist.

1. Öffnen Sie in **PushDemo.iOS** die Datei **Entitlements.plist**, und stellen Sie sicher, dass **Push-Benachrichtigungen aktivieren** bei der Ansicht auf der Registerkarte **Berechtigungen** aktiviert ist. Vergewissern Sie sich anschließend, dass die Einstellung **APS-Umgebung** bei der Ansicht auf der Registerkarte **Quelle** auf **Entwicklung** festgelegt ist.

### <a name="handle-push-notifications-for-ios"></a>Behandeln von Pushbenachrichtigungen für iOS

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemo.iOS**, wählen Sie **Neuer Ordner** aus dem Menü **Hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. Verwenden Sie dabei *Dienste* als **Ordnernamen**.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Dienste**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **Allgemein** > **Leere Klassen** aus, geben Sie *DeviceInstallationService.cs* als **Namen** ein, und klicken Sie dann auf **Neu** unter Hinzufügung der folgenden Implementierung.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.IsNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse bietet eine eindeutige ID (unter Verwendung des Werts [UIDevice.IdentifierForVendor](/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12)) und die Nutzlast der Benachrichtigungshubregistrierung.

1. Fügen Sie einen neuen Ordner mit dem Namen *Erweiterungen* zum Projekt **PushDemo.iOS** hinzu, und fügen Sie diesem Ordner dann eine **leere Klasse** namens *NSDataExtensions.cs* mit der folgenden Implementierung hinzu.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. Stellen Sie in **AppDelegate.cs** sicher, dass am Anfang der Datei die folgenden Namespaces hinzugefügt wurden.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Fügen Sie private Eigenschaften und die entsprechenden Unterstützungsfelder hinzu, um einen Verweis auf die **IPushDemoNotificationActionService**-, **INotificationRegistrationService**- und **IDeviceInstallationService**-Implementierungen zu speichern.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    INotificationRegistrationService _notificationRegistrationService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    INotificationRegistrationService NotificationRegistrationService
        => _notificationRegistrationService ??
            (_notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Fügen Sie die **RegisterForRemoteNotifications**-Methode hinzu, um Benutzerbenachrichtigungseinstellungen und dann Remotebenachrichtigungen mit **APNS** zu registrieren.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Fügen Sie die **CompleteRegistrationAsync**-Methode hinzu, um den `IDeviceInstallationService.Token`-Eigenschaftswert festzulegen. Aktualisieren Sie die Registrierung, und speichern Sie das Gerätetoken zwischen, wenn es seit der letzten Speicherung aktualisiert wurde.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Fügen Sie die **ProcessNotificationActions**-Methode zur Verarbeitung der **NSDictionary**-Benachrichtigungsdaten und zum bedingten Aufruf von **NotificationActionService.TriggerAction** hinzu.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Überschreiben Sie die **RegisteredForRemoteNotifications**-Methode, wobei Sie das **deviceToken**-Argument an die **CompleteRegistrationAsync**-Methode übergeben.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Überschreiben Sie die **ReceivedRemoteNotification**-Methode, wobei Sie das **userInfo**-Argument an die **ProcessNotificationActions**-Methode übergeben.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Überschreiben Sie die **FailedToRegisterForRemoteNotifications**-Methode, um den Fehler zu protokollieren.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Dies dient als Platzhalter. Für Produktionsszenarien werden Sie eine ordnungsgemäße Protokollierung und Fehlerbehandlung implementieren wollen.

1. Aktualisieren Sie die **FinishedLaunching**-Methode, um `Bootstrap.Begin` direkt nach dem Aufruf von `Forms.Init` aufzurufen, wobei Sie die plattformspezifische Implementierung von **IDeviceInstallationService** übergeben.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Fordern Sie in der gleichen Methode eine bedingte Autorisierung an, und registrieren Sie sich unmittelbar nach `Bootstrap.Begin` für Remotebenachrichtigungen.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Rufen Sie, immer noch in der **FinishedLaunching**-Methode, die **ProcessNotificationActions**-Methode unmittelbar nach dem Aufruf von `LoadApplication` auf, wenn das Argument **options** das **UIApplication.LaunchOptionsRemoteNotificationKey**-Element enthält, wobei Sie das resultierende **userInfo**-Objekt übergeben.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
