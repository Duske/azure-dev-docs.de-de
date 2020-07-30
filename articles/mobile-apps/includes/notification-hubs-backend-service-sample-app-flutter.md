---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6a26b6e6f40ae4cb705799a2d49f2f62904c08c2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401457"
---
### <a name="create-the-flutter-solution"></a>Erstellen der Flutter-Lösung

1. Öffnen Sie eine neue Instanz von **Visual Studio Code**.

1. Öffnen Sie die **Befehlspalette** (**UMSCHALT** + **Befehl** + **P**).

1. Wählen Sie den Befehl **Flutter: Neues Projekt** aus, und drücken Sie die **EINGABETASTE**.

1. Geben Sie *push_demo* als **Projektnamen** ein, und wählen Sie dann einen **Projektspeicherort** aus.

1. Wenn Sie dazu aufgefordert werden, wählen Sie **Pakete abrufen** aus.

1. **STRG** + **Klicken** Sie auf den Ordner **kotlin** (unter **app** > **src** > **main**), und wählen Sie dann **In Finder anzeigen** aus. Benennen Sie dann die untergeordneten Ordner (unter dem Ordner **kotlin**) in ```com```, ```<your_organization>``` bzw. ```pushdemo``` um.

    > [!NOTE]
    > Wenn Sie die **Visual Studio Code**-Vorlage verwenden, heißen diese Ordner standardmäßig **com**, **example** und **<project_name>** . Unter der Annahme, dass **mobcat** für die **Organisation** verwendet wird, sollte die Ordnerstruktur wie folgt angezeigt werden:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. Aktualisieren Sie in **Visual Studio Code** den Wert **applicationId** in **android** > **app** > **build.gradle** in `com.<your_organization>.pushdemo`.

    > [!NOTE]
    > Sie sollten für den Platzhalter *<your_organization>* Ihren eigenen Organisationsnamen verwenden. Wenn Sie z. B. **mobcat** als Organisation verwenden, führt dies zu einem **Paketnamen** mit dem Wert *com.mobcat.pushdemo*.

1. Aktualisieren Sie das Attribut **package** in den **AndroidManifest.xml**-Dateien unter **src** > **debug**, **src** > **main** bzw. **src** > **profile**. Stellen Sie sicher, dass die Werte der **applicationId** entsprechen, den Sie im vorherigen Schritt verwendet haben.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Aktualisieren Sie das ```android:label```-Attribut in der Datei **AndroidManifest.xml** unter **src** > **main** in *PushDemo*. Fügen Sie dann das ```android:allowBackup```-Attribut direkt unter ```android:label``` hinzu, und legen Sie seinen Wert auf **false** fest.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Öffnen Sie die Datei **build.gradle** auf App-Ebene (**android** > **app** > **build.gradle**), und aktualisieren Sie dann die *compileSdkVersion* (aus dem Abschnitt **android**) für die Verwendung von API **29**. Aktualisieren Sie dann die Werte *minSdkVersion* und *targetSdkVersion* Werte (aus dem Abschnitt **DefaultConfig**) in **26** bzw. **29**.

    > [!NOTE]
    > Für die Zwecke dieses Tutorials werden nur Geräte mit **API-Ebene 26 und höher** unterstützt. Sie können diese Einstellung jedoch erweitern, um Geräte mit älteren Versionen zu unterstützen.

1. **STRG** + **Klicken** Sie auf den Ordner **ios**, und wählen Sie dann **In Xcode öffnen** aus.

1. Klicken Sie in **Xcode** auf **Runner** (**xcodeproj** oben, nicht auf den Ordner). Wählen Sie dann das Ziel **Runner** aus, und wählen Sie die Registerkarte **Allgemein** aus. Wenn Sie die Buildkonfiguration **Alle** ausgewählt haben, aktualisieren Sie die **Bündel-ID** in `com.<your_organization>.PushDemo`.

    > [!NOTE]
    > Sie sollten für den Platzhalter *<your_organization>* Ihren eigenen Organisationsnamen verwenden. Wenn Sie z. B. **mobcat** als Organisation verwenden, führt dies zu einer **Bündel-ID** mit dem Wert *com.mobcat.PushDemo*.

1. Klicken Sie auf **Info.plist**, und aktualisieren Sie dann den Wert für den **Bündelnamen** in **PushDemo**.

1. Schließen Sie **Xcode**, und kehren Sie zu **Visual Studio Code** zurück.

1. Öffnen Sie in **Visual Studio Code** die Datei **pubspec.yaml**, und fügen Sie die **Dart-Pakete** [http](https://pub.dev/packages/http) und [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) als Abhängigkeiten hinzu. Speichern Sie dann die Datei, und klicken Sie auf **Pakete abrufen**, wenn Sie dazu aufgefordert werden.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. Ändern Sie im **Terminal** das Verzeichnis in den Ordner **ios** (für das Flutter-Projekt). Führen Sie dann den Befehl [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) aus, um neue Pods zu installieren (erforderlich für das [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)-Paket).

1. **STRG** + **Klicken** Sie auf den Ordner **lib**, und wählen Sie dann im Menü **Neue Datei** aus, indem Sie *main_page.dart* als Dateinamen verwenden. Fügen Sie anschließend den folgenden Code hinzu.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. Ersetzen Sie in **main.dart** den vorlagenbasierten Code durch Folgendes.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. Erstellen Sie die App im **Terminal**, und führen Sie sie auf jeder Zielplattform aus, um zu testen, ob die auf der Vorlage basierende App auf Ihren Geräten ausgeführt wird. Stellen Sie sicher, dass die unterstützten Geräte verbunden sind.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementieren der plattformübergreifenden Komponenten

1. **STRG** + **Klicken** Sie auf den Ordner **lib**, und wählen Sie dann im Menü **Neuer Ordner** aus, indem Sie *models* als **Ordnernamen** verwenden.

1. **STRG** + **Klicken** Sie auf den Ordner **models**, und wählen Sie dann im Menü **Neue Datei** aus, indem Sie *device_installation.dart* als Dateinamen verwenden. Fügen Sie anschließend den folgenden Code hinzu.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Fügen Sie dem Ordner **models** eine neue Datei namens *push_demo_action.dart* hinzu, die die Enumeration der in diesem Beispiel unterstützten Aktionen definiert.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Fügen Sie dem Projekt einen neuen Ordner mit dem Namen **services** hinzu, und fügen Sie diesem Ordner dann eine neue Datei mit dem Namen *device_installation_service.dart* mit der folgenden Implementierung hinzu.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Sie sollten für den Platzhalter *<your_organization>* Ihren eigenen Organisationsnamen verwenden. Wenn Sie z. B. **mobcat** als Organisation verwenden, führt dies zu einem [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html)-Namen mit dem Wert *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Diese Klasse kapselt das Arbeiten mit der zugrundeliegenden nativen Plattform zur Erfassung der erforderlichen Geräteinstallationsdetails. Ein [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) ermöglicht bidirektionale asynchrone Kommunikation mit den zugrunde liegenden nativen Plattformen. Das plattformspezifische Gegenstück für diesen Kanal wird in späteren Schritten erstellt.

1. Fügen Sie diesem Ordner eine weitere Datei namens *notification_action_service.dart* mit der folgenden Implementierung hinzu.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Dies wird als einfacher Mechanismus zur Zentralisierung der Verarbeitung von Benachrichtigungsaktionen verwendet, sodass sie mithilfe einer stark typisierten Enumeration plattformübergreifend verarbeitet werden können. Der Dienst ermöglicht es der zugrunde liegenden nativen Plattform, eine Aktion auszulösen, wenn eine solche in der Benachrichtigungsnutzlast angegeben wird. Er ermöglicht es dem gemeinsamen Code auch, rückwirkend zu überprüfen, ob eine Aktion während des Anwendungsstarts angegeben wurde, sobald Flutter bereit ist, diese zu verarbeiten. Beispielsweise, wenn die App durch Tippen auf eine Benachrichtigung vom Benachrichtigungszentrum gestartet wird.

1. Fügen Sie dem Ordner **services** eine neue Datei namens *notification_registration_service.dart* mit der folgenden Implementierung hinzu.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Diese Klasse kapselt die Verwendung von **DeviceInstallationService** und die Anforderungen an den Back-End-Dienst zur Durchführung der erforderlichen Aktionen für Registrierung, Aufheben der Registrierung und Aktualisieren der Registrierung. Das Argument **apiKey** ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.

1. Fügen Sie dem Ordner **lib** eine neue Datei namens *config.dart* mit der folgenden Implementierung hinzu.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Dies wird als einfache Methode zum Definieren von App-Geheimnissen verwendet. Ersetzen Sie die Platzhalterwerte durch eigene Werte. Sie sollten sich diese bei der Erstellung des Back-End-Diensts notiert haben. Die **API-App-URL** sollte `https://<api_app_name>.azurewebsites.net/` lauten. Der **apiKey**-Member ist nur erforderlich, wenn Sie sich entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen.
    >
    > Stellen Sie sicher, dass Sie dies Ihrer gitignore-Datei hinzufügen, um zu vermeiden, dass diese Geheimnisse an die Quellcodeverwaltung committet werden.

### <a name="implement-the-cross-platform-ui"></a>Implementieren der plattformübergreifenden Benutzeroberfläche

1. Ersetzen Sie in **main_page.dart** die **build**-Funktion durch Folgendes.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Fügen Sie die erforderlichen Importe am Anfang der Datei **main_page.dart** hinzu.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Fügen Sie der **_MainPageState**-Klasse ein Feld hinzu, um einen Verweis auf den **NotificationRegistrationService** zu speichern.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Implementieren Sie in der **_MainPageState**-Klasse die Ereignishandler für die **onPressed**-Ereignisse der Schaltflächen **Register** (Registrieren) und **Deregister** (Registrierung aufheben). Rufen Sie die entsprechenden **Register**/**Deregister**-Methoden auf, und zeigen Sie dann eine Warnung an, um das Ergebnis anzugeben.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Stellen Sie nun in **main.dart** sicher, dass am Anfang der Datei die folgenden Importe vorhanden sind.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Deklarieren Sie eine Variable zum Speichern eines Verweises auf eine Instanz von **NotificationActionService**, und initialisieren Sie sie.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Fügen Sie Funktionen hinzu, um die Anzeige einer Warnung zu verarbeiten, wenn eine Aktion ausgelöst wird.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Aktualisieren Sie die Funktion **main** zur Beobachtung des **actionTriggered**-Streams von **NotificationActionService**, und prüfen Sie auf alle Aktionen, die während des App-Starts erfasst wurden.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Dies soll lediglich den Empfang und die Weitergabe von Pushbenachrichtigungsaktionen veranschaulichen. Diese werden in der Regel im Hintergrund behandelt, z. B. wenn Sie zu einer bestimmten Ansicht navigieren oder einige Daten aktualisieren, anstatt in diesem Fall eine Warnung anzuzeigen.
