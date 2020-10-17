---
title: Anleitung zur Anmeldung für das Azure-Toolkit für Eclipse
description: Erfahren Sie, wie Sie sich bei Microsoft Azure mit dem Azure-Toolkit für Eclipse anmelden.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: d9e3b0de0c6ea20996c54b6ab5cb03083ed25a65
ms.sourcegitcommit: d5dabc6dde727ed167a9dc8a4eaaf21025b3efa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2020
ms.locfileid: "91947545"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-eclipse"></a>Anleitung zur Anmeldung für das Azure-Toolkit für Eclipse

Das Azure-Toolkit für Eclipse bietet zwei Methoden für die Anmeldung bei Ihrem Azure-Konto:

  - [Anmelden bei Ihrem Azure-Konto per Geräteanmeldung](#sign-in-to-your-azure-account-by-device-login)
  - [Anmelden bei Ihrem Azure-Konto per Dienstprinzipal](#sign-in-to-your-azure-account-by-service-principal)

Auch [**Abmeldemethoden**](#sign-out-of-your-azure-account) stehen zur Verfügung.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Anmelden bei Ihrem Azure-Konto per Geräteanmeldung

In diesem Abschnitt wird der Azure-Anmeldeprozess nach Geräteanmeldung erläutert.

1. Öffnen Sie das Projekt in Eclipse.

1. Klicken Sie auf **Extras**, anschließend auf **Azure** und dann auf **Anmelden**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Anmelden bei Azure in der Eclipse-IDE":::

1. Wählen Sie im Fenster **Azure Sign In** (Azure-Anmeldung) die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][I02]

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

> [!NOTE]
>
> Sollte der Browser nicht geöffnet werden, konfigurieren Sie Eclipse für die Verwendung eines externen Browsers wie Internet Explorer, Firefox oder Chrome:
>
> 1. Öffnen Sie „Preferences“ (Voreinstellungen) > „General“ (Allgemein) > „Web Browser“ (Webbrowser) > „Use external web browser in Eclipse“ (Externen Webbrowser in Eclipse verwenden).
>
> 2. Wählen Sie den gewünschten Browser aus.
>

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden.

1. Schließen Sie nach der Anmeldung den Browser, und wechseln Sie zurück zur Eclipse-IDE. Wählen Sie im Dialogfeld **Abonnements auswählen** die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Anmelden bei Ihrem Azure-Konto per Dienstprinzipal

In diesem Abschnitt erfahren Sie, wie Sie eine Datei mit Anmeldeinformationen erstellen, die Ihre Dienstprinzipaldaten enthält. Danach verwendet Eclipse die Datei mit Anmeldeinformationen, um Sie beim Öffnen Ihres Projekts automatisch bei Azure anzumelden.

1. Öffnen Sie das Projekt in Eclipse.

2. Klicken Sie auf **Extras**, anschließend auf **Azure** und dann auf **Anmelden**.

      :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Anmelden bei Azure in der Eclipse-IDE":::

3. In der **Azure Sign In** wählen Sie im Fenster **Dienstprinzipal**. Sollten Sie noch nicht über die Datei für die Dienstprinzipalauthentifizierung verfügen, klicken Sie auf **New** (Neu), um eine zu erstellen. Andernfalls können Sie auf **Browse** (Durchsuchen) klicken und direkt mit Schritt 8 fortfahren.

   ![Das Fenster für die Azure-Anmeldung mit ausgewählter Dienstprinzipaloption][A02]

4. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

> [!NOTE]
>
> Sollte der Browser nicht geöffnet werden, konfigurieren Sie Eclipse für die Verwendung eines externen Browsers wie Internet Explorer oder Chrome:
>
> 1. Öffnen Sie „Preferences“ (Voreinstellungen) > „General“ (Allgemein) > „Web Browser“ (Webbrowser) > „Use external web browser in Eclipse“ (Externen Webbrowser in Eclipse verwenden).
>
> 2. Wählen Sie den gewünschten Browser aus.
>

5. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

6. Wählen Sie im Dialogfeld **Create authentication files** (Authentifizierungsdateien erstellen) die gewünschten Abonnements und Ihr Zielverzeichnis aus, und klicken Sie dann auf **Start**.

7. Klicken Sie im Dialogfeld **Service Principal Creation Status** (Dienstprinzipal-Erstellungsstatus) nach erfolgreicher Erstellung Ihrer Dateien auf **OK**.

8. Die Adresse der erstellten Datei wird im Fenster für die **Azure-Anmeldung** automatisch ausgefüllt. Klicken Sie als Nächstes auf **Sign in** (Anmelden).

   ![Dialogfeld für Azure-Anmeldung][A06]

9. Wenn schließlich das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.


## <a name="sign-out-of-your-azure-account"></a>Abmelden von Ihrem Azure-Abonnement

Nachdem Sie Ihr Konto mithilfe der obigen Schritte konfiguriert haben, werden Sie bei jedem Start von Eclipse automatisch angemeldet. Mit den folgenden Schritten können Sie sich bei Bedarf von Ihrem Azure-Konto abmelden:

1. Klicken Sie auf **Extras**, anschließend auf **Azure** und dann auf **Abmelden**.

2. Wenn das Dialogfeld **Azure Sign Out** angezeigt wird, klicken Sie auf **Yes**.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->


<!-- IMG List -->

[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png
[A06]: media/sign-in-instructions/A06.png
