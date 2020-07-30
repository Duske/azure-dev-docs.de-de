---
author: alexeystrakh
ms.author: alstrakh
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: ef5f71997eca3d3ac35e004b5ee97668fb7582c9
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401476"
---
### <a name="create-the-react-native-solution"></a>Erstellen der React Native-Lösung

1. Aktualisieren Sie in `Terminal` Ihre für die Verwendung von React Native erforderlichen Umgebungstools mithilfe der folgenden Befehle:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. Falls bei Ihnen die Befehlszeilenschnittstelle von `React Native` installiert ist, führen Sie in `Terminal` den folgenden Befehl aus, um sie zu deinstallieren. Verwenden Sie `npx`, um automatisch auf die neueste verfügbare Version der React Native-Befehlszeilenschnittstelle zuzugreifen:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React Native verfügt über eine integrierte Befehlszeilenschnittstelle. Anstatt eine bestimmte Version der Befehlszeilenschnittstelle global zu installieren und zu verwalten, empfiehlt es sich, zur Laufzeit mithilfe von `npx` auf die aktuelle Version zuzugreifen, die in Node.js enthalten ist. Mit `npx react-native <command>` wird die aktuelle stabile Version der Befehlszeilenschnittstelle zum Zeitpunkt der Befehlsausführung heruntergeladen und ausgeführt.

1. Navigieren Sie zu Ihrem Projektordner, in dem Sie die neue Anwendung erstellen möchten. Geben Sie den Parameter `--template` an, um die TypeScript-basierte Vorlage zu verwenden:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Führen Sie den Metroserver aus. Dieser dient zum Erstellen von JavaScript-Paketen sowie zum Überwachen sämtlicher Codeaktualisierungen, um die Pakete in Echtzeit zu aktualisieren:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Führen Sie die iOS-App aus, um das Setup zu überprüfen. Vergewissern Sie sich vor dem Ausführen des folgenden Befehls, dass Sie einen iOS-Simulator gestartet oder eine Verbindung mit einem iOS-Gerät hergestellt haben:

    ```bash
    npx react-native run-ios
    ```

1. Führen Sie die Android-App aus, um das Setup zu überprüfen. Es sind ein paar zusätzliche Schritte erforderlich, um einen Android-Emulator oder ein Android-Gerät für den Zugriff auf den React Native-Metroserver zu konfigurieren. Mit den folgenden Befehlen wird das anfängliche JavaScript-Paket für Android generiert und im Ordner „assets“ platziert.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Dieses Skript wird vorab mit der ersten Version der App bereitgestellt. Konfigurieren Sie im Anschluss an die Bereitstellung Ihren Emulator oder Ihr Gerät für den Zugriff auf den Metroserver, indem Sie die IP-Adresse des Servers angeben. Führen Sie den folgenden Befehl aus, um die Android-Anwendung zu erstellen und auszuführen:

    ```bash
    npx react-native run-android
    ```

    Drücken Sie in der App `CMD+M` (Emulator), oder schütteln Sie das Gerät, um die Entwicklereinstellungen aufzufüllen, navigieren Sie zu `Settings` > `Change Bundle Location`, und geben Sie die IP-Adresse des Metroservers mit dem Standardport an: `<metro-server-ip-address>:8081`.

1. Nehmen Sie in der Datei `App.tsx` eine beliebige Änderung am Seitenlayout vor, speichern Sie die Datei, und vergewissern Sie sich, dass die Änderung automatisch für die iOS- und die Android-App übernommen wird.

    > [!NOTE]
    > Einen ausführlichen Leitfaden zur Einrichtung der Entwicklungsumgebung finden Sie in der [offiziellen Dokumentation](https://reactnative.dev/docs/environment-setup).

### <a name="install-required-packages"></a>Installieren erforderlicher Pakete

Für dieses Beispiel werden die folgenden drei Pakete benötigt:

1. [React Native-Pushbenachrichtigungen (iOS)](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [GitHub-Projekt](https://github.com/react-native-community/push-notification-ios)

    Dieses Paket wurde erstellt, als „PushNotificationIOS“ aus dem Kern von React Native ausgelagert wurde. Das Paket dient zur nativen Implementierung von Pushbenachrichtigungen für iOS und stellt eine React Native-Schnittstelle für den Zugriff bereit. Führen Sie den folgenden Befehl aus, um das Paket zu installieren:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [React Native-Pushbenachrichtigungen (plattformübergreifend)](https://www.npmjs.com/package/react-native-push-notification)

    Durch dieses Paket werden lokale Benachrichtigungen und Remotebenachrichtigungen in iOS und Android plattformübergreifend implementiert. Führen Sie den folgenden Befehl aus, um das Paket zu installieren:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Geräteinformationspaket](https://www.npmjs.com/package/react-native-device-info) Dieses Paket stellt Geräteinformationen zur Laufzeit bereit. Nutzen Sie es zum Definieren eines Gerätebezeichners, der zum Registrieren für Pushbenachrichtigungen verwendet wird. Führen Sie den folgenden Befehl aus, um das Paket zu installieren:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementieren der plattformübergreifenden Komponenten

1. Erstellen und implementieren Sie `DemoNotificationHandler`:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Erstellen und implementieren Sie `DemoNotificationService`:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Erstellen und implementieren Sie `DemoNotificationRegistrationService`:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Konfigurieren Sie die App. Öffnen Sie `package.json`, und fügen Sie die folgende Skriptdefinition hinzu:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Führen Sie anschließend das Skript aus. Daraufhin wird die Standardkonfiguration in den Ordner `config` kopiert.

    ```bash
    yarn configure
    ```

    Zum Schluss muss noch die im vorherigen Schritt kopierte Konfigurationsdatei mit den API-Zugriffsinformationen aktualisiert werden. Geben Sie die Parameter `apiKey` und `apiUrl` an:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementieren der plattformübergreifenden Benutzeroberfläche

1. Definieren des Seitenlayouts

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Anwenden von Stilen

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Initialisieren der Seitenkomponente

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definieren von Klickhandlern für Schaltflächen

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Behandeln empfangener Tokenregistrierungen und Pushbenachrichtigungen

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```