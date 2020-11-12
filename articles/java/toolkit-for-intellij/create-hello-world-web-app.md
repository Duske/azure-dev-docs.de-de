---
title: Erstellen einer „Hello World“-Web-App für Azure App Service mit IntelliJ
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Azure-Toolkit für IntelliJ eine „Hello World“-Web-App für Azure erstellen.
services: app-service
keywords: Java, IntelliJ, Web-App, Azure App Service, Hallo Welt, Hello World, Schnellstart
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 09/09/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 992ce6dfd32de9fc20016542b11f2792bf0a8e9b
ms.sourcegitcommit: cbcde17e91e7262a596d813243fd713ce5e97d06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93405849"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>Erstellen einer „Hello World“-Web-App für Azure App Service mit IntelliJ

In diesem Artikel werden die Schritte zum Erstellen einer einfachen „Hello World“-Web-App beschrieben. Außerdem erfahren Sie, wie Sie Ihre Web-App mit dem [Azure-Toolkit für IntelliJ](https://plugins.jetbrains.com/plugin/8053) in Azure App Service veröffentlichen.

> [!NOTE]
>
> Ein ähnliches Tutorial für Eclipse finden Sie [hier][eclipse-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Denken Sie daran, die Ressourcen nach Abschluss dieses Tutorials zu bereinigen. In diesem Fall wird Ihr kostenloses Kontokontingent im Rahmen dieses Leitfadens nicht überschritten.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Installation und Anmeldung

Im Folgenden wird Schritt für Schritt beschrieben, wie Sie den Azure-Anmeldeprozess in Ihrer IntelliJ-Entwicklungsumgebung durchführen.

1. Falls Sie das Plug-In nicht installiert haben, helfen Ihnen die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](./index.yml) weiter.

1. Navigieren Sie zum Anmelden bei Ihrem Azure-Konto zur linken Seitenleiste des **Azure-Explorers** , und klicken Sie anschließend auf das Symbol **Azure-Anmeldung**. Alternativ können Sie auch unter **Extras** die Option **Azure** erweitern und auf **Azure-Anmeldung** klicken.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="Anmelden bei Azure mit IntelliJ"::: 

1. Wählen Sie im Fenster für **die Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](sign-in-instructions.md).

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden.

1. Schließen Sie nach der Anmeldung den Browser, und wechseln Sie zurück zur IntelliJ-IDE. Wählen Sie im Dialogfeld **Abonnements auswählen** die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Auswählen**.

## <a name="creating-a-new-web-app-project"></a>Erstellen eines neuen Web-App-Projekts

1. Klicken Sie auf **Datei** , erweitern Sie die Option **Neu** , und klicken Sie anschließend auf **Projekt**.

1. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Maven** aus, und vergewissern Sie sich, dass die Option **Aus Archetyp erstellen** aktiviert ist. Wählen Sie in der Liste den Eintrag **maven-archetype-webapp** aus, und klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="Auswählen der Option „maven-archetype-webapp“"::: 

1. Erweitern Sie das Dropdownmenü **Artifact Coordinates** (Artefaktkoordinaten), um alle Eingabefelder anzuzeigen, und geben Sie die unten aufgeführten Informationen für Ihre neue Web-App an. Klicken Sie anschließend auf **Weiter** :

   * **Name** : Der Name Ihrer Web-App. Wird automatisch in das Feld **ArtifactId** der App eingefügt.
   * **GroupId** : Der Name der Artefaktgruppe (normalerweise eine Unternehmensdomäne). (Beispiel: *com.microsoft.azure*.)
   * **Version** : Wir behalten die Standardversion *1.0-SNAPSHOT* bei.

1. Passen Sie alle gewünschten Maven-Einstellungen an, oder übernehmen Sie die Standardeinstellungen, und klicken Sie dann auf **Fertig stellen**.

1. Navigieren Sie links auf der Registerkarte **Projekt** zu Ihrem Projekt, und öffnen Sie die Datei **src/main/webapp/index.jsp**. Ersetzen Sie den Code durch Folgendes, und **speichern Sie die Änderungen** :

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="Öffnen der Datei „index.jsp“":::

## <a name="deploying-web-app-to-azure"></a>Bereitstellen der Web-App in Azure

1. Klicken Sie in der Projekt-Explorer-Ansicht mit der rechten Maustaste auf Ihr Projekt, erweitern Sie die Option **Azure** , und klicken Sie anschließend auf **Deploy to Azure Web Apps** (In Azure-Web-Apps bereitstellen).

1. Im Dialogfeld „Deploy to Azure“ (In Azure bereitstellen) können Sie die Anwendung für eine vorhandene Tomcat-Web-App bereitstellen oder eine neue App erstellen.

   a. Klicken Sie auf **No available webapp, click to create a new one** (Keine Web-App verfügbar. Klicken Sie hier, um eine neue zu erstellen.), um eine neue Web-App zu erstellen. Wählen Sie andernfalls im Web-App-Dropdownmenü die Option **Create New WebApp** (Neue Web-App erstellen) aus, falls Ihr Abonnement vorhandene Web-Apps enthält.

      :::image type="content" source="media/create-hello-world-web-app/deploy-to-azure-webapps.png" alt-text="Dialogfeld „Deploy to Azure“ (In Azure bereitstellen)":::

   Geben Sie im Popupdialogfeld **Create WebApp** (Web-App erstellen) die folgenden Informationen an, und klicken Sie auf **OK** : 

      * **Name** : Die Domänennamen-Zeichenfolge der Web-App.
      * **Abonnement** : Hier geben Sie das Azure-Abonnement an, das Sie für die neue Web-App verwenden möchten.
      * **Plattform** : Wählen Sie *Linux* aus.
      * **Webcontainer** : Wählen Sie *TOMCAT 9.0-jre8* oder eine andere geeignete Option aus.
      * **Ressourcengruppe** : Hier geben Sie die Ressourcengruppe für Ihre Web-App an. Sie können eine vorhandene Ressourcengruppe auswählen, die Ihrem Azure-Konto zugeordnet ist, oder eine neue erstellen.
      * **App Service-Plan** : Hier geben Sie den App Service-Plan für Ihre Web-App an. Sie können einen vorhandenen Plan auswählen, der Ihrem Azure-Konto zugeordnet ist, oder einen neuen erstellen.

   b. Um eine vorhandene Web-App bereitzustellen, wählen Sie sie im Web-App-Dropdownmenü aus und klicken dann auf **Ausführen**.

1. Nachdem Ihre Web-App bereitgestellt wurde, wird im Toolkit eine Statusmeldung angezeigt. Diese enthält auch die URL Ihrer bereitgestellten Web-App, sofern die Bereitstellung erfolgreich war.

1. Sie können über den Link in der Statusmeldung zu Ihrer Web-App navigieren.

   :::image type="content" source="media/create-hello-world-web-app/browse-web-app.png" alt-text="Durchsuchen Ihrer Web-App":::

## <a name="managing-deploy-configurations"></a>Verwalten von Bereitstellungskonfigurationen

> [!TIP]
> Nachdem Sie Ihre Web-App veröffentlicht haben, können Sie den Bereitstellungsvorgang ausführen, indem Sie in der Symbolleiste auf den grünen Pfeil klicken.

1. Vor dem Ausführen des Bereitstellungsvorgangs für Ihre Web-App können Sie die Standardeinstellungen ändern, indem Sie auf das Dropdownmenü für Ihre Web-App klicken und die Option **Konfigurationen bearbeiten** auswählen.

   :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="Menü „Konfiguration bearbeiten“":::

1. Im Dialogfeld **Konfigurationen ausführen/debuggen** können Sie die Standardeinstellungen ändern. Klicken Sie auf **OK** , um die Einstellungen zu speichern.

## <a name="cleaning-up-resources"></a>Bereinigen von Ressourcen

1. Navigieren Sie zum Löschen Ihrer Web-App zur linken Seitenleiste des **Azure-Explorers** , und suchen Sie nach der Option **Web-Apps**. 

   > [!NOTE]
   > Falls das Menüelement „Web-Apps“ nicht erweitert wird, müssen Sie die Liste manuell aktualisieren. Klicken Sie hierzu in der Azure-Explorer-Symbolleiste auf das Symbol **Aktualisieren** , oder klicken Sie mit der rechten Maustaste auf das Menüelement „Web-Apps“, und wählen Sie **Aktualisieren** aus.

1. Klicken Sie mit der rechten Maustaste auf die Web-App, die Sie löschen möchten, und klicken Sie anschließend auf **Löschen**.

1. Navigieren Sie zum Löschen Ihres App Service-Plans oder der Ressourcengruppe zum [Azure-Portal](https://portal.azure.com), und löschen Sie die Ressourcen unter Ihrem Abonnement manuell.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Weitere Informationen zum Erstellen von Azure-Web-Apps finden Sie unter [Web-Apps – Übersicht].

<!-- URL List -->

[Azure Toolkit for IntelliJ]: /azure/developer/java/tookit-for-intellij
[Azure Toolkit for Eclipse]: /azure/developer/java/tookit-for-eclipse
[eclipse-hello-world]: ../toolkit-for-eclipse/create-hello-world-web-app.md
[Web-Apps – Übersicht]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[intelliJ-sign-in-instructions]: sign-in-instructions.md

<!-- IMG List -->
[marketplace]:media/create-hello-world-web-app/marketplace.png
[file-new-project]: media/create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: media/create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: media/create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: media/create-hello-world-web-app/maven-options.png
[project-name]: media/create-hello-world-web-app/project-name.png
[open-index-page]: media/create-hello-world-web-app/open-index-page.png
[edit-index-page]: media/create-hello-world-web-app/edit-index-page.png
[deploy-to-azure-menu]: media/create-hello-world-web-app/run-on-web-app-menu.png
[deploy-to-azure-dialog]: media/create-hello-world-web-app/run-on-web-app-dialog.png
[deploy-to-existing-webapp]: media/create-hello-world-web-app/deploy-to-existing-webapp.png
[create-new-web-app-dialog]: media/create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: media/create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: media/create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: media/create-hello-world-web-app/edit-configuration-dialog.png
[clean-resources]: media/create-hello-world-web-app/clean-resource.png