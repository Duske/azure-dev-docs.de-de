---
title: Erstellen einer „Hello World“-Web-App für Azure App Service mit Eclipse
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Toolkit für Eclipse eine „Hello World“-Web-App für Azure erstellen.
services: app-service
keywords: Java, Eclipse, Web-App, Azure App Service, Hallo Welt, Hello World, Schnellstart
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 25c46576e6c1c379d10cd5ce350ca73c5e28ab93
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81671016"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Erstellen einer „Hello World“-Web-App für Azure App Service mit Eclipse

Mithilfe des Open-Source-Plug-Ins [Azure-Toolkit für Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) können Sie innerhalb weniger Minuten eine einfache „Hello World“-Anwendung erstellen und als Web-App in Azure App Service bereitstellen.

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

1. Ziehen Sie die folgende Schaltfläche in Ihren aktiven Eclipse-Arbeitsbereich, um das Plug-In „Azure-Toolkit für Eclipse“ zu installieren. Weitere Installationsoptionen finden Sie [hier](installation.md).

    [![Ziehen Sie dieses Element in Ihren aktiven Eclipse*-Arbeitsbereich. *Eclipse Marketplace-Client erforderlich](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Ziehen Sie dieses Element in Ihren aktiven Eclipse*-Arbeitsbereich. *Eclipse Marketplace-Client erforderlich")

1. Melden Sie sich bei Ihrem Azure-Konto an. Klicken Sie hierzu auf **Tools** (Extras) > **Azure** > **Sign In** (Anmelden).
   ![Eclipse-Menü für die Anmeldung bei Azure][I01]

1. Wählen Sie im Fenster für die **Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](sign-in-instructions.md).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][I02]

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

   ![Dialogfeld für Azure-Anmeldung][I03]

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

   ![Der Browser für die Geräteanmeldung][I04]

1. Wenn schließlich das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   ![Dialogfeld zum Auswählen von Abonnements][I05]

## <a name="creating-web-app-project"></a>Erstellen des Web-App-Projekts

1. Klicken Sie auf **Datei**, dann auf **Neu** und schließlich auf **Dynamisches Webprojekt**. (Wenn **Dynamic Web Project** (Dynamisches Webprojekt) nach dem Klicken auf **File** (Datei) und **New** (Neu) nicht als verfügbares Projekt aufgeführt ist, gehen Sie wie folgt vor: Klicken Sie auf **File** (Datei), anschließend auf **New** (Neu) und dann auf **Project...** (Projekt...). Erweitern Sie die Option **Web**, klicken Sie auf **Dynamic Web Project** (Dynamisches Webprojekt) und dann auf **Next** (Weiter).)

   ![Erstellen eines neuen dynamischen Webprojekts][file-new-dynamic-web-project]

2. Nennen Sie das Projekt für die Zwecke dieses Tutorials **MyWebApp**. Ihr Bildschirm sieht dann in etwa wie folgt aus:
   
   ![Eigenschaften des neuen dynamischen Webprojekts][dynamic-web-project-properties]

3. Klicken Sie auf **Fertig stellen**.

4. Erweitern Sie in der Project Explorer-Ansicht von Eclipse die Option **MyWebApp**. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu** sowie auf **JSP-Datei**.

   ![Erstellen einer neuen JSP-Datei][create-new-jsp-file]

5. Geben Sie im Dialogfeld **New JSP File** (Neue JSP-Datei) den Namen **index.jsp** für die Datei ein. Behalten Sie den übergeordneten Ordner als **MyWebApp/WebContent** bei, und klicken Sie auf **Next** (Weiter).

   ![Dialogfeld „New JSP File“ (Neue JSP-Datei)][new-jsp-file-dialog]

6. Wählen Sie im Dialogfeld **Select JSP Template** (JSP-Vorlage auswählen) im Rahmen dieses Tutorials **New JSP File (html)** (Neue JSP-Datei (HTML)) aus, und klicken Sie dann auf **Finish** (Fertig stellen).

   ![Auswählen einer JSP-Vorlage][select-jsp-template]

7. Wenn in Eclipse die Datei „index.jsp“ geöffnet wird, geben Sie den Text **Hello World!** ein, damit er dynamisch im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte `<body>` -Inhalt sollte in etwa wie folgt aussehen:
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. Speichern Sie die Datei „index.jsp“.

## <a name="deploying-web-app-to-azure"></a>Bereitstellen der Web-App in Azure

1. Klicken Sie in der Projekt-Explorer-Ansicht von Eclipse mit der rechten Maustaste auf das Projekt, wählen Sie **Azure** aus, und wählen Sie dann **Publish as Azure Web App** (Als Azure-Web-App veröffentlichen) aus.
   
   ![Veröffentlichen als Azure-Web-App][publish-as-azure-web-app]

1. Im Dialogfeld **Web-App bereitstellen** können Sie eine der folgenden Optionen auswählen:

   * Wählen Sie eine vorhandene Web-App aus, sofern vorhanden.

      ![Auswählen eines App-Diensts][select-app-service]

   * Klicken Sie auf **Create New Web App** (Neue Web-App erstellen).

      ![Erstellen eines App Service][create-app-service]

      Geben Sie die erforderlichen Informationen für Ihre Web-App im Dialogfeld **App Service erstellen** an, und klicken Sie dann auf **Erstellen**.

      Hier können Sie die Runtime-Umgebung, App-Einstellungen, den Dienstplan und die Ressourcengruppe konfigurieren.

      ![Dialogfeld „App Service erstellen“][create-app-service-dialog]

1. Wählen Sie Ihre Web-App aus, und klicken Sie dann auf **Bereitstellen**.

   ![Bereitstellen eines App-Diensts][deploy-app-service]

1. Im Toolkit wird auf der Registerkarte **Azure-Aktivitätsprotokoll** der Status **Veröffentlicht** angezeigt, wenn die Web-App bereitgestellt wurde. Der Status ist als Link für die URL der bereitgestellten Web-App formatiert.

   ![Veröffentlichungsstatus][publish-status]

1. Sie können über den Link in der Statusmeldung zu Ihrer Web-App navigieren.

   ![Navigieren zur Web-App][browse-web-app]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="cleaning-up-resources"></a>Bereinigen der Ressourcen

1. Nachdem Sie Ihre Web-App in Azure veröffentlicht haben, können Sie sie verwalten, indem Sie im Azure-Explorer mit der rechten Maustaste darauf klicken und eine der Optionen aus dem Kontextmenü auswählen. Dort können Sie beispielsweise Ihre Web-App **löschen**, um die Ressourcen für dieses Tutorial zu bereinigen.

   ![Verwalten des App-Diensts][manage-app-service]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Weitere Informationen zum Erstellen von Azure-Web-Apps finden Sie unter [Web-Apps – Übersicht].

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Web-Apps – Übersicht]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md

<!-- IMG List -->
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[file-new-dynamic-web-project]: media/create-hello-world-web-app/file-new-dynamic-web-project.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[create-new-jsp-file]: media/create-hello-world-web-app/create-new-jsp-file.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[select-jsp-template]: media/create-hello-world-web-app/select-jsp-template.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[deploy-web-app-dialog]: media/create-hello-world-web-app/deploy-web-app-dialog.png
[select-app-service]: media/create-hello-world-web-app/select-app-service.png
[create-app-service-dialog]: media/create-hello-world-web-app/create-app-service-dialog.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[create-app-service]: media/create-hello-world-web-app/create-app-service.png
[deploy-app-service]: media/create-hello-world-web-app/deploy-app-service.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png
