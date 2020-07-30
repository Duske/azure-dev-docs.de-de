---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: b5eb04656e4e8e4623e4ca2fe22e75010e1ae1f6
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401488"
---
### <a name="create-a-notification-hub"></a>Erstellen eines Notification Hubs 

In diesem Abschnitt erstellen Sie einen Benachrichtigungshub und konfigurieren die Authentifizierung mit **APNS**. Sie können ein P12-Push-Zertifikat oder eine tokenbasierte Authentifizierung verwenden. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie mit Schritt 5 fortfahren.

1. Melden Sie sich bei [Azure](https://portal.azure.com) an.

1. Klicken Sie auf **Ressource erstellen**, suchen und wählen Sie **Benachrichtigungshub** aus, und klicken Sie dann auf **Erstellen**.

1. Aktualisieren Sie die folgenden Felder, und klicken Sie auf **Erstellen**:

    **GRUNDLEGENDE INFORMATIONEN**  

    **Abonnement:** Wählen Sie das **Zielabonnement** aus der Dropdown-Liste aus.  
    **Ressourcengruppe:** Erstellen Sie eine neue **Ressourcengruppe** (oder verwenden Sie eine bereits vorhandene).  

    **DETAILS ZUM NAMESPACE**  

    **Benachrichtigungshub-Namespace:** Geben Sie einen global eindeutigen Namen für den Namespace **Benachrichtigungshub** ein.  

    > [!NOTE]
    > Stellen Sie sicher, dass die Option **Neu erstellen** für dieses Feld ausgewählt ist.

    **DETAILS ZUM BENACHRICHTIGUNGSHUB**  

    **Benachrichtigungshub:** Geben Sie einen Namen für den **Benachrichtigungshub** ein.  
    **Standort:** Wählen Sie einen passenden Standort aus der Dropdownliste aus.  
    **Tarif:** Behalten Sie die Standardoption **Free** bei,  

    > [!NOTE]
    > es sei denn, Sie haben die maximale Anzahl von Hubs im Free-Tarif erreicht.

1. Sobald der **Benachrichtigungshub** bereitgestellt wurde, navigieren Sie zu dieser Ressource.
1. Navigieren Sie zu Ihrem neuen **Benachrichtigungshub**.
1. Wählen Sie die Option **Zugriffsrichtlinien** aus der Liste aus (unter **VERWALTEN**).
1. Notieren Sie sich den Wert von **Richtlinienname** zusammen mit den entsprechenden **Verbindungszeichenfolge**-Werten.
