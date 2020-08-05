---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 9d7db7db5a1b7323bd10e7e9cc87ca3b9a95826a
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401482"
---
### <a name="create-a-web-project"></a>Erstellen eines Webprojekts

1. Wählen Sie in **Visual Studio** die Optionen **Datei** > **Neue Lösung** aus.

1. Wählen Sie **.NET Core** > **App** > **ASP.NET Core** > **API** > **Weiter** aus.
  
1. Wählen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Option **Zielframework** von **.NET Core 3.1** aus.

1. Geben Sie *PushDemoApi* als **Projektnamen** ein, und wählen Sie dann **Erstellen** aus.

1. Starten Sie den Debugvorgang (**BEFEHL** + **EINGABE**), um die vorlagenbasierte App zu testen.

    > [!NOTE]
    > Die vorlagenbasierte App ist so konfiguriert, dass **WeatherForecastController-** als *launchUrl* verwendet wird. Dies wird unter **Eigenschaften** > **launchSettings.json** festgelegt.  
    >
    > Wenn die Meldung **Ungültiges Entwicklungszertifikat gefunden** angezeigt wird:
    >
    > 1. Klicken Sie auf **Ja**, um zu bestätigen, dass das Tool „dotnet dev-certs https“ zum Korrigieren dieses Fehlers ausgeführt wird. Vom Tool „dotnet dev-certs https“ werden Sie dann aufgefordert, ein Kennwort für das Zertifikat und das Kennwort für Ihren Schlüsselbund einzugeben.
    >
    > 1. Klicken Sie auf **Ja**, wenn Sie aufgefordert werden, **das neue Zertifikat zu installieren und ihm zu vertrauen**, und geben Sie dann das Kennwort für Ihren Schlüsselbund ein.

1. Erweitern Sie den Ordner **Controller**, und löschen Sie dann **WeatherForecastController.cs**.

1. Löschen Sie **WeatherForecast.cs**.

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemoApi**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Richten Sie mit dem [Geheimnis-Manager-Tool](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager) lokale Konfigurationswerte ein. Indem Sie die Kopplung der geheimen Schlüssel mit der Lösung aufheben, wird sichergestellt, dass sie nicht in die Quellcodeverwaltung geraten. Öffnen Sie **Terminal**, wechseln Sie dann zum Verzeichnis der Projektdatei, und führen Sie die folgenden Befehle aus:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Ersetzen Sie die Platzhalterwerte durch den Namen ihres eigenen Benachrichtigungshubs und die Werte der Verbindungszeichenfolge. Diese haben Sie sich im Abschnitt [Erstellen eines Benachrichtigungshubs](#create-a-notification-hub) notiert. Andernfalls können Sie sie in [Azure](https://portal.azure.com) nachschlagen.

    **NotificationsHub:Name**:  
    Weitere Informationen finden Sie unter *Name* in der Zusammenfassung **Essentials** am Anfang der**Übersicht**.  

    **NotificationHub:ConnectionString**:  
    Weitere Informationen finden Sie unter *DefaultFullSharedAccessSignature-* in **Zugriffsrichtlinien**

    > [!NOTE]
    > In Produktionsszenarien können Sie Optionen wie [Azure KeyVault](https://azure.microsoft.com/services/key-vault) in Betracht ziehen, um die Verbindungszeichenfolge sicher zu speichern. Der Einfachheit halber werden die geheimen Schlüssel den Anwendungseinstellungen von [Azure App Service](https://azure.microsoft.com/services/app-service/) hinzugefügt.

### <a name="authenticate-clients-using-an-api-key-optional"></a>Authentifizieren von Clients mit einem API-Schlüssel (optional)

API-Schlüssel sind nicht so sicher wie Token, sind aber für die Zwecke dieses Tutorials ausreichend. Ein API-Schlüssel kann problemlos über die [ASP.NET-Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1) konfiguriert werden.

1. Fügen Sie den **API-Schlüssel** zu den lokalen Konfigurationswerten hinzu.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Ersetzen Sie den Platzhalterwert durch ihren eigenen Wert, und notieren Sie ihn.

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemoApi**, wählen Sie **Neuer Ordner** aus dem Menü **Hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. Verwenden Sie dabei *Authentifizierung* als **Ordnernamen**.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Authentication**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **Allgemein** > **Leere Klassen** aus, geben Sie *ApiKeyAuthOptions.cs* als **Namen** ein, und klicken Sie dann auf **Neu** unter Hinzufügung der folgenden Implementierung.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Fügen Sie dem Ordner **Authentication** mit dem Namen *ApiKeyAuthHandler.cs* eine weitere **leere Klasse** hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > Ein [Authentifizierungs-Handler](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) ist ein Typ, der das Verhalten eines Schemas implementiert, in diesem Fall ein benutzerdefiniertes API-Schlüssel-Schema.

1. Fügen Sie dem Ordner **Authentication** mit dem Namen *ApiKeyAuthenticationBuilderExtensions.cs* eine weitere **leere Klasse** hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Erweiterungsmethode vereinfacht den Konfigurationscode der Middleware in **Startup.cs** damit Sie besser lesbar und allgemein leichter zu finden ist.

1. Aktualisieren Sie in **Startup.cs** die Methode **ConfigureServices**, um die API-Schlüsselauthentifizierung unterhalb des Aufrufs der Methode **services.AddControllers**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Aktualisieren Sie, immer noch in **Startup.cs**, die Methode **Configure**, um die Erweiterungsmethoden **UseAuthentication** und **UseAuthorization** für das **IApplicationBuilder**-Objekt der App aufzurufen. Stellen Sie sicher, dass diese Methoden nach **UseRouting** und vor **app.UseEndpoints** aufgerufen werden.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Durch Aufrufen von **UseAuthentication** wird die Middleware registriert, die die zuvor registrierten Authentifizierungsschemas (von **ConfigureServices**) verwendet. Diese Methode muss vor jeglicher anderen Middleware aufgerufen werden, die von der Authentifizierung der Benutzer abhängig ist.

### <a name="add-dependencies-and-configure-services"></a>Hinzufügen von Abhängigkeiten und Konfigurieren von Diensten

ASP.NET Core unterstützt das Softwareentwurfsmuster [Abhängigkeitsinjektion (Dependency Injection, DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1). Mit dieser Technik kann eine [Umkehrung der Steuerung (Inversion of Control, IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) zwischen Klassen und ihren Abhängigkeiten erreicht werden.  

Die Verwendung des Benachrichtigungshubs und des [Notification Hubs SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ist innerhalb eines Diensts gekapselt. Der Dienst wird durch eine geeignete Abstraktion registriert und verfügbar gemacht.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Abhängigkeiten**, und wählen Sie dann **NuGet-Pakete verwalten...** aus.

1. Suchen Sie nach **Microsoft.Azure.NotificationHubs**, und stellen Sie sicher, dass diese Option aktiviert ist.

1. Klicken Sie auf **Pakete hinzufügen** dann auf **Zustimmen**, wenn Sie aufgefordert werden, die Lizenzbedingungen zu akzeptieren.

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemoApi**, wählen Sie **Neuer Ordner** aus dem Menü **Hinzufügen** aus, und klicken Sie dann auf **Hinzufügen**. Verwenden Sie dabei *Modelle* als **Ordnernamen**.

1. **CONTROL** + **Klicken** Sie auf den Ordner **Modelle**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **Allgemein** > **Leere Klassen** aus, geben Sie *PushTemplates.cs* als **Namen** ein, und klicken Sie dann auf **Neu** unter Hinzufügung der folgenden Implementierung.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse enthält die tokenisierten Benachrichtigungsnutzlasten für die für dieses Szenario erforderlichen generischen und automatischen Benachrichtigungen. Die Nutzlasten werden außerhalb der [Installation](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) definiert, um Experimente zuzulassen, ohne vorhandene Installationen über den Dienst aktualisieren zu müssen. Die Verarbeitung von Änderungen an Installationen auf diese Weise ist nicht Gegenstand dieses Tutorials. Ziehen Sie für die Produktion [benutzerdefinierte Vorlagen](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages) in Betracht.

1. Wählen Sie **Allgemein** > **Leere Klassen** aus, geben Sie *DeviceInstallation.cs* als **Namen** ein, und klicken Sie dann auf **Neu** unter Hinzufügung der folgenden Implementierung.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Fügen Sie dem Ordner **Modelle** mit dem Namen *NotificationRequest.cs* eine weitere **leere Klasse** hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Fügen Sie dem Ordner **Modelle** mit dem Namen *NotificationHubOptions.cs* eine weitere **leere Klasse** hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Fügen Sie dem Projekt **PushDemoApi** einen neuen Ordner mit dem Namen *Dienste* hinzu.

1. Fügen Sie zum Ordner **Dienste** eine **leere Schnittstelle** namens *INotificationService.cs* hinzu, und fügen Sie dann die folgende Implementierung hinzu.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Fügen Sie zum Ordner **Dienste** eine **leere Klasse** namens *NotificationHubsService.cs* hinzu, und fügen Sie dann den folgenden Code hinzu, um die Schnittstelle **INotificationService** zu implementieren:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > Der für **SendTemplateNotificationAsync** bereitgestellte Tagausdruck ist auf 20 Tags beschränkt. Er ist für die meisten Operatoren auf 6 begrenzt, aber der Ausdruck enthält in diesem Fall nur ORs (||). Wenn die Anforderung mehr als 20 Tags enthält, muss sie in mehrere Anforderungen aufgeteilt werden. Weitere Details finden Sie in der Dokumentation [Weiterleitung und Tagausdrücke](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396).

1. Aktualisieren Sie die Methode**ConfigureServices** in **Startup.cs**, um **NotificationHubsService** als Singleton-Implementierung von **INotificationService** hinzuzufügen.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Erstellen der Benachrichtigungen-API

1. **CONTROL** + **Klicken** Sie auf den Ordner **Controller**, und wählen Sie dann **Neue Datei...** aus dem Menü **Hinzufügen** aus.

1. Wählen Sie **ASP.NET Core** > **Web-API-Controller-Klasse** aus, geben Sie *NotificationsController* als **Namen** ein, und klicken Sie dann auf **Neu**.

1. Fügen Sie am Anfang der Datei die folgenden Namespaces hinzu.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Aktualisieren Sie den vordefinierten Controller so, dass er von **ControllerBase** abgeleitet und mit dem Attribut **ApiController** ergänzt wird.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > Die Basisklasse **Controller** bietet Unterstützung für Ansichten, aber dies ist in diesem Fall nicht erforderlich, sodass stattdessen **ControllerBase** verwendet werden kann.

1. Wenn Sie entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen, sollten Sie **NotificationsController** auch mit dem **Authorize**-Attribut ergänzen.

    ```cs
    [Authorize]
    ```

1. Aktualisieren Sie den Konstruktor so, dass er die registrierte Instanz von **INotificationService** als Argument akzeptiert, und weisen Sie ihn einem readonly-Member zu.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. Ändern Sie in **launchSettings.json** (im Ordner **Eigenschaften**) die **launchUrl** von `weatherforecast` in *api/notifications*, damit sie mit der im **Route**-Attribut von **RegistrationsController** angegebenen URL übereinstimmt.

1. Starten Sie das Debugging (**BEFEHL** + **EINGABE**), um zu überprüfen, ob die App mit dem neuen **NotificationsController** funktioniert und den Status **401 – Nicht autorisiert** zurückgibt.

    > [!NOTE]
    > Visual Studio startet die App möglicherweise nicht automatisch im Browser. Sie verwenden [Postman](https://www.postman.com/downloads), um die API von diesem Punkt an zu testen.

1. Legen Sie auf einer neuen **[Postman](https://www.postman.com/downloads)** -Registerkarte die Anforderung auf **GET** fest, und geben Sie die Adresse unten ein.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Die localhost-Adresse sollte mit dem **applicationUrl**-Wert unter **Eigenschaften** > **launchSettings.json** übereinstimmen. Der Standardwert sollte `https://localhost:5001;http://localhost:5000` sein, aber dies muss überprüft werden, wenn Sie eine 404-Antwort erhalten.

1. Wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen, stellen Sie sicher, dass die Anforderungsheader Ihren **apikey**-Wert enthalten.

   | Schlüssel                            | Wert                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Klicken Sie auf die Schaltfläche **Senden**.

    > [!NOTE]
    > Sie sollten einen **200 OK**-Status mit einigen **JSON**-Inhalten erhalten.
    >
    > Wenn Sie eine Warnung der **SSL-Zertifikatüberprüfung** erhalten, können Sie die **[Postman](https://www.postman.com/downloads)** -Einstellung zum Anfordern der SSL-Zertifikatüberprüfung unter **Einstellungen** deaktivieren.

1. Ersetzen Sie die vorlagenbasierte Klassenmethoden durch den folgenden Code.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Erstellen der API-App

Sie erstellen jetzt eine [API-App](https://azure.microsoft.com/services/app-service/api/) in [Azure App Service](https://docs.microsoft.com/azure/app-service/) zum Hosten des Back-End-Diensts.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Ressource erstellen**, suchen und wählen Sie **API-App** aus, und klicken Sie dann auf **Erstellen**.

1. Aktualisieren Sie die folgenden Felder, und klicken Sie auf **Erstellen**.

    **App-Name:**  
    Geben Sie einen global eindeutigen Namen für die **API-App** ein.

    **Abonnement:**  
    Wählen Sie dasselbe **Zielabonnement** aus, in dem Sie den Benachrichtigungshub erstellt haben.

    **Ressourcengruppe:**  
    Wählen Sie dieselbe **Ressourcengruppe** aus, in der Sie den Benachrichtigungshub erstellt haben.

    **App Service-Plan/Standort:**  
    Erstellen Sie einen neuen **App Services-Plan**  

    > [!NOTE]
    > Wechseln Sie von der Standardoption zu einem Plan, der **SSL**-Unterstützung umfasst. Andernfalls müssen Sie bei Verwendung der mobilen App die entsprechenden Schritte ausführen, um zu verhindern, dass **http**-Anforderungen blockiert werden.

    **Application Insights:**  
    Behalten Sie die vorgeschlagene Option bei (eine neue Ressource wird unter diesem Namen erstellt), oder wählen Sie eine vorhandene Ressource aus.

1. Sobald die **API-App** bereitgestellt wurde, navigieren Sie zu dieser Ressource.

1. Notieren Sie sich die Eigenschaft **URL** in der Zusammenfassung **Essentials** oben in der **Übersicht**. Diese URL ist Ihr *Back-End-Endpunkt*, der später in diesem Tutorial verwendet wird.

    > [!NOTE]
    > In der URL wird der von Ihnen angegebene API-App-Name im Format `https://<app_name>.azurewebsites.net` verwendet.

1. Wählen Sie die Option **Konfiguration** aus der Liste aus (unter **Einstellungen**).  

1. Klicken Sie für jede der folgenden Einstellungen auf **Neue Anwendungseinstellung**, um den **Namen** und einen **Wert** einzugeben, und klicken Sie dann auf **OK**.

   | Name                               | Wert                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Dies sind die gleichen Einstellungen, die Sie zuvor in den Benutzereinstellungen definiert haben. Sie sollten Sie diese kopieren und übernehmen können. Die Einstellung **Authentication:ApiKey** ist nur erforderlich, wenn Sie die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchgeführt haben. In Produktionsszenarien können Sie Optionen wie [Azure KeyVault](https://azure.microsoft.com/services/key-vault) in Betracht ziehen. Diese wurden in diesem Fall der Einfachheit halber als Anwendungseinstellungen hinzugefügt.

1. Sobald alle Anwendungseinstellungen hinzugefügt wurden, klicken Sie auf **Speichern** und dann auf **Fortsetzen**.

### <a name="publish-the-backend-service"></a>Veröffentlichen des Back-End-Diensts

Als Nächstes wird die App der API-App bereitgestellt, damit sie für alle Geräte zur Verfügung steht.  

1. Ändern Sie die Konfiguration von **Debuggen** in **Release**, wenn Sie dies noch nicht getan haben.

1. **CONTROL** + **Klicken** Sie auf das Projekt **PushDemoApi**, und wählen Sie dann **In Azure veröffentlichen...** aus dem Menü **Veröffentlichen** aus.

1. Befolgen Sie den Authentifizierungsflow, wenn Sie dazu aufgefordert werden. Verwenden Sie das Konto, das Sie im vorigen Abschnitt [Erstellen der API-App](#create-the-api-app) verwendet haben.

1. Wählen Sie die zuvor erstellte **Azure App Service-API-App** aus der Liste als Ihr Veröffentlichungsziel aus, und klicken Sie dann auf **Veröffentlichen**.

Nach Abschluss des Assistenten wird die App in Azure veröffentlicht und anschließend geöffnet. Notieren Sie sich die **URL**, wenn Sie dies noch nicht getan haben. Diese URL ist Ihr *Back-End-Endpunkt*, der später in diesem Tutorial verwendet wird.

### <a name="validating-the-published-api"></a>Überprüfen der veröffentlichten API

1. Öffnen Sie auf der Registerkarte **[Postman](https://www.postman.com/downloads)** eine neue Registerkarte, legen Sie die Anforderung auf **POST** fest, und geben Sie die Adresse unten ein. Ersetzen Sie den Platzhalter durch die Basisadresse, die Sie im vorherigen Abschnitt [Veröffentlichung des Back-End-Diensts](#publish-the-backend-service) notiert haben.

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Die Basisadresse muss das Format folgende aufweisen: ``https://<app_name>.azurewebsites.net/``

1. Wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen, stellen Sie sicher, dass die Anforderungsheader Ihren **apikey**-Wert enthalten.

   | Schlüssel                            | Wert                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Wählen Sie die Option **raw** für den **Text** aus, wählen Sie dann **JSON** aus der Liste der Formatoptionen aus, und fügen Sie anschließend **JSON**-Platzhalterinhalt ein:

    ```json
    {}
    ```

1. Klicken Sie auf **Send**.

    > [!NOTE]
    > Sie sollten eine Statusmeldung **400 – Ungültige Anforderung** Status vom Dienst erhalten.

1. Führen Sie die Schritte 1-4 noch einmal aus, aber dieses Mal erhalten Sie bei Angabe des zu validierenden Anforderungsendpunkts dieselbe **400 – Ungültige Anforderung**-Antwort.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Es ist noch nicht möglich, die API mit gültigen Anforderungsdaten zu testen, da hierfür plattformspezifische Informationen von der mobilen Client-App erforderlich sind.
