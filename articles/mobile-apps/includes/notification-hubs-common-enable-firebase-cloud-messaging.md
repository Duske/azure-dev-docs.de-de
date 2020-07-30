---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: fb2311fe9256daa53b2687c43b508c4af4f97bd2
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401494"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Erstellen eines Firebase-Projekts und Aktivieren von Firebase Cloud Messaging für Android

1. Melden Sie sich bei der [Firebase-Konsole](https://firebase.google.com/console/) an. Erstellen Sie ein neues Firebase-Projekt, wobei Sie **PushDemo** als **Projektnamen** eingeben.

    > [!NOTE]
    > Es wird ein eindeutiger Name für Sie generiert. Standardmäßig besteht dies aus einer Variante des von Ihnen angegebenen Namens (in Kleinbuchstaben) und einer generierten Zahl, getrennt durch einen Bindestrich. Wenn Sie möchten, können Sie dies ändern, vorausgesetzt, es ist immer noch global eindeutig.

1. Klicken Sie nach der Erstellung Ihres Projekts auf **Add Firebase to your Android app** (Firebase der Android-App hinzufügen).

    ![Hinzufügen von Firebase zu Ihrer Android-App](../media/notification-hubs-add-firebase-to-android-app.png)

1. Führen Sie auf der Seite **Hinzufügen von Firebase zu Ihrer Android-App** die folgenden Schritte aus.
    1. Geben Sie in **Android-Paketname** einen Namen für Ihr Paket ein. Beispiel: `com.<organization_identifier>.<package_name>`.

        ![Angeben des Paketnamens](../media/specify-package-name-firebase-cloud-messaging-settings.png)
    1. Wählen Sie **Register app** (App registrieren) aus.  
    1. Klicken Sie auf **google-services.json herunterladen**. Speichern Sie die Datei dann zur späteren Verwendung in einem lokalen Ordner, und wählen Sie **Weiter** aus.  

        ![Herunterladen von „google-services.json“](../media/download-google-service-button.png)
    1. Wählen Sie **Weiter** aus.
    1. Wählen Sie **Weiter zur Konsole** aus.

        > [!NOTE]
        > Wenn die Schaltfläche **Weiter zur Konsole** aufgrund der Aktivierung von *Installation überprüfen* nicht aktiviert ist, wählen Sie **Diesen Schritt überspringen** aus.

1. Klicken Sie in der Firebase-Konsole auf das Zahnrad für Ihr Projekt. Klicken Sie dann auf **Projekteinstellungen**.

    ![Klicken auf „Projekteinstellungen“](../media/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Wenn Sie die Datei **google-services.json** nicht heruntergeladen haben, können Sie dies auf dieser Seite tun.

1. Wechseln Sie oben zur Registerkarte **Cloud Messaging**. Kopieren und speichern Sie den **Serverschlüssel** für eine spätere Verwendung. Dieser Wert dient zum Konfigurieren des Notification Hub.

    ![Kopieren des Serverschlüssels](../media/server-key.png)
