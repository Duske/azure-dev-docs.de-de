---
title: Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ
description: Erfahren Sie, wie Sie sich bei Microsoft Azure mit dem Azure-Toolkit für IntelliJ anmelden.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 2891b0c09c43b652fd7dd41e354290c2821bad46
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534563"
---
# <a name="sign-in-instructions-for-the-azure-toolkit-for-intellij"></a>Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ

Sobald es [installiert](https://www.jetbrains.com/help/idea/managing-plugins.html) ist, bietet das [Azure-Toolkit für IntelliJ](https://plugins.jetbrains.com/plugin/8053) zwei Methoden für die Anmeldung bei Ihrem Azure-Konto:

  - [Anmelden bei Ihrem Azure-Konto per Geräteanmeldung](#sign-in-to-your-azure-account-by-device-login)
  - [Anmelden bei Ihrem Azure-Konto per Dienstprinzipal](#sign-in-to-your-azure-account-by-service-principal)

Auch [**Abmeldemethoden**](#sign-out-of-your-azure-account) stehen zur Verfügung.

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="sign-in-to-your-azure-account-by-device-login"></a>Anmelden bei Ihrem Azure-Konto per Geräteanmeldung

Gehen Sie wie folgt vor, um sich per Geräteanmeldung bei Azure anzumelden:

1. Öffnen Sie das Projekt in IntelliJ IDEA.

1. Öffnen Sie über die Seitenleiste den **Azure-Explorer**, und klicken Sie anschließend auf der Leiste am oberen Rand auf das Symbol **Azure-Anmeldung** (oder navigieren Sie im IntelliJ-Menü zu **Tools > Azure > Azure Sign in** (Extras > Azure > Azure-Anmeldung)).

   ![IntelliJ-Befehl für Azure-Anmeldung][I01]

1. Wählen Sie im Fenster **Azure Sign In** (Azure-Anmeldung) die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][I02]

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden.

1. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.


## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Anmelden bei Ihrem Azure-Konto per Dienstprinzipal

In diesem Abschnitt erfahren Sie, wie Sie eine Datei mit Anmeldeinformationen erstellen, die Ihre Dienstprinzipaldaten enthält. Danach verwendet IntelliJ die Datei mit Anmeldeinformationen, um Sie beim Öffnen Ihres Projekts automatisch bei Azure anzumelden.

1. Öffnen Sie das Projekt in IntelliJ IDEA.

1. Öffnen Sie über die Seitenleiste den **Azure-Explorer**, und klicken Sie anschließend auf der Leiste am oberen Rand auf das Symbol **Azure-Anmeldung** (oder navigieren Sie im IntelliJ-Menü zu **Tools > Azure > Azure Sign in** (Extras > Azure > Azure-Anmeldung)).

   ![IntelliJ-Befehl für Azure-Anmeldung][I01]

1. Wählen Sie im Fenster für die **Azure-Anmeldung** die Option **Dienstprinzipal** aus, und klicken Sie dann auf **Neu**.

   ![Das Fenster für die Azure-Anmeldung mit ausgewählter Dienstprinzipaloption][A02]

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden. Schließen Sie den Browser nach Abschluss der Authentifizierung, und wechseln Sie zurück zu IntelliJ.

1. Wählen Sie im Dialogfeld **Create authentication files** (Authentifizierungsdateien erstellen) die gewünschten Abonnements und Ihr Zielverzeichnis aus, und klicken Sie dann auf **Start**.

1. Klicken Sie im Dialogfeld **Service Principal Creation Status** (Dienstprinzipal-Erstellungsstatus) nach erfolgreicher Erstellung Ihrer Dateien auf **OK**.

1. Klicken Sie im Fenster **Azure Sign In** (Azure-Anmeldung) auf **Sign in** (Anmelden). 

1. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   > [!TIP]
   > Nachdem Sie die Datei für die Dienstprinzipalauthentifizierung erstellt haben, können Sie mit Schritt 3 beginnen, Ihre Authentifizierungsdatei auswählen und sich anmelden.

## <a name="sign-out-of-your-azure-account"></a>Abmelden von Ihrem Azure-Abonnement

Nachdem Sie Ihr Konto mithilfe der obigen Schritte konfiguriert haben, werden Sie bei jedem Start von IntelliJ IDEA automatisch angemeldet. 

Falls Sie sich aber von Ihrem Azure-Konto abmelden möchten, müssen Sie zur Seitenleiste des Azure-Explorers navigieren und auf das Symbol **Azure-Abmeldung** klicken oder im IntelliJ-Menü zu **Tools > Azure > Azure Sign Out** (Extras > Azure > Azure-Abmeldung) navigieren.


## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png

[A02]: media/sign-in-instructions/A02.png

