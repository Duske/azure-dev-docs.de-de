---
title: Bereitstellen eines virtuellen Linux-Computers mit Jenkins und Azure DevOps Services
description: Es wird beschrieben, wie Sie Continuous Integration/Deployment (CI/CD) für die Bereitstellung von Apps auf virtuellen Linux-Computern mit Jenkins und Azure DevOps Services nutzen.
keywords: Jenkins, Azure, DevOps, virtueller Computer, CI/CD, Azure DevOps Services
ms.topic: tutorial
ms.date: 07/31/2018
ms.custom: devx-track-jenkins
ms.openlocfilehash: 3eedcc72a7c272f1cacf97b9071e750aab5a446e
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831326"
---
# <a name="tutorial-deploy-to-linux-virtual-machine-using-jenkins-and-azure-devops-services"></a>Tutorial: Bereitstellen eines virtuellen Linux-Computers mit Jenkins und Azure DevOps Services

Continuous Integration (CI) und Continuous Deployment (CD) bilden eine Pipeline, über die Sie Ihren Code erstellen, freigeben und bereitstellen können. Azure DevOps Services umfasst eine vollständige Gruppe von CI/CD-Automatisierungstools mit vollem Funktionsumfang für die Bereitstellung in Azure. Jenkins ist ein gängiges serverbasiertes CI/CD-Drittanbietertool, das auch CI/CD-Automatisierung beinhaltet. Sie können Azure DevOps Services und Jenkins zusammen verwenden und so die Bereitstellung Ihrer Cloud-Apps oder Clouddienste anpassen.

In diesem Tutorial erstellen Sie mithilfe von Jenkins eine Node.js-Web-App. Anschließend stellen Sie diese mit Azure DevOps

für eine [Bereitstellungsgruppe](/azure/devops/pipelines/release/deployment-groups/index?view=vsts) bereit, die virtuelle Linux-Computer (VMs) enthält. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Abrufen der Beispiel-App
> * Konfigurieren von Jenkins-Plug-Ins
> * Konfigurieren eines Jenkins Freestyle-Projekts für Node.js
> * Konfigurieren von Jenkins für die Azure DevOps Services-Integration
> * Erstellen eines Jenkins-Dienstendpunkts
> * Erstellen einer Bereitstellungsgruppe für die virtuellen Azure-Computer
> * Erstellen einer Azure Pipelines-Releasepipeline.
> * Ausführen manueller und durch CI ausgelöster Bereitstellungen

## <a name="before-you-begin"></a>Voraussetzungen

* Sie benötigen Zugriff auf einen Jenkins-Server. Falls Sie noch keinen Jenkins-Server erstellt haben, lesen Sie unter [Erstellen eines Jenkins-Masters auf einem virtuellen Azure-Computer](/azure/jenkins/install-jenkins-solution-template) weiter. 

* Melden Sie sich bei Ihrer Azure DevOps Services-Organisation an (**https://{yourorganization}.visualstudio.com**). 
  Sie können eine [kostenlose Azure DevOps-Organisation](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) erhalten.

  > [!NOTE]
  > Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Sie benötigen einen virtuellen Linux-Computer für ein Bereitstellungsziel.  Weitere Informationen finden Sie unter [Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle](/azure/virtual-machines/linux/tutorial-manage-vm).

*  Öffnen Sie den eingehenden Port 80 für den virtuellen Computer. Weitere Informationen finden Sie unter [Erstellen von Netzwerksicherheitsgruppen über das Azure-Portal](/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Abrufen der Beispiel-App

Für die Bereitstellung benötigen Sie eine in einem Git-Repository gespeicherte App.
Für dieses Tutorial empfiehlt sich [diese über GitHub verfügbare Beispiel-App](https://github.com/azure-devops/fabrikam-node). Dieses Tutorial enthält ein Beispielskript, das verwendet wird, um Node.js und eine Anwendung zu installieren. Wenn Sie Ihr eigenes Repository verwenden möchten, sollten Sie ein ähnliches Beispiel konfigurieren.

Erstellen Sie einen Fork dieser App, und notieren Sie den Speicherort (URL) zur Verwendung in nachfolgenden Schritten in diesem Tutorial. Weitere Informationen finden Sie unter [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Forken eines Repositorys).    

> [!NOTE]
> Die App wurde über [Yeoman](https://yeoman.io/learning/index.html) erstellt. Sie verwendet Express, Bower und Grunt. Darüber hinaus verfügt sie über einige npm-Pakete als Abhängigkeiten.
> Die Beispiel-App enthält zudem ein Skript, über das Nginx eingerichtet und die App bereitgestellt wird. Es wird auf den virtuellen Computern ausgeführt. Das Skript führt folgende Aktionen aus:
> 1. Es installiert Node, Nginx und PM2.
> 2. Es konfiguriert Nginx und PM2.
> 3. Es startet die Node-App.

## <a name="configure-jenkins-plug-ins"></a>Konfigurieren von Jenkins-Plug-Ins

Als Erstes müssen zwei Jenkins-Plug-Ins konfiguriert werden: **NodeJS** und **VS Team Services Continuous Deployment**.

1. Öffnen Sie Ihr Jenkins-Konto, und klicken Sie auf **Manage Jenkins** (Jenkins verwalten) aus.
2. Klicken Sie auf der Seite **Manage Jenkins** (Jenkins verwalten) auf **Manage Plugins** (Plug-Ins verwalten).
3. Filtern Sie die Liste, um das Plug-In **NodeJS** zu finden, und klicken Sie anschließend auf **Install without restart** (Ohne Neustart installieren).
    ![Hinzufügen des NodeJS-Plug-Ins zu Jenkins](media/deploy-to-linux-vm-using-azure-devops-services/jenkins-nodejs-plugin.png)
4. Filtern Sie die Liste, um das Plug-In **VS Team Services Continuous Deployment** zu finden, und klicken Sie anschließend auf **Install without restart** (Ohne Neustart installieren).
5. Navigieren Sie wieder zum Jenkins-Dashboard, und klicken Sie auf **Manage Jenkins** (Jenkins verwalten).
6. Klicken Sie auf **Global Tool Configuration** (Globale Toolkonfiguration). Suchen Sie nach **NodeJS**, und klicken Sie auf **NodeJS installations** (NodeJS-Installationen).
7. Klicken Sie auf **Install automatically** (Automatisch installieren), und geben Sie einen Wert für **Name** ein.
8. Wählen Sie **Speichern** aus.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurieren eines Jenkins Freestyle-Projekts für Node.js

1. Wählen Sie **Neues Element** aus. Geben Sie einen Elementnamen ein.
2. Klicken Sie auf **Freestyle Project** (Freestyle-Projekt). Klicken Sie auf **OK**.
3. Wählen Sie auf der Registerkarte **Source Code Management** (Quellcodeverwaltung) die Option **Git** aus, und geben Sie die Repositorydetails sowie den Branch mit Ihrem App-Code ein.    
    ![Hinzufügen eines Repositorys zum Build](media/deploy-to-linux-vm-using-azure-devops-services/jenkins-git.png)
4. Wählen Sie auf der Registerkarte **Build Triggers** (Buildtrigger) die Option **Poll SCM** (SCM abrufen) aus, und geben Sie den Zeitplan `H/03 * * * *` ein, um alle drei Minuten Änderungen im Git-Repository abzurufen. 
5. Wählen Sie auf der Registerkarte **Build Environment** (Buildumgebung) die Option **Provide Node &amp; npm bin/folder PATH** (Knoten angeben & npm bin/PATH-Ordner) und dann den Wert für **NodeJS Installation** (NodeJS-Installation) aus. Behalten Sie für **npmrc file** (NPMRC-Datei) die Einstellung **use system default** (Systemstandard verwenden) bei.
6. Wählen Sie auf der Registerkarte **Build** die Option **Execute shell** (Shell ausführen) aus, und geben Sie den Befehl `npm install` ein, um sicherzustellen, dass alle Abhängigkeiten aktualisiert werden.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Konfigurieren von Jenkins für die Azure DevOps Services-Integration

> [!NOTE]
> Stellen sie sicher, dass das für die folgenden Schritte verwendete persönliche Zugriffstoken (Personal Access Token, PAT) die Berechtigung *Release (Lesen, Schreiben, Ausführen und Verwalten)* in Azure DevOps Services enthält.
 
1.  Erstellen Sie ein persönliches Zugriffstoken in Ihrer Azure DevOps Services-Organisation, wenn Sie noch keines besitzen. In Jenkins sind diese Informationen für den Zugriff auf Ihre Azure DevOps Services-Organisation erforderlich. Speichern Sie die Tokeninformationen für die folgenden Schritte in diesem Abschnitt.
  
    Informationen zum Generieren eines Tokens finden Sie unter [Authentifizieren Zugriffs mit persönlichen Zugriffstoken für Azure DevOps Services](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts).
2. Klicken Sie auf der Registerkarte **Post-build Actions** (Postbuildaktionen) auf **Add post-build action** (Postbuildaktion hinzufügen). Klicken Sie auf **Archive the artifacts** (Artefakte archivieren).
3. Geben Sie für **Files to archive** (Dateien zur Archivierung) den Wert `**/*` ein, um alle Dateien einzuschließen.
4. Klicken Sie zum Erstellen einer weiteren Aktion auf **Add post-build action** (Postbuildaktion hinzufügen).
5. Klicken Sie auf **Trigger release in TFS/Team Services** (Release in TFS/Team Services auslösen). Geben Sie den URI für Ihre Azure DevOps Services-Organisation ein wie z.B. **https://{your-organization-name}.visualstudio.com**.
6. Geben Sie den **Projektnamen** ein.
7. Wählen Sie einen Namen für die Releasepipeline aus. (Sie können diese Releasepipeline später in Azure DevOps Services erstellen.)
8. Wählen Sie die Anmeldeinformationen für die Verbindung mit Ihrer Azure DevOps Services- oder Azure DevOps Server-Umgebung aus:
   - Lassen Sie bei Verwendung von Azure DevOps Services das Feld **Benutzername** leer. 
   - Geben Sie bei Verwendung einer lokalen Version von Azure DevOps Server einen Benutzernamen und ein Kennwort ein.    
   ![Konfigurieren von Jenkins-Postbuildaktionen](media/deploy-to-linux-vm-using-azure-devops-services/trigger-release-from-jenkins.png)
5. Speichern Sie das Jenkins-Projekt.


## <a name="create-a-jenkins-service-endpoint"></a>Erstellen eines Jenkins-Dienstendpunkts

Über einen Dienstendpunkt kann eine Verbindung zwischen Azure DevOps Services und Jenkins hergestellt werden.

1. Öffnen Sie in Azure DevOps Services die Seite **Dienste**, öffnen Sie die Liste **Neuer Dienstendpunkt**, und wählen Sie **Jenkins** aus.
   ![Hinzufügen eines Jenkins-Endpunkts](media/deploy-to-linux-vm-using-azure-devops-services/add-jenkins-endpoint.png)
2. Geben Sie einen Namen für die Verbindung ein.
3. Geben Sie die URL Ihres Jenkins-Servers ein, und klicken Sie auf die Option **Nicht vertrauenswürdige SSL-Zertifikate annehmen**. Beispiel-URL: **http://{Ihre Jenkins-URL}.westcentralus.cloudapp.azure.com**
4. Geben Sie den Benutzernamen und das Kennwort für Ihr Jenkins-Konto ein.
5. Klicken Sie auf **Verbindung überprüfen**, um zu überprüfen, ob die Informationen korrekt sind.
6. Klicken Sie auf **OK**, um den Dienstendpunkt zu erstellen.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Erstellen einer Bereitstellungsgruppe für virtuelle Azure-Computer

Sie benötigen eine [Bereitstellungsgruppe](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) für die Registrierung des Azure DevOps Services-Agents, damit die Releasepipeline auf dem virtuellen Computer bereitgestellt werden kann. Mit Bereitstellungsgruppen können Sie komfortabel logische Gruppen von Zielcomputern für die Bereitstellung definieren und den erforderlichen Agent auf den einzelnen Computern installieren.

   > [!NOTE]
   > Achten Sie bei der folgenden Prozedur darauf, die erforderlichen Komponenten zu installieren und *das Skript nicht mit sudo-Berechtigungen auszuführen*.

1. Öffnen Sie die Registerkarte **Releases** des Hubs **Build &amp; Release**, und klicken Sie auf der Registerkarte **Bereitstellungsgruppen** auf **+ Neu**.
2. Geben Sie einen Namen und eine optionale Beschreibung für die Bereitstellungsgruppe ein. Klicken Sie anschließend auf **Erstellen**.
3. Wählen Sie das Betriebssystem für den virtuellen Zielcomputer der Bereitstellung aus. Wählen Sie also beispielsweise **Ubuntu 16.04+** aus.
4. Klicken Sie auf **Verwenden Sie für die Authentifizierung ein persönliches Zugriffstoken im Skript**.
5. Wählen Sie den Link **Systemvoraussetzungen** aus. Installieren Sie die erforderlichen Komponenten für Ihr Betriebssystem.
6. Klicken Sie auf **Skript in die Zwischenablage kopieren**, um das Skript zu kopieren.
7. Melden Sie sich bei dem virtuellen Zielcomputer der Bereitstellung an, und führen Sie das Skript aus. Führen Sie das Skript nicht mit sudo-Berechtigungen aus.
8. Nach der Installation werden Sie aufgefordert, Bereitstellungsgruppenmarkierungen anzugeben. Übernehmen Sie die Standardeinstellungen.
9. Suchen Sie den neu registrierten virtuellen Computer in Azure DevOps Services unter **Bereitstellungsgruppen** (in **Ziele**).

## <a name="create-an-azure-pipelines-release-pipeline"></a>Erstellen einer Azure Pipelines-Releasepipeline

Eine Releasepipeline gibt den Prozess an, der in Azure Pipelines zum Bereitstellen der App verwendet wird. In diesem Beispiel führen Sie ein Shellskript aus.

So erstellen Sie die Releasepipeline in Azure Pipelines:

1. Öffnen Sie die Registerkarte **Releases** des Hubs **Build &amp; Release**, und klicken Sie auf **Releasepipeline erstellen**. 
2. Wählen Sie die Vorlage **Leer** aus, indem Sie mit einem **leeren Prozess** starten.
3. Klicken Sie im Abschnitt **Artefakte** auf **+ Artefakt hinzufügen**, und wählen Sie unter **Quelltyp** die Option **Jenkins** aus. Wählen Sie Ihre Jenkins-Dienstendpunktverbindung aus. Wählen Sie dann den Jenkins-Quellauftrag aus, und klicken Sie auf **Hinzufügen**.
4. Klicken Sie auf die Auslassungspunkte neben **Umgebung 1**. Klicken Sie auf **Bereitstellungsgruppenphase hinzufügen**.
5. Wählen Sie Ihre Bereitstellungsgruppe aus.
5. Klicken Sie auf **+** , um **Bereitstellungsgruppenphase** eine Aufgabe hinzuzufügen.
6. Wählen Sie die Aufgabe **Shellskript** aus, und klicken Sie anschließend auf **Hinzufügen**. Die Aufgabe **Shellskript** gibt die Konfiguration für ein Skript an, das auf jedem Server ausgeführt wird, um Node.js zu installieren und die App zu starten.
8. Geben Sie für **Skriptpfad** Folgendes ein: **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Klicken Sie auf **Erweitert**, und aktivieren Sie dann **Arbeitsverzeichnis angeben**.
10. Geben Sie für **Arbeitsverzeichnis** Folgendes ein: **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Ändern Sie den Namen der Releasepipeline in den Namen, den Sie auf der Registerkarte **Postbuildaktionen** des Builds in Jenkins angegeben haben. Dieser Name ist in Jenkins erforderlich, damit eine neue Freigabe ausgelöst werden kann, wenn die Quellartefakte aktualisiert werden.
12. Klicken Sie auf **Speichern** und dann auf **OK**, um die Releasepipeline zu speichern.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Ausführen manueller und durch CI ausgelöster Bereitstellungen

1. Klicken Sie auf **+ Release** und anschließend auf **Release erstellen**.
2. Wählen Sie in der hervorgehobenen Dropdownliste den abgeschlossenen Build aus, und klicken Sie anschließend auf **Warteschlange**.
3. Klicken Sie in der Popupmeldung auf den Releaselink. Beispiel: „Release **Release-1** wurde erstellt.“
4. Öffnen Sie die Registerkarte **Protokolle**, um die Konsolenausgabe der Freigabe anzuzeigen.
5. Öffnen Sie im Browser die URL für einen der Server, die Sie der Bereitstellungsgruppe hinzugefügt haben. Geben Sie dazu beispielsweise **http://{IP-Adresse des Servers}** ein.
6. Wechseln Sie zum Git-Quellrepository, und ändern Sie den Inhalt der Überschrift **h1** in der Datei „app/views/index.jade“ durch geänderten Text.
7. Committen Sie die Änderung.
8. Nach einigen Minuten wird auf der Seite **Releases** von Azure DevOps eine neu erstellte Freigabe angezeigt. Öffnen Sie die Freigabe, um anzuzeigen, wie die Bereitstellung durchgeführt wird. Glückwunsch!

## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Bereitstellung einer App in Azure mit Jenkins (für den Build) und Azure DevOps (für das Release) automatisiert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen der App in Jenkins
> * Konfigurieren von Jenkins für die Azure DevOps Services-Integration
> * Erstellen einer Bereitstellungsgruppe für die virtuellen Azure-Computer
> * Erstellen einer Azure Pipeline-Instanz zum Konfigurieren der virtuellen Computer und zum Bereitstellen der App.

Informationen zur Verwendung von Azure Pipelines für Build- und Releaseschritte finden Sie [hier](/azure/devops/pipelines/apps/cd/deploy-linuxvm-deploygroups).

Informationen zum Erstellen einer YAML-basierten CI/CD-Pipeline für die Bereitstellung auf virtuellen Computern finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/Jenkins/)