---
title: Erstellen einer „Hello World“-Web-App für Azure App Service mit Eclipse
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Toolkit für Eclipse eine „Hello World“-Web-App für Azure erstellen.
services: app-service
keywords: Java, Eclipse, Web-App, Azure App Service, Hallo Welt, Hello World, Schnellstart
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 08/25/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 57cb6f2b731ee6d9522787d70efbaf35e029c632
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586148"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Erstellen einer „Hello World“-Web-App für Azure App Service mit Eclipse

In diesem Artikel werden die Schritte beschrieben, die Sie zum Erstellen einer einfachen „Hello World“-Web-App und zum Veröffentlichen Ihrer Web-App für Azure App Service mit dem [Azure-Toolkit für Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) ausführen müssen.

> [!NOTE]
>
> Ein ähnliches Tutorial für IntelliJ IDEA finden Sie [hier][intellij-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Denken Sie daran, die Ressourcen nach Abschluss dieses Tutorials zu bereinigen. In diesem Fall wird Ihr kostenloses Kontokontingent im Rahmen dieses Leitfadens nicht überschritten.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Installation und Anmeldung

Im Folgenden wird Schritt für Schritt beschrieben, wie Sie den Azure-Anmeldeprozess in Ihrer Eclipse-Entwicklungsumgebung durchführen.

1. Falls Sie das Plug-In nicht installiert haben, helfen Ihnen die Informationen unter [Installieren des Azure-Toolkits für Eclipse](installation.md) weiter.

1. Melden Sie sich bei Ihrem Azure-Konto an. Klicken Sie hierzu auf **Tools** (Extras) > **Azure** > **Sign In** (Anmelden).

   :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Anmelden bei Azure in der Eclipse-IDE":::

1. Wählen Sie im Fenster für die **Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](sign-in-instructions.md).

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden.

1. Schließen Sie nach der Anmeldung den Browser, und wechseln Sie zurück zur Eclipse-IDE. Wählen Sie im Dialogfeld **Abonnements auswählen** die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

### <a name="install-required-software-optional"></a>Installieren der erforderlichen Software *(optional)*

Führen Sie die folgenden Schritte aus, um sicherzustellen, dass Sie über die erforderlichen Komponenten für die Arbeit mit Web-App-Projekten verfügen:

1. Klicken Sie auf das Menü **Hilfe** und dann auf **Neue Software installieren**.

1. Klicken Sie im Dialogfeld **Verfügbare Software** auf **Verwalten**, und vergewissern Sie sich, dass die aktuelle Eclipse-Version ausgewählt ist (z. B. *2020-06*).

1. Klicken Sie auf **Anwenden und schließen**. Erweitern Sie das Dropdownmenü *Arbeiten mit:* , um die vorgeschlagenen Websites anzuzeigen. Wählen Sie die aktuelle Version der Eclipse-Website zum Abfragen der verfügbaren Software aus.

1. Scrollen Sie in der Liste nach unten, und wählen Sie den Eintrag **Web, XML, Java EE and OSGi Enterprise Development** (Web, XML, Java EE und OSGi Enterprise Development) aus. Klicken Sie auf **Weiter**.

1. Klicken Sie im Fenster „Installationsdetails“ auf **Weiter**.

1. Lesen Sie im Dialogfeld Review Licenses die Bedingungen der Lizenzverträge. Wenn Sie dem Lizenzvertrag zustimmen, klicken Sie auf **I accept the terms of the license agreements** (Ich akzeptiere die Bedingungen der Lizenzvereinbarungen), und klicken Sie dann auf **Fertig stellen**. 

   > [!NOTE]
   > Sie können den Status der Installation unten rechts in Ihrem Eclipse-Arbeitsbereich überprüfen.

1. Wenn Sie aufgefordert werden, Eclipse zum Abschließen der Installation neu zu starten, klicken Sie auf **Restart Now**.

## <a name="creating-a-web-app-project"></a>Erstellen eines Web-App-Projekts

1. Klicken Sie auf **Datei**, erweitern Sie die Option **Neu**, und klicken Sie anschließend auf **Projekt**. Erweitern Sie im Dialogfeld „Neues Projekt“ die Option **Web**, wählen Sie **Dynamisches Webprojekt** aus, und klicken Sie auf **Weiter**.

   > [!TIP]
   > Falls **Web** nicht als verfügbares Projekt aufgeführt ist, helfen Ihnen die Informationen in [diesem Abschnitt](#install-required-software-optional) weiter. Darin wird beschrieben, wie Sie sicherstellen können, dass Sie über die benötigte Eclipse-Software verfügen.

1. Nennen Sie das Projekt für die Zwecke dieses Tutorials **MyWebApp**. Ihr Bildschirm sieht dann in etwa wie folgt aus:
   
   ![Eigenschaften des neuen dynamischen Webprojekts][dynamic-web-project-properties]

1. Klicken Sie auf **Fertig stellen**.

1. Erweitern Sie im linken Bereich des Paket-Explorers die Option **MyWebApp**. Klicken Sie mit der rechten Maustaste auf **WebContent**, zeigen Sie auf **Neu**, und klicken Sie anschließend auf **Sonstige...** .

1. Erweitern Sie **Web**, um die Option **JSP File** (JSP-Datei) anzuzeigen. Klicken Sie auf **Weiter**.

1. Geben Sie im Dialogfeld **New JSP File** (Neue JSP-Datei) den Namen **index.jsp** für die Datei ein. Behalten Sie den übergeordneten Ordner als **MyWebApp/WebContent** bei, und klicken Sie auf **Next** (Weiter).

   ![Dialogfeld „New JSP File“ (Neue JSP-Datei)][new-jsp-file-dialog]

1. Wählen Sie für dieses Tutorial im Dialogfeld **Select JSP Template** (JSP-Vorlage auswählen) die Option **New JSP File (html 5)** (Neue JSP-Datei (HTML 5)) aus, und klicken Sie dann auf **Finish** (Fertig stellen).

1. Wenn in Eclipse die Datei „index.jsp“ geöffnet wird, geben Sie den Text **Hello World!** ein, damit er dynamisch im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte `<body>` -Inhalt sollte in etwa wie folgt aussehen:
   
   ```jsp
   <body>
   <b><% out.println("Hello World!"); %></b>
   </body>
   ```
1. Speichern Sie die Datei „index.jsp“.

## <a name="deploying-the-web-app-to-azure"></a>Bereitstellen der Web-App in Azure

1. Klicken Sie im linken Bereich des Projekt-Explorers mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Azure** aus. Wählen Sie anschließend **Publish as Azure Web App** (Als Azure-Web-App veröffentlichen) aus.
   
   ![Veröffentlichen als Azure-Web-App][publish-as-azure-web-app]

1. Im Dialogfeld **Web-App bereitstellen** können Sie eine der folgenden Optionen auswählen:

   * Wählen Sie eine vorhandene Web-App aus, sofern vorhanden.

   * Klicken Sie auf **Erstellen**, falls Sie nicht bereits über eine Web-App verfügen.

      Hier können Sie die Laufzeitumgebung, die Ressourcengruppe des App Service-Plans und die App-Einstellungen konfigurieren. Erstellen Sie bei Bedarf neue Ressourcen.

      Geben Sie die erforderlichen Informationen für Ihre Web-App im Dialogfeld **App Service erstellen** an, und klicken Sie dann auf **Erstellen**.

1. Wählen Sie Ihre Web-App aus, und klicken Sie dann auf **Bereitstellen**.

1. Im Toolkit wird auf der Registerkarte **Azure-Aktivitätsprotokoll** der Status **Veröffentlicht** angezeigt, wenn die Web-App bereitgestellt wurde. Der Status ist als Link für die URL der bereitgestellten Web-App formatiert.

1. Sie können über den Link in der Statusmeldung zu Ihrer Web-App navigieren.

   ![Navigieren zur Web-App][browse-web-app]

## <a name="cleaning-up-resources"></a>Bereinigen der Ressourcen

1. Nachdem Sie Ihre Web-App in Azure veröffentlicht haben, können Sie sie verwalten, indem Sie im Azure-Explorer mit der rechten Maustaste darauf klicken und eine der Optionen aus dem Kontextmenü auswählen. Dort können Sie beispielsweise Ihre Web-App **löschen**, um die Ressourcen für dieses Tutorial zu bereinigen.

   ![Verwalten des App-Diensts][manage-app-service]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Weitere Informationen zum Erstellen von Azure-Web-Apps finden Sie unter [Web-Apps – Übersicht].

<!-- URL List -->

[Azure Toolkit for Eclipse]: /azure/developer/java/tookit-for-eclipse
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Web-Apps – Übersicht]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png