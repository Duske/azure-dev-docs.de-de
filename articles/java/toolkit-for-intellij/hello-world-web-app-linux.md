---
title: Bereitstellen einer Hallo Welt-Web-App in einem Linux-Container
titleSuffix: Azure Toolkit for IntelliJ
description: Führen Sie eine einfache Web-App vom Typ „Hallo Welt“ in einem Linux-Container aus, und stellen Sie sie mithilfe des Azure-Toolkits für IntelliJ in der Cloud bereit.
services: app-service\web
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 0dd1b0202442a4af5322513f038ddcc9908a7dd1
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534707"
---
# <a name="deploy-java-app-to-azure-web-apps-for-containers-using-azure-toolkit-for-intellij"></a>Bereitstellen einer Java-App in Azure-Web-Apps für Container mithilfe des Azure-Toolkits für IntelliJ

[Docker]-Container sind eine weit verbreitete Methode zum Bereitstellen von Webanwendungen. Mithilfe von Docker-Containern können Entwickler ihre Projektdateien und Abhängigkeiten für die Bereitstellung auf einem Server in einem einzelnen Paket zusammenfassen. Mit dem Azure-Toolkit für IntelliJ wird dieser Prozess für Java-Entwickler vereinfacht, indem Features für die Bereitstellung von Containern in Microsoft Azure hinzugefügt werden.

In diesem Artikel werden die Schritte zum Erstellen einer einfachen „Hello World“-Web-App beschrieben. Außerdem erfahren Sie, wie Sie Ihre Web-App mithilfe des Azure-Toolkits für IntelliJ in einem Linux-Container in Azure veröffentlichen.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* Einen [Docker]-Client

> [!NOTE]
>
> Sie müssen für die Schritte in diesem Tutorial [Docker] so konfigurieren, dass der Daemon ohne TLS an Port 2375 verfügbar gemacht wird. Sie können diese Einstellung beim Installieren von Docker oder über das Docker-Einstellungsmenü konfigurieren.
>
> ![Docker-Einstellungsmenü][docker-settings-menu]
>

## <a name="installation-and-sign-in"></a>Installation und Anmeldung

Im Folgenden wird Schritt für Schritt beschrieben, wie Sie den Azure-Anmeldeprozess in Ihrer IntelliJ-Entwicklungsumgebung durchführen.

1. Falls Sie das Plug-In nicht installiert haben, helfen Ihnen die Informationen unter [Installieren des Azure-Toolkits für IntelliJ](installation.md) weiter.

1. Navigieren Sie zum Anmelden bei Ihrem Azure-Konto zur linken Seitenleiste des **Azure-Explorers**, und klicken Sie anschließend auf das Symbol **Azure-Anmeldung**. Alternativ können Sie auch unter **Extras** die Option **Azure** erweitern und auf **Azure-Anmeldung** klicken.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="Anmelden bei Azure mit IntelliJ"::: 

1. Wählen Sie im Fenster für **die Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](sign-in-instructions.md).

1. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

1. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

1. Wählen Sie Ihr Azure-Konto aus, und führen Sie alle erforderlichen Authentifizierungsschritte aus, um sich anzumelden.

1. Schließen Sie nach der Anmeldung den Browser, und wechseln Sie zurück zur IntelliJ-IDE. Wählen Sie im Dialogfeld **Abonnements auswählen** die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

## <a name="creating-a-new-web-app-project"></a>Erstellen eines neuen Web-App-Projekts

1. Klicken Sie auf **Datei**, erweitern Sie die Option **Neu**, und klicken Sie anschließend auf **Projekt**.

1. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Maven** aus, und vergewissern Sie sich, dass die Option **Aus Archetyp erstellen** aktiviert ist. Wählen Sie in der Liste den Eintrag **maven-archetype-webapp** aus, und klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="Auswählen der Option „maven-archetype-webapp“"::: 

1. Erweitern Sie das Dropdownmenü **Artifact Coordinates** (Artefaktkoordinaten), um alle Eingabefelder anzuzeigen, und geben Sie die unten aufgeführten Informationen für Ihre neue Web-App an. Klicken Sie anschließend auf **Weiter**:

   * **Name**: Der Name Ihrer Web-App. Wird automatisch in das Feld **ArtifactId** der App eingefügt.
   * **GroupId**: Der Name der Artefaktgruppe (normalerweise eine Unternehmensdomäne). (Beispiel: *com.microsoft.azure*.)
   * **Version**: Wir behalten die Standardversion *1.0-SNAPSHOT* bei.

1. Passen Sie alle gewünschten Maven-Einstellungen an, oder übernehmen Sie die Standardeinstellungen, und klicken Sie dann auf **Fertig stellen**.

1. Navigieren Sie links auf der Registerkarte **Projekt** zu Ihrem Projekt, und öffnen Sie die Datei **src/main/webapp/WEB-INF/index.jsp**. Ersetzen Sie den Code durch Folgendes, und **speichern Sie die Änderungen**:

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="Öffnen der Datei „index.jsp“":::

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Erstellen einer Azure-Containerregistrierung, die als private Docker-Registrierung verwendet werden soll

Die folgende Anleitung führt Sie durch die Verwendung des Azure-Portals zur Erstellung einer Azure-Containerregistrierung.

> [!NOTE]
>
> Wenn Sie statt des Azure-Portals die Azure CLI verwenden möchten, führen Sie die Schritte unter [Erstellen einer privaten Docker-Containerregistrierung mit Azure-CLI-2.0][Create Docker Registry using Azure CLI] aus.
>

1. Navigieren Sie zum [Azure portal], und melden Sie sich an.

   Nachdem Sie sich im Azure-Portal bei Ihrem Konto angemeldet haben, können Sie die Schritte im Artikel [Erstellen einer privaten Docker-Containerregistrierung im Azure-Portal] ausführen, die im Folgenden aus Gründen der Zweckmäßigkeit umschrieben werden.

1. Klicken Sie auf das Menüsymbol für **+ Ressource erstellen**, die Kategorie **Container** und dann auf **Containerregistrierung**.

1. Geben Sie die folgenden Informationen ein, wenn die Seite **Containerregistrierung erstellen** angezeigt wird:

   * **Abonnement**: Das Azure-Abonnement, das Sie für die neue Containerregistrierung verwenden möchten.

   * **Ressourcengruppe**: Die Ressourcengruppe für Ihre Containerregistrierung. Wählen Sie eine der folgenden Optionen aus:
      * **Neue erstellen**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden**: Gibt an, dass Sie in einer Liste von Ressourcengruppen, die Ihrem Azure-Konto zugeordnet sind, eine Auswahl treffen möchten.

   * **Registrierungsname**: Der Name für die neue Containerregistrierung.

   * **Standort**: Gibt die Region an, in der Ihre Containerregistrierung erstellt wird (z. B. „USA, Westen“).

   * **SKU**: Die Dienstebene für Ihre Containerregistrierung. Wählen Sie für dieses Tutorial die Option *Basic* aus. Weitere Informationen finden Sie unter [Azure Container Registry-Tarife](/azure/container-registry/container-registry-skus).

1. Klicken Sie auf **Bewerten + erstellen**, und überprüfen Sie, ob die Informationen korrekt sind. Klicken Sie abschließend auf **Erstellen**.

## <a name="deploy-your-web-app-in-a-docker-container"></a>Bereitstellen Ihrer Web-App in einem Docker-Container

Mit den folgenden Schritten wird die Konfiguration der Docker-Unterstützung für Ihre Web-App und die Bereitstellung der Web-App beschrieben.

1. Navigieren Sie auf der linken Registerkarte **Projekt** zu Ihrem Projekt, und klicken Sie mit der rechten Maustaste darauf. Erweitern Sie **Azure**, und klicken Sie auf **Add Docker Support** (Docker-Unterstützung hinzufügen).

   Damit wird automatisch eine Docker-Datei mit einer Standardkonfiguration erstellt.

   :::image type="content" source="media/hello-world-web-app-linux/docker-support-file.png" alt-text="Datei für Docker-Unterstützung":::

1. Nachdem Sie die Docker-Unterstützung hinzugefügt haben, können Sie wie folgt vorgehen: Klicken Sie im Projekt-Explorer mit der rechten Maustaste auf Ihr Projekt, erweitern Sie die Option **Azure**, und klicken Sie dann auf **Run on Web App for Containers** (In Web-App für Container ausführen).

1. Geben Sie im Dialogfeld **Run on Web App for Containers** (In Web-App für Container ausführen) die folgenden Informationen ein:

   * **Name**: Gibt den Anzeigenamen an, der im Azure-Toolkit angezeigt wird. 

   * **Containerregistrierung**: Wählen Sie im Dropdownmenü die Containerregistrierung aus, die Sie im vorherigen Abschnitt dieses Artikels erstellt haben. Die Felder für **Server-URL**, **Benutzername** und **Kennwort** werden automatisch ausgefüllt.

   * **Image und Tag**: Gibt den Namen des Containerimages an, wobei in der Regel die folgende Syntax verwendet wird: „*Registrierung*.azurecr.io/*App-Name*:latest“. Hierbei gilt Folgendes: 
      * *Registrierung* ist Ihre Containerregistrierung aus dem vorherigen Abschnitt dieses Artikels. 
      * *App-Name* ist der Name Ihrer Web-App. 

   * **Use Existing Web App** (Vorhandene Web-App verwenden) oder **Create New Web App** (Neue Web-App erstellen): Gibt an, ob Sie Ihren Container in einer vorhandenen Web-App bereitstellen oder eine neue Web-App erstellen. Mit dem von Ihnen angegebenen **App-Namen** wird die URL für Ihre Web-App erstellt, z. B. *wingtiptoys.azurewebsites.net*.

   * **Ressourcengruppe**: Gibt an, ob Sie eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen möchten. 

   * **App Service-Plan**: Gibt an, ob Sie einen vorhandenen App Service-Plan verwenden oder einen neuen erstellen möchten. 

1. Wenn Sie mit dem Konfigurieren der oben aufgeführten Einstellungen fertig sind, klicken Sie auf **Ausführen**. Nachdem Ihre Web-App erfolgreich bereitgestellt wurde, wird der Status im Fenster **Ausführen** angezeigt.

1. Nach der Veröffentlichung Ihrer Web-App können Sie zu der URL navigieren, die für Ihre Web-App zuvor angegeben wurde, z. B. *wingtiptoys.azurewebsites.net*.

   ![Navigieren zu Ihrer Web-App][browsing-to-web-app]

## <a name="optional-modify-your-web-app-publish-settings"></a>Optional: Ändern der Veröffentlichungseinstellungen für die Web-App

1. Nachdem Sie Ihre Web-App veröffentlicht haben, werden die Einstellungen als Standard gespeichert. Sie können Ihre Anwendung in Azure ausführen, indem Sie auf der Symbolleiste auf den grünen Pfeil klicken. Sie können diese Einstellungen ändern, indem Sie auf das Dropdownmenü für Ihre Web-App und dann auf **Konfigurationen bearbeiten** klicken.

    :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="Menü „Konfiguration bearbeiten“":::

1. Wenn das Dialogfeld für die **Ausführungs-/Debugkonfigurationen** angezeigt wird, können Sie beliebige Standardeinstellungen ändern. Klicken Sie anschließend auf **OK**.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliche Ressourcen für Docker finden Sie auf der offiziellen [Docker-Website][Docker].

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Azure portal]: https://portal.azure.com/
[Erstellen einer privaten Docker-Containerregistrierung im Azure-Portal]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: /azure/developer/java
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png