---
title: Bereitstellen einer Spring Boot-Web-App für Linux in Azure App Service
description: In diesem Tutorial werden die Schritte zum Bereitstellen einer Spring Boot-Anwendung als Linux-Webanwendung in Microsoft Azure erläutert.
services: azure app service
documentationcenter: java
ms.date: 12/31/2019
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc, devx-track-java
ms.openlocfilehash: f6f90deebd214d0381f3a66c6808dbc4f848aa13
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725144"
---
# <a name="deploy-a-spring-boot-application-to-linux-on-azure-app-service"></a>Bereitstellen einer Spring Boot-Anwendung für Linux in Azure App Service

In diesem Tutorial wird die Verwendung von [Docker] zum Packen Ihrer [Spring Boot]-Anwendung in Container und zum Bereitstellen Ihres eigenen Docker-Images für einen Linux-Host in [Azure App Service](/azure/app-service/containers/app-service-linux-intro) erläutert.

## <a name="prerequisites"></a>Voraussetzungen

Zur Durchführung der Schritte in diesem Tutorial benötigen Sie Folgendes:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Die [Azure-Befehlszeilenschnittstelle (CLI)]
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* Das Erstellungstool Apache [Maven] (Version 3)
* Einen [Git-Client]
* Einen [Docker]-Client

> [!NOTE]
>
> Aufgrund der Virtualisierungsanforderungen dieses Tutorials können Sie die Schritte in diesem Artikel nicht auf einem virtuellen Computer ausführen; Sie müssen einen physischen Computer mit aktivierten Virtualisierungsfunktionen verwenden.

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Erstellen der Web-App für erste Schritte mit Spring Boot in Docker

Die folgende Anleitung führt Sie durch die erforderlichen Schritte für das Erstellen einer einfachen Spring Boot-Webanwendung und das lokale Testen.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie ein lokales Verzeichnis zum Speichern Ihrer Anwendung, und wechseln Sie in dieses Verzeichnis. Beispiel:

   ```bash
   mkdir SpringBoot
   cd SpringBoot
   ```

1. Klonen Sie das Beispielprojekt [Spring Boot on Docker Getting Started] in das Verzeichnis, das Sie gerade erstellt haben. Beispiel:

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt. Beispiel:

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Erstellen Sie die JAR-Datei mit Maven. Beispiel:

   ```bash
   mvn package
   ```

1. Wechseln Sie nach dem Erstellen der Web-App in das Verzeichnis `target` mit der JAR-Datei, und starten Sie die Web-App. Beispiel:

   ```bash
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. Testen Sie die Web-App, indem Sie sie lokal mit einem Webbrowser durchsuchen. Wenn beispielsweise cURL verfügbar ist und Sie den Tomcat-Server so konfiguriert haben, dass er an Port 80 ausgeführt wird:

   ```bash
   curl http://localhost
   ```

1. Die folgende Meldung sollte angezeigt werden: **Hello Docker World!**

   ![Lokales Durchsuchen von Beispiel-Apps][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Erstellen einer Azure-Containerregistrierung, die als private Docker-Registrierung verwendet werden soll

Die folgende Anleitung führt Sie durch die Verwendung des Azure-Portals zur Erstellung einer Azure Container Registry-Instanz.

> [!NOTE]
>
> Wenn Sie statt des Azure-Portals die Azure CLI verwenden möchten, führen Sie die Schritte unter [Erstellen einer privaten Docker-Containerregistrierung mit Azure-CLI-2.0](/azure/container-registry/container-registry-get-started-azure-cli) aus.

1. Navigieren Sie zum [Azure portal], und melden Sie sich an.

   Nachdem Sie sich im Azure-Portal bei Ihrem Konto angemeldet haben, führen Sie die Schritte im Artikel [Erstellen einer privaten Docker-Containerregistrierung im Azure-Portal] aus, die im Folgenden aus Gründen der Zweckmäßigkeit umschrieben werden.

1. Klicken Sie auf das Menüsymbol für **+ Neu**, auf **Container** und anschließend auf **Azure Container Registry**.

   ![Erstellen einer neuen Azure-Containerregistrierung][AR01]

1. Wenn die Seite **Containerregistrierung erstellen** angezeigt wird, geben Sie Werte für **Registrierungsname**, **Abonnement**, **Ressourcengruppe** und **Standort** ein. Klicken Sie dann auf **Erstellen**.

   ![Konfigurieren der Einstellungen für die Azure-Containerregistrierung][AR03]

## <a name="configure-maven-to-build-image-to-your-azure-container-registry"></a>Konfigurieren von Maven für die Erstellung eines Images für Ihre Azure Container Registry-Instanz

1. Navigieren Sie zu dem abgeschlossenen Projektverzeichnis für Ihre Spring Boot-Anwendung (z. B. „*C:\SpringBoot\gs-spring-boot-docker\complete*“ oder „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*“), und öffnen Sie die Datei *pom.xml* mit einem Texteditor.

1. Aktualisieren Sie die Auflistung `<properties>` in der Datei *pom.xml* mit der aktuellen Version von [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin), dem Wert des Anmeldeservers und den Zugriffseinstellungen für Ihre Azure Container Registry-Instanz aus dem vorherigen Abschnitt dieses Tutorials. Beispiel:

   ```xml
   <properties>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Fügen Sie [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) der Sammlung `<plugins>` in der Datei *pom.xml* hinzu.  In diesem Beispiel wird Version 2.2.0 verwendet.

   Geben Sie das Basisimage (in diesem Fall `mcr.microsoft.com/java/jre:8-zulu-alpine`) unter `<from>/<image>` an. Geben Sie den Namen des endgültigen Images, das auf der Grundlage des Basisimages erstellt werden soll, unter `<to>/<image>` an.  

   Das `{docker.image.prefix}`-Element für die Authentifizierung ist der **Anmeldeserver** auf der zuvor gezeigten Registrierungsseite. `{project.artifactId}` ist der Name und die Versionsnummer der JAR-Datei aus dem ersten Maven-Build des Projekts.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jre:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navigieren Sie zu dem abgeschlossenen Projektverzeichnis für Ihre Spring Boot-Anwendung, und führen Sie den folgenden Befehl aus, um die Anwendung erneut zu erstellen und den Container in die Azure-Containerregistrierung zu übertragen:

   ```bash
   az acr login -n wingtiptoysregistry && mvn compile jib:build
   ```

> [!NOTE]
> 1. Mit dem Befehl `az acr login ...` wird versucht, die Anmeldung bei Azure Container Registry durchzuführen. Andernfalls müssen Sie `<username>` und `<password>` für „jib-maven-plugin“ angeben. Weitere Informationen finden Sie unter [Authentifizierungsmethoden](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin#authentication-methods) in Jib.
> 2. Wenn Sie Jib zum Pushen Ihres Images an Azure Container Registry verwenden, berücksichtigt das Image *Dockerfile* nicht. Ausführliche Informationen finden Sie in [diesem Dokument](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html).
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Erstellen einer Web-App unter Linux in Azure App Service über Ihr Containerimage

1. Navigieren Sie zum [Azure portal], und melden Sie sich an.

2. Klicken Sie auf das Menüsymbol für **+ Ressource erstellen** > **Compute** > **Web-App für Container**.
   
   ![Erstellen einer neuen Web-App im Azure-Portal][LX01]

3. Wenn die Seite **Web-App unter Linux** angezeigt wird, geben Sie folgende Informationen ein:

   * Wählen Sie Ihr **Abonnement** aus der Dropdown-Liste aus.

   * Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder geben Sie einen Namen an, um eine neue Ressourcengruppe zu erstellen.

   * Geben Sie einen eindeutigen Namen für **App-Name** ein, z. B. *wingtiptoyslinux*.

   * Geben Sie unter **Veröffentlichen** `Docker Container` an.

   * Wählen Sie unter *Betriebssystem* die Option **Linux** aus.

   * Wählen Sie **Region** aus.

   * Wählen Sie unter **Linux-Plan** einen vorhandenen **App Service-Plan** aus. Klicken Sie alternativ auf **Neu erstellen**, um einen neuen App Service-Plan zu erstellen.

   * Klicken Sie auf **Weiter: Docker**.

   ![Konfigurieren von Einstellungen für Web-Apps][LX02]

      Wählen Sie auf der Seite **Web-App** die Option **Docker** aus, und geben Sie die folgenden Informationen ein:

   * Wählen Sie **Einzelner Container** aus.

   * **Registrierung**: Wählen Sie Ihren Container aus, etwa *wingtiptoysregistry*.

   * **Image**: Wählen Sie das zuvor erstellte Image aus, etwa *gs-spring-boot-docker*.

   * **Tag:** Wählen Sie das Tag für das Image aus, etwa *latest*.

   * **Startbefehl**: Lassen Sie dieses Feld leer, da das Image bereits den Startbefehl enthält.

   Nachdem Sie alle oben genannten Informationen eingegeben haben, klicken Sie auf **Bewerten + erstellen**.

   ![Konfigurieren von Einstellungen für Web-Apps][LX02-A]

   * Klicken Sie auf **Überprüfen + erstellen**.

Prüfen Sie die Informationen, und klicken Sie auf **Erstellen**.

Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.  Auf der Bereitstellungsseite wird die URL für den Zugriff auf die Anwendung angezeigt.

   ![Abrufen der Bereitstellungs-URL][LX02-B]

> [!NOTE]
>
> Azure ordnet dem eingebetteten Tomcat-Server, der an Port 80 ausgeführt wird, automatisch Internetanforderungen zu. Wenn Sie Ihren eingebetteten Tomcat-Server aber so konfiguriert haben, dass er an Port 8080 oder einem benutzerdefinierten Port ausgeführt wird, müssen Sie Ihrer Web-App eine Umgebungsvariable hinzufügen, die den Port für Ihren eingebetteten Tomcat-Server definiert. Führen Sie dazu die folgenden Schritte aus:
>
> 1. Navigieren Sie zum [Azure portal], und melden Sie sich an.
>
> 2. Klicken Sie auf das Symbol für **Web-Apps**, und wählen Sie auf der Seite **App Services** Ihre Web-App aus.
>
> 3. Klicken Sie im Navigationsbereich auf der linken Seite auf **Konfiguration**.
>
> 4. Fügen Sie im Abschnitt **Anwendungseinstellungen** eine neue Einstellung mit dem Namen **WEBSITES_PORT** hinzu, und geben Sie Ihre benutzerdefinierte Portnummer für den Wert ein.
>
> 5. Klicken Sie auf **OK**. Klicken Sie anschließend auf **Speichern**.
>
> ![Speichern einer benutzerdefinierten Portnummer im Azure-Portal][LX03]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Bereitstellen von Spring Boot-Anwendungen in Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Deploy a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Bereitstellen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

Weitere Informationen zum Spring Boot-Beispielprojekt in Docker finden Sie unter [Spring Boot on Docker Getting Started].

Hilfe zu den ersten Schritten mit eigenen Spring Boot-Anwendungen finden Sie bei **Spring Initializr** unter https://start.spring.io/.

Weitere Informationen zu den ersten Schritten beim Erstellen einer einfachen Spring Boot-Anwendung finden Sie bei Spring Initializr unter https://start.spring.io/.

Weitere Beispiele zur Vorgehensweise bei der Verwendung benutzerdefinierter Docker-Images mit Azure finden Sie unter [Verwenden eines benutzerdefinierten Docker-Images für Azure-Web-Apps unter Linux].

<!-- URL List -->

[Azure-Befehlszeilenschnittstelle (CLI)]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure für Java-Entwickler]: /azure/developer/java/
[Azure portal]: https://portal.azure.com/
[Erstellen einer privaten Docker-Containerregistrierung im Azure-Portal]: /azure/container-registry/container-registry-get-started-portal
[Verwenden eines benutzerdefinierten Docker-Images für Azure-Web-Apps unter Linux]: /azure/app-service/tutorial-custom-container
[Docker]: https://www.docker.com/
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Git-Client]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/java/ (Arbeiten mit Azure DevOps und Java)
[Maven]: http://maven.apache.org/
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker (Erste Schritte mit Spring Boot in Docker)
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: media/deploy-spring-boot-java-app-on-linux/AR03.png
[LX01]: media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: media/deploy-spring-boot-java-app-on-linux/LX03.png
