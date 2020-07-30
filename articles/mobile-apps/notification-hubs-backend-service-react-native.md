---
title: Senden von Pushbenachrichtigungen an React Native-Apps mit Azure Notification Hubs über einen Back-End-Dienst | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Pushbenachrichtigungen an React Native-Apps senden, die Azure Notification Hubs über einen Back-End-Dienst verwenden.
author: alexeystrakh
ms.service: mobile-services
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: alstrakh
ms.openlocfilehash: ddc35adf00b41dd421cbcd64437a7366bf4c74b1
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401453"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutorial: Senden von Pushbenachrichtigungen an React Native-Apps mit Azure Notification Hubs über einen Back-End-Dienst  

[![Beispiel herunterladen](media/download.png) Herunterladen des Beispiels](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

In diesem Tutorial wird [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) verwendet, um Pushbenachrichtigungen an eine [React Native](https://reactnative.dev/)-Anwendung für **Android** und **iOS** zu senden.  

[!INCLUDE [Notification Hubs Backend Service Introduction](includes/notification-hubs-backend-service-introduction.md)]

In diesem Tutorial werden die folgenden Schritte erklärt:

> [!div class="checklist"]
>
> * [Einrichten von Push Notification Services und Azure Notification Hubs](#set-up-push-notification-services-and-azure-notification-hub)
> * [Erstellen einer Anwendung für das ASP.NET Core-Web-API-Back-End](#create-an-aspnet-core-web-api-backend-application)
> * [Erstellen einer plattformübergreifenden React Native-Anwendung](#create-a-cross-platform-react-native-application)
> * [Konfigurieren des nativen Android-Projekts für Pushbenachrichtigungen](#configure-the-native-android-project-for-push-notifications)
> * [Konfigurieren des nativen iOS-Projekts für Pushbenachrichtigungen](#configure-the-native-ios-project-for-push-notifications)
> * [Testen der Lösung](#test-the-solution)

## <a name="prerequisites"></a>Voraussetzungen

Um dies nachvollziehen zu können, benötigen Sie Folgendes:

* Ein [Azure-Abonnement](https://portal.azure.com), in dem Sie Ressourcen erstellen und verwalten können.
* Einen Mac mit installiertem [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) (oder einen PC, auf dem [Visual Studio 2019](https://visualstudio.microsoft.com/vs) mit der Workload **Mobile-Entwicklung mit .NET** ausgeführt wird).
* Die Möglichkeit, die App entweder unter **Android** (physische oder Emulatorgeräte) oder **iOS** (nur physische Geräte) auszuführen.

Für Android benötigen Sie Folgendes:

* Ein vom Entwickler entsperrtes physisches Gerät oder einen Emulator *(mit API 26 und höher und installierten Google Play Services)* .

Für iOS benötigen Sie Folgendes:

* Ein aktives [Apple Developer-Konto](https://developer.apple.com).
* Ein physisches iOS-Gerät, das [für Ihr Entwicklerkonto registriert ist](https://help.apple.com/developer-account/#/dev40df0d9fa) *(mit iOS 13.0 und höher)* .
* Ein in Ihrer **Keychain** installiertes **P12**-[Entwicklungszertifikat](https://help.apple.com/developer-account/#/dev04fd06d56), mit dem Sie eine [App auf einem physischen Gerät ausführen](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca) können.

> [!NOTE]
> Der iOS-Simulator unterstützt keine Remotebenachrichtigungen, sodass zum Nachverfolgen dieses Beispiels unter iOS ein physisches Gerät erforderlich ist. Für dieses Tutorial müssen Sie die App aber nicht unter beiden Betriebssystemen (**Android** und **iOS**) ausführen.

Sie können die Schritte in diesem Beispiel zu den Grundprinzipien ohne vorherige Erfahrung ausführen. Kenntnisse der folgenden Aspekte sind jedoch von Vorteil.

* [Apple Developer-Portal](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Google Firebase-Konsole](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) und [Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs](/azure/notification-hubs/ios-sdk-get-started.md)
* [React Native](https://reactnative.dev/docs/getting-started)

Die angegebenen Schritte gelten für [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) sowie für [Visual Studio Code](https://code.visualstudio.com/download), können aber auch in [Visual Studio 2019](https://visualstudio.microsoft.com/vs) verwendet werden.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Einrichten von Push Notification Services und Azure Notification Hubs

In diesem Abschnitt richten Sie **[Firebase Cloud Messaging (FCM)](https://firebase.google.com/docs/cloud-messaging)** und **[Apple Push Notification Services (APNS)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** ein. Anschließend erstellen und konfigurieren Sie einen Notification Hub für das Arbeiten mit diesen Diensten.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Erstellen einer Anwendung für das ASP.NET Core-Web-API-Back-End

In diesem Abschnitt wird das [ASP.NET Core-Web-API](https://dotnet.microsoft.com/apps/aspnet/apis)-Back-End für die [Geräteregistrierung](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) und das Senden von Benachrichtigungen an die mobile React Native-App erstellt.

[!INCLUDE [Create an ASP.NET Core Web API backend application](includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Erstellen einer plattformübergreifenden React Native-Anwendung

In diesem Abschnitt wird eine mobile [React Native](https://reactnative.dev/)-Anwendung mit plattformübergreifender Implementierung von Pushbenachrichtigungen erstellt.

[!INCLUDE [Sample application generic overview](includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](includes/notification-hubs-backend-service-sample-app-react-native.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Konfigurieren des nativen Android-Projekts für Pushbenachrichtigungen

[!INCLUDE [Configure the native Android project](includes/notification-hubs-backend-service-configure-react-native-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Konfigurieren des nativen iOS-Projekts für Pushbenachrichtigungen

[!INCLUDE [Configure the native iOS project](includes/notification-hubs-backend-service-configure-react-native-ios.md)]

## <a name="test-the-solution"></a>Testen der Lösung

Sie können jetzt das Senden von Benachrichtigungen über den Back-End-Dienst testen.

[!INCLUDE [Testing the solution](includes/notification-hubs-backend-service-testing.md)]

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [Troubleshooting](includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Verwandte Links

* [Übersicht über Azure Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview.md)
* [Installieren von Visual Studio für Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Herunterladen von Visual Studio Code](https://code.visualstudio.com/download)
* [Einrichten der Entwicklungsumgebung](https://reactnative.dev/docs/environment-setup)
* [Notification Hubs SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Notification Hubs-SDK auf GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrieren bei einem Anwendungs-Back-End](/azure/notification-hubs/notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Registrierungsverwaltung](/azure/notification-hubs/notification-hubs-push-notification-registration-management.md)
* [Arbeiten mit Tags](/azure/notification-hubs/notification-hubs-tags-segment-push-message.md)
* [Arbeiten mit benutzerdefinierten Vorlagen](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)

## <a name="next-steps"></a>Nächste Schritte

Sie verfügen nun über eine einfache React Native-App, die über einen Back-End-Dienst mit einem Notification Hub verbunden ist und Benachrichtigungen senden und empfangen kann.

[!INCLUDE [Next steps](includes/notification-hubs-backend-service-next-steps.md)]