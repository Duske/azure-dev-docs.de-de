---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 1f994464e6d0cb1c90bc1078eee5061cb91e6778
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401456"
---
### <a name="validate-package-name-and-permissions"></a>Überprüfen von Paketname und Berechtigungen

1. Öffnen Sie in **PushDemo.Android** die **Projektoptionen** und dann **Android-Anwendung** aus dem Abschnitt **Build**.

1. Überprüfen Sie, ob der **Paketname** mit dem Wert übereinstimmt, den Sie im **PushDemo**-Projekt der [Firebase-Konsole](https://console.firebase.google.com) verwendet haben. Der **Paketname** wies das Format ``com.<organization>.pushdemo`` auf.

1. Legen Sie die **Minimale Android-Version** auf **Android 8.0 (API-Ebene 26)** und die **Android-Zielversion** auf die neueste **API-Ebene** fest.

    > [!NOTE]
    > Für die Zwecke dieses Tutorials werden nur Geräte mit **API-Ebene 26 und höher** unterstützt. Sie können diese Einstellung jedoch erweitern, um Geräte mit älteren Versionen zu unterstützen.

1. Stellen Sie sicher, dass die Berechtigungen **INTERNET** und **READ_PHONE_STATE** unter  **Erforderliche Berechtigungen** aktiviert sind.

1. Klicken Sie auf **OK**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Hinzufügen der Pakete „Xamarin Google Play Services Base“ und „Xamarin.Firebase.Messaging“

1. **CONTROL** + **Klicken** Sie in **PushDemo.Android** auf den Ordner **Pakete**, und wählen Sie dann **NuGet-Pakete verwalten...** aus.

1. Suchen Sie nach **Xamarin.GooglePlayServices.Base** (nicht **Basement**), und stellen Sie sicher, dass es aktiviert ist.

1. Suchen Sie nach **Xamarin.Firebase.Messaging**, und stellen Sie sicher, dass es aktiviert ist.

1. Klicken Sie auf **Pakete hinzufügen** dann auf **Zustimmen**, wenn Sie aufgefordert werden, die **Lizenzbedingungen** zu akzeptieren.

### <a name="add-the-google-services-json-file"></a>Hinzufügen der JSON-Datei von Google Services

1. **CONTROL** + **Klicken** Sie auf das Projekt `PushDemo.Android`, und wählen Sie dann **Vorhandene Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie die Datei *google-services.json* aus, die Sie zuvor beim Einrichten des **PushDemo**-Projekts in der [Firebase-Konsole](https://console.firebase.google.com) heruntergeladen haben, und klicken Sie dann auf **Öffnen**.

1. Wenn Sie dazu aufgefordert werden, **kopieren Sie die Datei in das Verzeichnis**.

1. **CONTROL** + **Klicken** Sie aus dem `PushDemo.Android`-Projekt heraus auf die Datei *google-services.json*, und stellen Sie dann sicher, dass **GoogleServicesJson** als **Build-Aktion** festgelegt ist.

### <a name="handle-push-notifications-for-android"></a>Behandeln von Pushbenachrichtigungen für Android

1. **CONTROL** + **Klicken** Sie auf das Projekt `PushDemo.Android`, wählen Sie **Neuer Ordner** aus dem Menü **Hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. Verwenden Sie dabei *Dienste* als **Ordnernamen**.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Dienste**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **Allgemein** > **Leere Klassen** aus, geben Sie *DeviceInstallationService.cs* als **Namen** ein, und klicken Sie dann auf **Neu** unter Hinzufügung der folgenden Implementierung.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for FCM");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse bietet eine eindeutige ID (unter Verwendung von [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) als Teil der Nutzlast der Benachrichtigungshubregistrierung.

1. Fügen Sie dem Ordner **Dienste** mit dem Namen *PushNotificationFirebaseMessagingService.cs* eine weitere **leere Klasse** hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
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

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. Stellen Sie in **MainActivity.cs** sicher, dass am Anfang der Datei die folgenden Namespaces hinzugefügt wurden.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. Legen Sie in **MainActivity.cs** das **LaunchMode**-Element auf **SingleTop** fest, damit **MainActivity** beim Öffnen nicht erneut erstellt wird.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Fügen Sie private Eigenschaften und entsprechende Unterstützungsfelder hinzu, um einen Verweis auf die **IPushNotificationActionService**- und **IDeviceInstallationService**-Implementierungen zu speichern.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implementieren Sie die **IOnSuccessListener**-Schnittstelle zum Abrufen und Speichern des **Firebase**-Tokens.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Fügen Sie eine neue Methode namens **ProcessNotificationActions** hinzu, die prüft, ob eine gegebene **Absicht** einen zusätzlichen Wert namens *Aktion* ausweist. Führen Sie eine bedingte Auslösung dieser Aktion unter Verwendung der Implementierung **IPushDemoNotificationActionService** aus.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Überschreiben Sie die Methode **OnNewIntent**, um die Methode **ProcessNotificationActions** aufzurufen.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Da das **LaunchMode**-Element für die **Aktivität** auf **SingleTop** festgelegt ist, wird eine **Absicht** an die bestehende **Activity**-Instanz über die **OnNewIntent**-Methode statt über die **OnCreate**-Methode gesendet, daher müssen Sie eine eingehende Absicht sowohl in der **OnCreate**- als auch in der **OnNewIntent**-Methode behandeln.

1. Aktualisieren Sie die **OnCreate**-Methode, um `Bootstrap.Begin` direkt nach dem Aufruf von `base.OnCreate` aufzurufen, wobei Sie die plattformspezifische Implementierung von **IDeviceInstallationService** übergeben.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. In derselben Methode erfolgt ein bedingter Aufruf von **GetInstanceId-** in der **FirebaseApp**-Instanz unmittelbar nach dem Aufrufen von `Bootstrap.Begin`, wobei **MainActivity** als **IOnSuccessListener** hinzugefügt wird.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Rufen Sie, immer noch in **OnCreate**, die **ProcessNotificationActions**-Methode unmittelbar nach dem Aufruf von `LoadApplication` auf, wobei Sie die aktuelle **Absicht** übergeben.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Sie müssen die App jedes Mal, wenn Sie sie von einer Debugsitzung aus ausführen und beenden, neu registrieren, um weiterhin Pushbenachrichtigungen zu erhalten.
