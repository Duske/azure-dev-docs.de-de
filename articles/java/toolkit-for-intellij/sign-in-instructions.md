---
title: Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ
description: Erfahren Sie, wie Sie sich bei Microsoft Azure mit dem Azure-Toolkit für IntelliJ anmelden.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 4447c9d2b09d0d004b8c858aceb31e6c7cee9493
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673986"
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

2. Öffnen Sie über die Seitenleiste den **Azure-Explorer**, und klicken Sie anschließend auf der Leiste am oberen Rand auf das Symbol für die **Azure-Anmeldung**, oder navigieren Sie im IDEA-Menü zu **Tools > Azure > Azure Sign in** (Extras > Azure > Azure-Anmeldung).

   ![IntelliJ-Befehl für Azure-Anmeldung][I01]

3. Wählen Sie im Fenster **Azure Sign In** (Azure-Anmeldung) die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][I02]

4. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

   ![Dialogfeld für Azure-Anmeldung][I03]

5. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

   ![Der Browser für die Geräteanmeldung][I04]

6. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   ![Dialogfeld zum Auswählen von Abonnements][I05]

## <a name="sign-in-to-your-azure-account-by-service-principal"></a>Anmelden bei Ihrem Azure-Konto per Dienstprinzipal

In diesem Abschnitt erfahren Sie, wie Sie eine Datei mit Anmeldeinformationen erstellen, die Ihre Dienstprinzipaldaten enthält. Danach verwendet IntelliJ die Datei mit Anmeldeinformationen, um Sie beim Öffnen Ihres Projekts automatisch bei Azure anzumelden.

1. Öffnen Sie das Projekt in IntelliJ IDEA.

1. Öffnen Sie über die Seitenleiste den **Azure-Explorer**, und klicken Sie anschließend auf der Leiste am oberen Rand auf das Symbol für die **Azure-Anmeldung**, oder navigieren Sie im IDEA-Menü zu **Tools > Azure > Azure Sign in** (Extras > Azure > Azure-Anmeldung).
   ![IntelliJ-Befehl für Azure-Anmeldung][A01]

1. Wählen Sie im Fenster für die **Azure-Anmeldung** die Option **Dienstprinzipal** aus, und klicken Sie dann auf **Neu**.

   ![Das Fenster für die Azure-Anmeldung mit ausgewählter Dienstprinzipaloption][A02]

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

   ![Dialogfeld für Azure-Anmeldung][A03]

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

   ![Der Browser für die Geräteanmeldung][A04]

1. Wählen Sie im Dialogfeld **Create authentication files** (Authentifizierungsdateien erstellen) die gewünschten Abonnements und Ihr Zielverzeichnis aus, und klicken Sie dann auf **Start**.

   ![Fenster zum Erstellen der Authentifizierungsdateien][A05]

1. Klicken Sie im Dialogfeld **Service Principal Creation Status** (Dienstprinzipal-Erstellungsstatus) nach erfolgreicher Erstellung Ihrer Dateien auf **OK**.

   ![Dialogfeld für den Status der Dienstprinzipalerstellung][A06]

1. Klicken Sie im Fenster **Azure Sign In** (Azure-Anmeldung) auf **Sign in** (Anmelden). 

   ![Dialogfeld für Azure-Anmeldung][A07]

1. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   ![Dialogfeld zum Auswählen von Abonnements][A08]

> Nachdem Sie die Datei zur Dienstprinzipalauthentifizierung erstellt haben, können Sie mit Schritt 8 beginnen, Ihre Authentifizierungsdatei auswählen und sich anmelden.

## <a name="sign-out-of-your-azure-account"></a>Abmelden von Ihrem Azure-Abonnement

Nachdem Sie Ihr Konto mithilfe der obigen Schritte konfiguriert haben, werden Sie bei jedem Start von IntelliJ IDEA automatisch angemeldet. Mit den folgenden Schritten können Sie sich bei Bedarf von Ihrem Azure-Konto abmelden.

* Öffnen Sie in IntelliJ IDEA die Seitenleiste von Azure Explorer, klicken Sie auf das Symbol für **Azure-Abmeldung**, und bestätigen Sie (oder im IDEA-Menü **Tools > Azure > Azure Sign Out** (Extras > Azure > Azure-Abmeldung)).

   ![IntelliJ-Befehl für Azure-Abmeldung][L01]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- IMG List -->

[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[A01]: media/sign-in-instructions/A01.png
[A02]: media/sign-in-instructions/A02.png
[A03]: media/sign-in-instructions/A03.png
[A04]: media/sign-in-instructions/A04.png
[A05]: media/sign-in-instructions/A05.png
[A06]: media/sign-in-instructions/A06.png
[A07]: media/sign-in-instructions/A07.png
[A08]: media/sign-in-instructions/A08.png
[A09]: media/sign-in-instructions/A09.png

[L01]: media/sign-in-instructions/L01.png
[L02]: media/sign-in-instructions/L02.png
[L03]: media/sign-in-instructions/L03.png
