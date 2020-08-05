---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 6d9f12534c8bd5ab4fac8a1e337196fcc7ad559e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401481"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurieren Ihrer Notification Hubs-Instanz mit APNs-Informationen

Wählen Sie unter **Notification Services** die Option **Apple** aus, und führen Sie dann je nach der Methode, die Sie im Abschnitt [Erstellen eines Zertifikats für Notification Hubs](#creating-a-certificate-for-notification-hubs) ausgewählt haben, die entsprechenden Schritte aus.  

> [!NOTE]
> Verwenden Sie **Produktion** nur dann als **Anwendungsmodus**, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.

### <a name="option-1-using-a-p12-push-certificate"></a>OPTION 1: Verwenden eines P12-Zertifikats

1. Wählen Sie **Certificate**aus.

1. Wählen Sie das Dateisymbol aus.

1. Wählen Sie die zuvor exportierte P12-Datei aus, und wählen Sie dann **Öffnen**.

1. Geben Sie bei Bedarf das richtige Kennwort an.

1. Wählen Sie den Modus **Sandbox** aus.

1. Wählen Sie **Speichern** aus.

### <a name="option-2-using-token-based-authentication"></a>OPTION 2: Verwenden der tokenbasierten Authentifizierung

1. Wählen Sie **Token** aus.
1. Geben Sie die folgenden Werte ein, die Sie zuvor abgerufen haben:

    - **Schlüssel-ID**
    - **Bundle-ID**
    - **Team-ID**
    - **Token**

1. Wählen Sie **Sandbox** aus.
1. Wählen Sie **Speichern** aus.
