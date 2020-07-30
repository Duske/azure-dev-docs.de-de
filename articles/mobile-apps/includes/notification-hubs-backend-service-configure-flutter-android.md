---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 62ff9b49f6b2ba30d3538b2c13812ca7fc1ef583
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401465"
---
### <a name="add-the-google-services-json-file"></a>Hinzufügen der JSON-Datei von Google Services

1. **STRG** + **Klicken** Sie aufden Ordner **android**, und wählen Sie dann **In Android Studio öffnen** aus. Wechseln Sie dann zur Ansicht **Projekt** (sofern nicht bereits geschehen).

1. Suchen Sie nach der Datei *google-services.json*, die Sie zuvor beim Einrichten des **PushDemo**-Projekts in der [Firebase-Konsole](https://console.firebase.google.com) heruntergeladen haben. Ziehen Sie sie anschließend in das Stammverzeichnis des **app**-Moduls (**android** > **android** > **app**).

### <a name="configure-build-settings-and-permissions"></a>Konfigurieren von Buildeinstellungen und Berechtigungen

1. Wechseln Sie aus der Ansicht **Projekt** zu **Android**.

1. Öffnen Sie **AndroidManifest.xml**, und fügen Sie dann die Berechtigungen **INTERNET** und **READ_PHONE_STATE** nach dem **application**-Element vor dem schließenden **</manifest>** -Tag hinzu.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Hinzufügen der Firebase-SDKs

1. Öffnen Sie in **Android Studio**die Datei **build.gradle** auf Projektebene (**Gradle Scripts** > **build.gradle (Project: android)** ). Stellen Sie sicher, dass der Klassenpfad „com.google.gms:google-services“ im Knoten ``buildscript`` > **dependencies** vorhanden ist.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Stellen Sie sicher, dass Sie auf die neueste Version gemäß den Anweisungen in der [Firebase-Konsole](https://console.firebase.google.com) verweisen, als Sie das **Android-Projekt** erstellt haben.

1. Wenden Sie in der Datei **build.gradle** auf App-Ebene (**Gradle Scripts** > **build.gradle (Module: app)** ) das **Google Services Gradle-Plug-In** an. Wenden Sie das Plug-In direkt über dem Knoten **Android** an.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. Fügen Sie in derselben Datei im Knoten **dependencies** die Abhängigkeit für die Android-Bibliothek **Cloud Messaging** hinzu.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Stellen Sie sicher, dass Sie auf die neueste Version gemäß der [Cloud Messaging-Android-Clientdokumentation](https://firebase.google.com/docs/cloud-messaging/android/client) verweisen.

1. Speichern Sie die Änderungen, und klicken Sie dann auf die Schaltfläche **Jetzt synchronisieren** (über die Eingabeaufforderung der Symbolleiste) oder auf **Projekt mit Gradle-Dateien synchronisieren**.

### <a name="handle-push-notifications-for-android"></a>Behandeln von Pushbenachrichtigungen für Android

1. **STRG** + **klicken** Sie in **Android Studio** auf den Paketordner **com.<Ihre_Organisation>.pushdemo** (**app** > **src** > **main** > **kotlin**), und wählen Sie **Paket** aus dem Menü **Neu** aus. Geben Sie **services** als Name ein, und drücken Sie dann die **EINGABETASTE**.

1. **STRG** + **Klicken** Sie auf den Ordner **services**, und wählen Sie **Kotlin-Datei/-Klasse** aus dem Menü **Neu** aus. Geben Sie **DeviceInstallationService** als Namen ein, und drücken Sie dann die **EINGABETASTE**.

1. Implementieren Sie den **DeviceInstallationService** mit dem folgenden Code.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/deviceinstallation`-Kanal. Dies wurde in **DeviceInstallationService.dart** im Flutter-Teil der App definiert. In diesem Fall erfolgen die Aufrufe vom allgemeinen Code zum nativen Host. Stellen Sie sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.
    >
    > Diese Klasse bietet eine eindeutige ID (unter Verwendung von [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) als Teil der Nutzlast der Benachrichtigungshubregistrierung.

1. Fügen Sie dem Ordner **services** eine weitere **Kotlin-Datei/-Klasse** namens *NotificationRegistrationService* hinzu, und fügen Sie dann den folgenden Code hinzu.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/notificationregistration`-Kanal. Dies wurde in **NotificationRegistrationService.dart** im Flutter-Teil der App definiert. In diesem Fall erfolgen die Aufrufe vom nativen Host zum allgemeinen Code. Stellen Sie auch hier sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.

1. Fügen Sie dem Ordner **services** eine weitere **Kotlin-Datei/-Klasse** namens *NotificationActionService* hinzu, und fügen Sie dann den folgenden Code hinzu.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse implementiert das plattformspezifische Pendant für den `com.<your_organization>.pushdemo/notificationaction`-Kanal. Dies wurde in **NotificationActionService.dart** im Flutter-Teil der App definiert. In diesem Fall können Aufrufe in beide Richtungen erfolgen. Stellen Sie sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.

1. Fügen Sie dem Paket **com.<Ihre_Organisation>.pushdemo** eine neue **Kotlin-Datei/-Klasse** mit dem Namen *PushNotificationsFirebaseMessagingService* hinzu, und verwenden Sie für die Implementierung den folgenden Code.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Diese Klasse ist für die Verarbeitung von Benachrichtigungen zuständig, wenn die App im Vordergrund ausgeführt wird. Sie ruft bedingt **triggerAction** für **NotificationActionService** auf, wenn eine Aktion in der Nutzlast der Benachrichtigung enthalten ist, die in **onMessageReceived** empfangen wird. Dadurch wird auch **refreshRegistration** für **NotificationRegistrationService** aufgerufen, wenn das **Firebase**-Token durch Überschreiben der **onNewToken**-Funktion erneut generiert wird.
    >
    > Stellen Sie hier erneut sicher, dass Sie alle Vorkommen von **<your_organization>** durch Ihre eigene Organisation ersetzen.

1. Fügen Sie in **AndroidManifest.xml** (**app** > **src** > **main**) den **PushNotificationsFirebaseMessagingService** unter dem **application**-Element mit dem `com.google.firebase.MESSAGING_EVENT`-Intentfilter hinzu.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. Stellen Sie in **DeviceInstallationService** sicher, dass am Anfang der Datei die folgenden Importe vorhanden sind.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Ersetzen Sie **< your_organization >** durch ihren eigenen Organisationswert.

1. Aktualisieren Sie den Platzhaltertext *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation*, um den Tokenwert aus **PushNotificationFirebaseMessagingService** abzurufen.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. Stellen Sie in **MainActivity** sicher, dass am Anfang der Datei die folgenden Importe vorhanden sind.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Ersetzen Sie **< your_organization >** durch ihren eigenen Organisationswert.

1. Fügen Sie eine Variable hinzu, um einen Verweis auf **DeviceInstallationService** zu speichern.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Fügen Sie eine Funktion namens **ProcessNotificationActions** hinzu, um zu überprüfen, ob ein **Intent** einen zusätzlichen Wert namens **action** aufweist. Lösen Sie diese Aktion bedingt aus, oder speichern Sie sie zur späteren Verwendung, wenn die Aktion während des App-Starts verarbeitet wird.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Überschreiben Sie die Funktion **onNewIntent**, um **ProcessNotificationActions** aufzurufen.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Da das **LaunchMode**-Element für **MainActivity** auf **SingleTop** festgelegt ist, wird ein **Intent** an die vorhandene **Activity**-Instanz über die **onNewIntent**-Funktion statt über die **onCreate**-Funktion gesendet, daher müssen Sie einen eingehenden **Intent** sowohl in der **onCreate**- als auch in der **onNewIntent**-Funktion behandeln.

1. Überschreiben Sie die **onCreate**-Funktion, und legen Sie den **DeviceInstallationService** auf eine neue Instanz von **DeviceInstallationService** fest.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Legen Sie die **notificationActionService**- und **notificationRegistrationService**-Eigenschaften für **PushNotificationFirebaseMessagingServices** fest.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Rufen Sie in der gleichen Funktion **FirebaseInstanceId.getInstance().instanceId** bedingt auf. Implementieren Sie **OnCompleteListener**, um den sich ergebenden **token**-Wert für **PushNotificationFirebaseMessagingService** festzulegen, bevor Sie **refreshRegistration** aufrufen.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Rufen Sie in **onCreate** **processNotificationActions** am Ende der Funktion auf. Verwenden Sie *true* für das *launchAction*-Argument, um anzugeben, dass diese Aktion beim Starten der App verarbeitet wird.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Sie müssen die App jedes Mal, wenn Sie sie von einer Debugsitzung aus ausführen und beenden, neu registrieren, um weiterhin Pushbenachrichtigungen zu erhalten.
