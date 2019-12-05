---
title: CI/CD für MicroProfile-Apps mit Azure Pipelines
description: Erfahren Sie, wie Sie mithilfe von Azure Pipelines einen CI/CD-Releasezyklus zum Bereitstellen einer MicroProfile-App in einer Azure-Web-App für Container-Instanz einrichten.
services: devops
documentationcenter: MicroProfile
author: ruyakubu
manager: brunoborges
ms.author: ruyakubu
ms.date: 07/31/2019
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: cdd704626b51105f93c19378511f4a267cb56649
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812238"
---
# <a name="cicd-for-microprofile-apps-using-azure-pipelines"></a>CI/CD für MicroProfile-Apps mit Azure Pipelines

Dieses Tutorial zeigt Ihnen, wie Sie mithilfe von Azure Pipelines mühelos einen CI/CD-Releasezyklus (Continuous Integration/Continuous Deployment) zum Bereitstellen ihrer [MicroProfile](http://microprofile.io) Java EE-Anwendung in einer Azure-Web-App für Container-Instanz einrichten können. Die MicroProfile-App in diesem Tutorial verwendet ein [Payara Micro](https://www.payara.fish/payara_micro)-Basis-Image, um eine WAR-Datei zu erstellen. 

```Dockerfile
FROM payara/micro:5.182
COPY target/*.war $DEPLOY_DIR/ROOT.war
EXPOSE 8080
```
Sie starten den Azure Pipelines-Containerprozess, indem Sie ein Docker-Image erstellen und das Containerimage in eine Azure Container Registry (ACR) pushen. Sie schließen den Prozess ab, indem Sie eine Azure Pipelines-Releasepipeline erstellen und das Containerimage in einer Web-App bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine [Azure Container Registry](https://azure.microsoft.com/services/container-registry).
   
1. Erstellen Sie im Azure-Portal eine [Azure Web-App für Container](https://azure.microsoft.com/services/app-service/containers/). Wählen Sie **Linux** als **Betriebssystem** aus, und wählen Sie für **Container konfigurieren** die Option **Schnellstart** als **Imagequelle** aus.  
   
1. Kopieren und speichern Sie die Klon-URL aus dem GitHub-Repository unter [https://github.com/Azure-Samples/microprofile-hello-azure](https://github.com/Azure-Samples/microprofile-hello-azure).
   
1. Registrieren Sie sich bei Ihrer [Azure DevOps](https://dev.azure.com)-Organisation, oder melden Sie sich dort an, und erstellen Sie ein neues [Projekt](/vsts/organizations/projects/create-project). 
   
1. Importieren Sie das GitHub-Beispielrepository in Azure Repos:
   
   1. Wählen Sie auf Ihrer Azure DevOps-Projektseite im linken Navigationsbereich **Repos** aus.
   1. Wählen Sie unter **oder Repository importieren** den Befehl **Importieren** aus. 
   1. Geben Sie unter **Klon-URL** die von Ihnen gespeicherte Git-Klon-URL ein, und wählen Sie **Importieren** aus.
  
## <a name="create-a-build-pipeline"></a>Erstellen einer Buildpipeline

Die Continuous Integration-Buildpipeline in Azure Pipelines führt jedes Mal automatisch alle Buildaufgaben aus, wenn in der Java EE-Quell-App ein Commit stattfindet. In diesem Beispiel verwendet Azure Pipelines Maven zum Erstellen des Java-MicroProfile-Projekts.

1. Wählen Sie auf Ihrer Azure DevOps-Projektseite im linken Navigationsbereich **Pipelines** > **Builds** aus. 
   
1. Wählen Sie **Neue Pipeline** aus.
   
1. Wählen Sie **Klassischen Editor verwenden, um eine Pipeline ohne YAML zu erstellen** aus. 
   
1. Stellen Sie sicher, dass Ihr Projektname und das importierte GitHub-Repository in den Feldern angezeigt werden, und wählen Sie **Weiter** aus.
   
1. Wählen Sie in der Liste der Vorlagen **Maven** aus, und wählen Sie anschließend **Übernehmen** aus.
   
1. Stellen Sie im rechten Bereich sicher, dass in der Dropdownliste **Agentpool** der Eintrag **Hosted Ubuntu 1604** angezeigt wird.
   
   > [!NOTE]
   > Diese Einstellung teilt Azure Pipelines mit, welcher Buildserver verwendet werden soll.  Sie können auch Ihren privaten, angepassten Buildserver verwenden.
   
1. Um die Pipeline für Continuous Integration zu konfigurieren, wählen Sie im linken Bereich die Registerkarte **Trigger** aus, und aktivieren Sie dann das Kontrollkästchen neben **Continuous Integration aktivieren**.  
   
1. Wählen Sie am oberen Rand der Seite das Dropdownmenü neben **Speichern und in Warteschlange einreihen** aus, und wählen Sie dann **Speichern** aus. 

   ![Aktivieren von Continuous Integration](media/cicd-microprofile/continuous-integration.png)

## <a name="create-a-docker-build-image"></a>Erstellen eines Docker-Buildimages

Azure Pipelines verwendet eine Dockerfile-Datei mit einem Basisimage von Payara Micro, um ein Docker-Image zu erstellen.  

1. Wählen Sie die Registerkarte **Aufgaben** aus, und wählen Sie dann das Pluszeichen **+** neben **Agent-Auftrag 1** aus, um die Aufgabe hinzuzufügen.
   
   ![Hinzufügen einer neuen Aufgabe](media/cicd-microprofile/add-task.png)
   
1. Wählen Sie im rechten Bereich **Docker** aus der Liste der Vorlagen aus, und wählen Sie anschließend **Hinzufügen** aus. 
   
1. Wählen Sie im linken Bereich **buildAndPush** aus, und geben Sie im rechten Bereich eine Beschreibung in das Feld **Anzeigename** ein.
   
1. Wählen Sie unter **Containerrepository** neben dem Feld **Containerregistrierung** die Option **Neu** aus. 
   
1. Füllen Sie das Dialogfeld **Dienstverbindung für Docker-Registrierung hinzufügen** wie folgt aus:
   
   |Feld|Wert|
   |---|---|
   |**Registrierungstyp**|Wählen Sie **Azure Container Registry** aus.|
   |**Verbindungsname**|Geben Sie einen Namen für die Verbindung ein.|
   |**Azure-Abonnement**|Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus, und falls notwendig, wählen Sie **Autorisieren** aus.|
   |**Azure Container Registry**|Wählen Sie Ihren Azure Container Registry-Namen aus dem Dropdown aus.| 
   
1. Klicken Sie auf **OK**.
   
   ![Hinzufügen einer Dienstverbindung für Docker-Registrierung](media/cicd-microprofile/dockerconnection.png)
   
   > [!NOTE]
   > Wenn Sie Docker-Hub oder eine andere Registrierung verwenden, wählen Sie **Docker Hub** oder **Andere** anstelle von **Azure Container Registry** neben **Registrierungstyp** aus. Geben Sie dann die Anmeldeinformationen und Verbindungsinformationen für Ihre Containerregistrierung an.
   
1. Wählen Sie unter **Befehle** aus der Dropdownliste **Befehl** die Option **Build** aus.
   
1. Wählen Sie die Auslassungspunkte **...** neben dem Feld **Dockerfile** aus, wechseln Sie zu der **Dockerfile**-Datei, und wählen Sie sie aus Ihrem GitHub-Repository aus, und wählen Sie dann **OK** aus. 
   
   ![Auswählen der Dockerfile-Datei](media/cicd-microprofile/selectdockerfile.png)
   
1. Geben Sie unter **Tags** in einer neuen Zeile *Neueste* ein. 
   
1. Wählen Sie am oberen Rand der Seite das Dropdownmenü neben **Speichern und in Warteschlange einreihen** aus, und wählen Sie dann **Speichern** aus. 

## <a name="push-the-docker-image-to-acr"></a>Pushen des Docker-Images in ACR

Azure Pipelines überträgt das Docker-Image per Push in Ihre Azure Container Registry und verwendet es, um die MicroProfile-API-App als Container-Java-Web-App auszuführen.

1. Da Sie Docker in Azure Pipelines verwenden, erstellen Sie eine andere Docker-Vorlage, indem Sie die Schritte unter [Erstellen eines Docker-Buildimages](#create-a-docker-build-image) wiederholen. Wählen Sie diesmal im Dropdownmenü **Befehl** die Option **Push** aus.
   
1. Wählen Sie das Dropdownmenü neben **Speichern und in Warteschlange einreihen** aus, und wählen Sie dann **Speichern und in Warteschlange einreihen** aus. 
   
1. Stellen Sie im Popup **Pipeline ausführen** sicher, dass unter **Agentpool** der Eintrag **Hosted Ubuntu 1604** ausgewählt ist, und wählen Sie **Speichern und ausführen** aus. 
   
1. Nachdem der Buildprozess abgeschlossen wurde, können Sie den Link auf der Seite **Build** auswählen, um den Erfolg des Buildprozesses zu überprüfen und weitere Details anzuzeigen.
   
   ![Auswählen des Build-Links](media/cicd-microprofile/checkbuild.png)

## <a name="create-a-release-pipeline"></a>Erstellen einer Releasepipeline

Eine Continuous Releasepipeline in Azure Pipelines löst die Bereitstellung in einer Zielumgebung wie Azure automatisch aus, sobald der Buildprozess erfolgreich abgeschlossen wurde. Sie können Releasepipeline für Umgebungen wie Entwicklung, Testen, Staging oder Produktion erstellen.

1. Wählen Sie auf Ihrer Azure DevOps-Projektseite im linken Navigationsbereich **Pipelines** > **Releases** aus. 
   
1. Wählen Sie **Neue Pipeline** aus.
   
1. Wählen Sie in der Vorlagenliste die Option **Java-App in Azure App Service bereitstellen** aus, und wählen Sie dann **Anwenden** aus. 
   
   ![Auswählen der Vorlage „Java-App in Azure App Service bereitstellen“](media/cicd-microprofile/selectreleasetemplate.png)
   
1. Ändern Sie im Popup Fenster **Stufe 1** in einen Stufennamen wie *Dev*, *Test*, *Staging* oder *Produktion*, und schließen Sie dann das Fenster. 
   
1. Wählen Sie unter **Artefakte** im linken Bereich **Hinzufügen** aus, um Artefakte aus der Buildpipeline mit der Releasepipeline zu verknüpfen. 
   
1. Wählen Sie im rechten Bereich Ihre Buildpipeline in der Dropdownliste unter **Quelle (Buildpipeline)** aus, und wählen Sie dann **Hinzufügen** aus.
   
   ![Hinzufügen eines Buildartefakts](media/cicd-microprofile/addbuildartifact.png)
   
1. Wählen Sie den Link in der Stufe **Produktion** aus, um **Stufenaufgaben anzuzeigen**.
   
   ![Auswählen des Stufennamens](media/cicd-microprofile/viewstagetasks.png)
   
1. Füllen Sie im rechten Bereich das Formular wie folgt aus:
   
   |Feld|Wert|
   |---|---|
   |**Azure-Abonnement**|Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.|
   |**App-Typ**|Wählen Sie aus dem Dropdown **Web-App für Container (Linux)** aus.|
   |**App Service-Name**|Wählen Sie in der Dropdownliste Ihre ACR-Instanz aus.|
   |**Registrierung oder Namespaces**|Geben Sie Ihren ACR-Namen in das Feld ein. Geben Sie beispielsweise *mymicroprofileregistry.azure.io* ein.
   |**Repository**|Geben Sie das Repository ein, das Ihr Docker-Image enthält.| 
   
   ![Konfigurieren von Stufenaufgaben](media/cicd-microprofile/configurestage.png)
   
1. Wählen Sie im linken Bereich **WAR in Azure App Service bereitstellen** aus, und geben Sie im rechten Bereich das Tag *Neueste* in das Feld **Tag** ein. 
   
1. Wählen Sie im linken Bereich **Auf Agent ausführen** aus und im rechten Bereich in der Dropdownliste **Agentpool** die Option **Hosted Ubuntu 1604**. 

## <a name="set-up-environment-variables"></a>Einrichten von Umgebungsvariablen

Fügen Sie Umgebungsvariablen hinzu, und definieren Sie diese, um während der Bereitstellung eine Verbindung mit der Containerregistrierung herzustellen.

1. Wählen Sie die Registerkarte **Variablen** aus, und wählen Sie dann **Hinzufügen** aus, um die folgenden Variablen für Ihre Containerregistrierungs-URL, den Benutzernamen und das Kennwort hinzuzufügen. 
   
   |NAME|Wert|
   |---|---|
   |*registry.url*|Geben Sie Ihre Containerregistrierungs-URL ein. Beispiel: *https:\//mymicroprofileregistry.azure.io*|
   |*registry.username*|Geben Sie den Benutzernamen für die Registrierung ein.|
   |*registry.password*|Geben Sie das Kennwort für die Registrierung ein. Wählen Sie aus Sicherheitsgründen das Schlosssymbol aus, um den Kennwortwert auszublenden.|
   
   ![Hinzufügen von Variablen](media/cicd-microprofile/addvariables.png)
   
1. Wählen Sie auf der Registerkarte **Aufgaben** im linken Bereich **WAR in Azure App Service bereitstellen** aus. 
   
1. Erweitern Sie im rechten Bereich **Anwendungs- und Konfigurationseinstellungen**, und wählen Sie dann die Auslassungspunkte **...** neben dem Feld **App-Einstellungen** aus.
   
1. Wählen Sie im Popup **App-Einstellungen** den Befehl **Hinzufügen** aus, um die App-Einstellungsvariablen zu definieren und zuzuweisen:
   
   |NAME|Wert|
   |---|---|
   |*DOCKER_REGISTRY_SERVER_URL*|*$(registry.url)*|
   |*DOCKER_REGISTRY_SERVER_USERNAME*|*$(registry.username)*|
   |*DOCKER_REGISTRY_SERVER_PASSWORD*|*$(registry.password)*|
   
1. Klicken Sie auf **OK**.
   
   ![Hinzufügen und Festlegen von Variablen](media/cicd-microprofile/appsettings.png)
   
## <a name="set-up-continuous-deployment"></a>Einrichten der fortlaufenden Bereitstellung 

Führen Sie die folgenden Schritte aus, um Continuous Deployment zu aktivieren: 

1. Wählen Sie auf der Registerkarte **Pipeline** unter **Artefakte** das Blitzsymbol im Buildartefakt aus. 
   
1. Legen Sie im rechten Bereich den **Continuous Deployment-Trigger** auf **Aktiviert** fest.
   
1. Wählen Sie oben rechts **Speichern** aus und dann noch mal **Speichern**. 
   
   ![Aktivieren des Continuous Deployment-Triggers](media/cicd-microprofile/setcontinuousdeployment.png)
   
## <a name="deploy-the-java-app"></a>Bereitstellen der Java-App

Nachdem Sie nun CI/CD aktiviert haben, werden durch die Änderung des 	Quellcodes Builds und Releases automatisch erstellt und ausgeführt. Sie können Releases auch wie folgt manuell erstellen und ausführen:

1. Wählen Sie oben rechts auf der Seite „Releasepipeline“ **Release erstellen** aus.
   
1. Wählen Sie auf der Seite **Neues Release erstellen** den Stufennamen unter **Stufen für einen Trigger werden von automatisiert in manuell geändert** aus. 
   
1. Klicken Sie auf **Erstellen**. 
   
1. Wählen Sie den Releasenamen aus, zeigen Sie auf die Stufe, oder wählen Sie eine aus, und wählen Sie dann **Bereitstellen** aus. 
   
## <a name="test-the-java-web-app"></a>Testen der Java-Web-App

Testen Sie nach erfolgreichem Abschluss der Bereitstellung Ihre Web-App. 

1. Kopieren Sie die Web-App-URL aus dem Azure-Portal.
   
   ![App Service-App im Azure-Portal](media/cicd-microprofile/portalurl.png)
   
1. Geben Sie die URL in Ihrem Webbrowser ein, um Ihre App auszuführen. Auf der Webseite sollte **Hello Azure!** angezeigt werden.
   
   ![Java-Web-App-Seite](media/cicd-microprofile/webapp.png)

