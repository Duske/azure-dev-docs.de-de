---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 588a59014e01c7fc43c0c1573589be9a8776dd2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401464"
---
### <a name="create-the-xamarinforms-solution"></a>Erstellen der Xamarin.Forms-Lösung

1. Erstellen Sie in **Visual Studio** eine neue **Xamarin.Forms**-Lösung, wobei Sie die **Leere Forms-App** als Vorlage verwenden und *PushDemo* als **Projektnamen** eingeben.

    > [!NOTE]
    > Vergewissern Sie sich im Dialogfeld **Leere Forms-App konfigurieren**, dass der **Organisationsbezeichner** mit dem zuvor verwendeten Wert übereinstimmt und dass sowohl **Android** als auch **iOS** als Ziele aktiviert sind.

1. **CONTROL** + **Klicken** Sie auf die *PushDemo*-Lösung, und wählen Sie dann **NuGet-Pakete aktualisieren** aus.
1. **CONTROL** + **Klicken** Sie auf die *PushDemo*-Lösung, und wählen Sie dann **NuGet-Pakete verwalten** aus.
1. Suchen Sie nach **Newtonsoft.Json**, und stellen Sie sicher, dass es aktiviert ist.
1. Klicken Sie auf **Pakete hinzufügen** und dann auf **Zustimmen**, wenn Sie aufgefordert werden, die Lizenzbedingungen zu akzeptieren.
1. Erstellen Sie die App, und führen Sie sie auf jeder Zielplattform aus (**BEFEHL** + **EINGABE**), um zu testen, ob die auf der Vorlage basierende App auf Ihren Geräten ausgeführt wird.

### <a name="implement-the-cross-platform-components"></a>Implementieren der plattformübergreifenden Komponenten

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemo**, wählen Sie **Neuer Ordner** aus dem Menü **Hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. Verwenden Sie dabei *Modelle* als **Ordnernamen**.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Modelle**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **Allgemein** > **Leere Klasse** aus, geben Sie *DeviceInstallation.cs* ein, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Fügen Sie eine **leere Aufzählung** namens *PushDemoAction.cs* mit der folgenden Implementierung zum Ordner **Modelle** hinzu.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Fügen Sie einen neuen Ordner mit dem Namen *Dienste* zum Projekt **PushDemo** hinzu, und fügen Sie diesem Ordner dann eine **leere Klasse** namens *ServiceContainer.cs* mit der folgenden Implementierung hinzu.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Dies ist eine abgespeckte Version der Klasse [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) aus dem [XamCAT](https://github.com/xamcat/mobcat-library)-Repository. Sie wird als ein Lightweight-IoC-Container (Inversion of Control) verwendet.

1. Fügen Sie zum Ordner **Dienste** eine **leere Schnittstelle** namens *IDeviceInstallationService.cs* hinzu, und fügen Sie dann den folgenden Code hinzu.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Diese Schnittstelle wird später von jedem Ziel implementiert und über Bootstrapping geladen, um die plattformspezifische Funktionalität und die erforderlichen **DeviceInstallation**-Informationen bereitzustellen, die vom Back-End-Dienst benötigt werden.

1. Fügen Sie zum Ordner **Dienste** eine weitere **leere Schnittstelle** namens *INotificationRegistrationService.cs* hinzu, und fügen Sie dann den folgenden Code hinzu.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Dadurch wird die Interaktion zwischen dem Client und dem Back-End-Dienst behandelt.

1. Fügen Sie zum Ordner **Dienste** eine weitere **leere Schnittstelle** namens *INotificationActionService.cs* hinzu, und fügen Sie dann den folgenden Code hinzu.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Dieser einfache Mechanismus dient dazu, die Behandlung von Benachrichtigungsaktionen zu zentralisieren.

1. Fügen Sie dem Ordner **Dienste** eine **leere Schnittstelle** namens *IPushDemoNotificationActionService.cs* hinzu, die von *INotificationActionService* abgeleitet ist, mit der folgenden Implementierung.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Dieser Typ ist für die Anwendung **PushDemo** spezifisch und verwendet die **PushDemoAction**-Enumeration, um die Aktion zu identifizieren, die in stark typisierter Weise ausgelöst wird.

1. Fügen Sie zum Ordner **Dienste** eine **leere Klasse** namens *NotificationRegistrationService.cs* hinzu, und implementieren Sie *INotificationRegistrationService* mit dem folgenden Code.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Das Argument **apiKey** ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.

1. Fügen Sie zum Ordner **Dienste** eine **leere Klasse** namens *PushDemoNotificationActionService.cs* hinzu, und implementieren Sie *IPushDemoNotificationActionService* mit dem folgenden Code.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Fügen Sie zum Projekt **PushDemo** eine **leere Klasse** namens *Config.cs* mit der folgenden Implementierung hinzu.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Diese einfache Methode dient dazu, geheime Schlüssen aus der Quellcodeverwaltung herauszuhalten. Sie können diese Werte als Teil eines automatisierten Builds ersetzen oder durch eine lokale Teilklasse überschreiben. Dieses Verfahren wird im nächsten Schritt behandelt.
    >
    > Das Feld **ApiKey** ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.

1. Fügen Sie zum Projekt **PushDemo** eine weitere **leere Klasse**, dieses Mal mit dem Namen *Config.local_secrets.cs* und der folgenden Implementierung hinzu.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Ersetzen Sie die Platzhalterwerte durch eigene Werte. Sie sollten sich diese bei der Erstellung des Back-End-Diensts notiert haben. Die **API-App**-URL sollte ``https://<api_app_name>.azurewebsites.net/`` lauten. Denken Sie daran, ihrer gitignore-Datei ``*.local_secrets.*`` hinzuzufügen, um das Committen dieser Datei zu vermeiden.
    >
    > Das Feld **ApiKey** ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.

1. Fügen Sie zum Projekt **PushDemo** eine **leere Klasse** namens *Bootstrap.cs* mit der folgenden Implementierung hinzu.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > Die Methode **Begin** wird von jeder Plattform aufgerufen, wenn die App unter Übergabe einer plattformspezifischen Implementierung von **IDeviceInstallationService** gestartet wird.
    >
    > Das **NotificationRegistrationService** **apiKey**-Konstruktorargument ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.

### <a name="implement-the-cross-platform-ui"></a>Implementieren der plattformübergreifenden Benutzeroberfläche

1. Öffnen Sie im Projekt **PushDemo** die Datei **MainPage.xaml**, und ersetzen Sie das Steuerelement **StackLayout** durch Folgendes.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Fügen Sie nun in **MainPage.xaml.cs** ein **readonly**-Unterstützungsfeld zum Speichern eines Verweises auf die Implementierung **INotificationRegistrationService** hinzu.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. Lösen Sie im **MainPage**-Konstruktor die **INotificationRegistrationService**-Implementierung unter Verwendung von **ServiceContainer** auf, und weisen Sie sie dem *_notificationRegistrationService_*-Unterstützungsfeld zu.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementieren Sie die Ereignishandler für die **Clicked**-Ereignisse der Schaltflächen **RegisterButton** und **DeregisterButton**, die die entsprechenden **Register**/**Deregister**-Methoden aufrufen.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Stellen Sie nun sicher, dass in **App.xaml.cs** auf die folgenden Namespaces verwiesen wird.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementieren Sie den Ereignishandler für das **ActionTriggered**-Ereignis von **IPushDemoNotificationActionService**.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Lösen Sie im **App**-Konstruktor die **IPushNotificationActionService**-Implementierung mithilfe von **ServiceContainer** auf, und abonnieren Sie das **ActionTriggered**-Ereignis von **IPushDemoNotificationActionService**.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Dies soll lediglich den Empfang und die Weitergabe von Pushbenachrichtigungsaktionen veranschaulichen. Diese werden in der Regel im Hintergrund behandelt, z. B. wenn Sie zu einer bestimmten Ansicht navigieren oder einige Daten aktualisieren, anstatt eine Warnung über die **Stammseite** (in diesem Fall **MainPage**) anzuzeigen.
