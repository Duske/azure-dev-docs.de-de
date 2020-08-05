---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: a5872650356f8b3ae9b33381615d6128cb2d8d72
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401452"
---
### <a name="configure-required-android-packages"></a>Konfigurieren erforderlicher Android-Pakete

Beim Erstellen der App wird das Paket [automatisch verknüpft](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md). Zum Abschließen des Konfigurationsprozesses müssen noch ein paar zusätzliche Schritte ausgeführt werden:

### <a name="configure-android-manifest"></a>Konfigurieren des Android-Manifests

Überprüfen Sie in „android/app/src/main/AndroidManifest.xml“ den Paketnamen, die Berechtigungen und die erforderlichen Dienste. Vergewissern Sie sich, dass Sie die Empfänger `RNPushNotificationPublisher` und `RNPushNotificationBootEventReceiver` sowie den Dienst `RNPushNotificationListenerService` registriert haben. Die Benachrichtigungsmetadaten können zum Anpassen der Darstellung Ihrer Pushbenachrichtigungen verwendet werden.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Konfigurieren von Google-Diensten

Registrieren Sie Google-Dienste in „android/app/build.gradle“:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Kopieren Sie die Datei „google-services.json“, die Sie im Rahmen des FCM-Setups heruntergeladen haben, in den Projektordner „android/app/“.

### <a name="handle-push-notifications-for-android"></a>Behandeln von Pushbenachrichtigungen für Android

Sie haben den vorhandenen Dienst `RNPushNotificationListenerService` für die Behandlung eingehender Android-Pushbenachrichtigungen konfiguriert. Dieser Dienst wurde zuvor im Anwendungsmanifest registriert. Er verarbeitet eingehende Benachrichtigungen und sendet sie per Proxy an die plattformübergreifende React Native-Komponente. Es sind keine weiteren Schritte erforderlich.
