---
title: 'Tutorial: Bereitstellen über GitHub in Azure App Service mit Jenkins'
description: Es wird beschrieben, wie Sie Jenkins für Continuous Integration (CI) über GitHub und Continuous Deployment (CD) in Azure App Service für Java-Web-Apps konfigurieren.
keywords: Jenkins, Azure, DevOps, App Service
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 6516f5481f6170a9d15d43113ac0f3f234174931
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82170026"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-using-jenkins"></a>Tutorial: Bereitstellen über GitHub in Azure App Service mit Jenkins

In diesem Tutorial werden Continuous Integration (CI) und Continuous Deployment (CD) in Jenkins eingerichtet, um eine exemplarische Java-Web-App aus GitHub in [Azure App Service unter Linux](/azure/app-service/containers/app-service-linux-intro) bereitzustellen. Wenn Sie die App aktualisieren, indem Sie Commits an GitHub pushen, wird die App von Jenkins automatisch erstellt und erneut in Azure App Service veröffentlicht. Die Beispiel-App in diesem Tutorial wurde unter Verwendung des [Spring Boot](https://projects.spring.io/spring-boot/)-Frameworks entwickelt. 

![Übersicht über die GitHub-Bereitstellung in Azure App Service](media/deploy-from-github-to-azure-app-service/azure-continuous-integration-deployment-overview.png)

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Installieren des Jenkins-Plug-Ins, um die Erstellung auf der Grundlage von GitHub, die Bereitstellung in Azure App Service und andere ähnliche Aufgaben zu ermöglichen
> * Forken des GitHub-Beispielrepositorys, um eine Arbeitskopie zu erhalten
> * Herstellen einer Verbindung zwischen Jenkins und GitHub
> * Erstellen eines Azure-Dienstprinzipals, damit Jenkins ohne Ihre Anmeldeinformationen auf Azure zugreifen kann
> * Hinzufügen Ihres Dienstprinzipals zu Jenkins
> * Erstellen der Jenkins-Pipeline, die die Beispiel-App erstellt und bereitstellt, wenn Sie die App in GitHub aktualisieren
> * Erstellen von Build- und Bereitstellungsdateien für Ihre Jenkins-Pipeline
> * Verweisen Ihrer Jenkins-Pipeline auf das Build- und Bereitstellungsskript
> * Bereitstellen Ihrer Beispiel-App in Azure durch Ausführen eines manuellen Buildvorgangs
> * Pushen eines App-Updates in GitHub, um Jenkins zu veranlassen, die App zu erstellen und erneut in Azure bereitzustellen

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Eine [Jenkins](https://jenkins.io/)-Serverinstallation mit Java Development Kit (JDK) und Maven-Tools auf einem virtuellen Linux-Computer.

  Wenn Sie nicht über einen Jenkins-Server verfügen, führen Sie im Azure-Portal die folgenden Schritte aus: [Erstellen eines Jenkins-Servers auf einem virtuellen Azure-Linux-Computer](/azure/jenkins/install-jenkins-solution-template)

* Ein [GitHub](https://github.com)-Konto, um eine Arbeitskopie („fork“) für die exemplarische Java-Web-App zu erhalten. 

* Die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli). Diese können Sie entweder über Ihre lokale Befehlszeilenschnittstelle oder über [Azure Cloud Shell](/azure/cloud-shell/overview) ausführen.

## <a name="install-jenkins-plug-ins"></a>Installieren von Jenkins-Plug-Ins

1. Melden Sie sich unter folgender Adresse bei Ihrer Jenkins-Webkonsole an:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Wählen Sie auf der Hauptseite von Jenkins **Manage Jenkins** > **Manage Plugins** (Jenkins verwalten > Plug-Ins verwalten) aus.

   ![Verwalten von Jenkins-Plug-Ins](media/deploy-from-github-to-azure-app-service/manage-plug-ins-for-azure.png)

1. Wählen Sie auf der Registerkarte **Available** (Verfügbar) folgende Plug-Ins aus:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Environment Injector-Plug-In](https://plugins.jenkins.io/envinject)
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   Sollten diese Plug-Ins nicht angezeigt werden, überprüfen Sie auf der Registerkarte **Installed** (Installiert), ob sie bereits installiert sind.

1. Wählen Sie zum Installieren der ausgewählten-Plug-Ins die Option **Download now and install after restart** (Jetzt herunterladen und nach Neustart installieren) aus.

1. Wählen Sie nach Abschluss des Vorgangs im Jenkins-Menü **Manage Jenkins** (Jenkins verwalten) aus, um für weitere Schritte zur Verwaltungsseite von Jenkins zurückzukehren.

## <a name="fork-sample-github-repo"></a>Forken des GitHub-Beispielrepositorys

1. [Melden Sie sich beim GitHub-Repository für die Spring Boot-Beispielanwendung an.](https://github.com/spring-guides/gs-spring-boot) 

1. Wählen Sie in GitHub in der rechten oberen Ecke **Fork** aus.

   ![Forken des Beispielrepositorys aus GitHub](media/deploy-from-github-to-azure-app-service/fork-github-repo.png)

1. Folgen Sie den Anweisungen, um Ihr GitHub-Konto auszuwählen und das Forken abzuschließen.

Richten Sie als Nächstes Jenkins mit Ihren GitHub-Anmeldeinformationen ein.

## <a name="connect-jenkins-to-github"></a>Herstellen einer Verbindung zwischen Jenkins und GitHub

Aktivieren Sie [GitHub-Webhooks](https://developer.github.com/webhooks/) in Jenkins, damit Jenkins GitHub überwachen und entsprechend reagieren kann, wenn neue Commits an die Web-App in Ihrem GitHub-Fork gepusht werden.

> [!NOTE]
> 
> Mit diesen Schritten werden Anmeldeinformationen für ein persönliches Zugriffstoken erstellt, um Jenkins die Zusammenarbeit mit GitHub zu ermöglichen. Dabei werden Ihr Benutzername und Ihr Kennwort für GitHub verwendet. 
> Sollte Ihr GitHub-Konto die zweistufige Authentifizierung verwenden, müssen Sie stattdessen Ihr Token in GitHub erstellen und Jenkins für die Verwendung dieses Tokens konfigurieren. 
> Weitere Informationen finden Sie in der Dokumentation für das [GitHub-Plug-In von Jenkins](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin).

1. Wählen Sie auf der Seite **Manage Jenkins** (Jenkins verwalten) die Option **Configure System** (System konfigurieren) aus. 

   ![Konfigurieren des Systems in Jenkins](media/deploy-from-github-to-azure-app-service/manage-jenkins-configure-system.png)

1. Geben Sie im Abschnitt **GitHub** Details für Ihren GitHub-Server an. Wählen Sie in der Liste **Add GitHub Server** (GitHub-Server hinzufügen) die Option **GitHub Server** (GitHub-Server) aus. 

   ![Hinzufügen des GitHub-Servers in Jenkins](media/deploy-from-github-to-azure-app-service/add-GitHub-server.png)

1. Sollte die Eigenschaft **Manage Hooks** (Hooks verwalten) nicht ausgewählt sein, wählen Sie diese Eigenschaft aus. Wählen Sie **Advanced** (Erweitert) aus, um weitere Einstellungen angeben zu können. 

   ![Angeben erweiterter Jenkins-Einstellungen für GitHub-Server](media/deploy-from-github-to-azure-app-service/advanced-GitHub-settings.png)

1. Wählen Sie in der Liste **Manage additional GitHub actions** (Zusätzliche GitHub-Aktionen verwalten) die Option **Convert login and password to token** (Anmeldename und Kennwort in Token konvertieren) aus.

   ![Konvertieren von Anmeldename und Kennwort in ein Token für GitHub](media/deploy-from-github-to-azure-app-service/manage-additional-actions.png)

1. Wählen Sie **From login and password** (Auf der Grundlage von Anmeldename und Kennwort) aus, um Ihren Benutzernamen und Ihr Kennwort für GitHub eingeben zu können. Wählen Sie anschließend **Create token credentials** (Tokenanmeldeinformationen erstellen) aus, um ein [persönliches Zugriffstoken (Personal Access Token, PAT) für GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) zu erstellen.   

   ![Erstellen eines persönlichen Zugriffstokens für GitHub auf der Grundlage von Anmeldename und Kennwort](media/deploy-from-github-to-azure-app-service/create-github-token-credentials.png)

1. Wählen Sie im Abschnitt **GitHub Server** (GitHub-Server) in der Liste mit den **Anmeldeinformationen** Ihr neues Token aus. Wählen Sie **Test connection** (Verbindung testen) aus, um zu überprüfen, ob die Authentifizierung funktioniert.

   ![Überprüfen der Verbindung mit dem GitHub-Server unter Verwendung des neuen persönlichen Zugriffstokens](media/deploy-from-github-to-azure-app-service/check-github-connection.png)

Erstellen Sie als Nächstes den Azure-Dienstprinzipal, den Jenkins für die Authentifizierung von und den Zugriff auf Azure-Ressourcen benötigt.

## <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

In einem späteren Abschnitt erstellen Sie einen Jenkins-Pipelineauftrag, der Ihre App auf der Grundlage von GitHub erstellt und in Azure App Service bereitstellt. Damit Jenkins auf Azure zugreifen kann, ohne Ihre Anmeldeinformationen eingeben zu müssen, erstellen Sie in Azure Active Directory einen [Dienstprinzipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) für Jenkins. Ein Dienstprinzipal ist eine separate Identität, die Jenkins für die Authentifizierung des Zugriffs auf Azure-Ressourcen verwenden kann. Führen Sie zum Erstellen dieses Dienstprinzipals den Azure CLI-Befehl [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) aus – entweder über Ihre lokale Befehlszeile oder über Azure Cloud Shell. Beispiel: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Der Dienstprinzipalname muss in Anführungszeichen gesetzt werden. Erstellen Sie außerdem ein sicheres Kennwort unter Berücksichtigung der [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Wenn Sie kein Kennwort angeben, erstellt die Azure-Befehlszeilenschnittstelle ein Kennwort für Sie. 

Der Befehl **`create-for-rbac`** generiert die folgende Ausgabe: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Falls Sie bereits über einen Dienstprinzipal verfügen, können Sie stattdessen diese Identität verwenden.
> Verwenden Sie die Eigenschaftswerte `appId`, `password` und `tenant`, wenn Sie Dienstprinzipalwerte für die Authentifizierung angeben. 
> Verwenden Sie den Eigenschaftswert `displayName`, wenn Sie nach einem vorhandenen Dienstprinzipal suchen.

## <a name="add-service-principal-to-jenkins"></a>Hinzufügen des Dienstprinzipals zu Jenkins

1. Wählen Sie auf der Hauptseite von Jenkins **Credentials** > **System** (Anmeldeinformationen > System) aus. 

1. Wählen Sie auf der Seite **System** unter **Domain** (Domäne) die Option **Global credentials (unrestricted)** (Globale Anmeldeinformationen (uneingeschränkt)) aus.

1. Wählen Sie im linken Menü die Option **Add Credentials** (Anmeldeinformationen hinzufügen) aus.

1. Wählen Sie in der Liste **Kind** (Art) die Option **Azure Service Principal** (Azure-Dienstprinzipal) aus.

1. Geben Sie die Informationen für Ihren Dienstprinzipal und Ihr Azure-Abonnement in die Eigenschaften ein, die in der Tabelle in diesem Schritt beschrieben sind:

   ![Hinzufügen der Anmeldeinformationen für den Azure-Dienstprinzipal](media/deploy-from-github-to-azure-app-service/add-service-principal-credentials.png)

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------| 
   | **Abonnement-ID** | <*yourAzureSubscription-ID*> | Der GUID-Wert für Ihr Azure-Abonnement. <p>**Tipp**: Sollten Sie Ihre Azure-Abonnement-ID nicht kennen, führen Sie an der Befehlszeile oder in Cloud Shell den folgenden Azure CLI-Befehl aus, und verwenden Sie dann den `id`-GUID-Wert: <p>`az account list` | 
   | **Client-ID** | <*yourAzureServicePrincipal-ID*> | Der `appId`-GUID-Wert, der zuvor für Ihren Azure-Dienstprinzipal generiert wurde. | 
   | **Geheimer Clientschlüssel** | <*yourSecurePassword*> | Der `password`-Wert (Geheimnis), den Sie für Ihren Azure-Dienstprinzipal angegeben haben. | 
   | **Tenant ID** | <*yourAzureActiveDirectoryTenant-ID*> | Der `tenant`-GUID-Wert für Ihren Azure Active Directory-Mandanten. | 
   | **ID** | <*yourAzureServicePrincipalName*> | Der `displayName`-Wert für Ihren Azure-Dienstprinzipal. | 

1. Wählen Sie **Verify Service Principal** (Dienstprinzipal überprüfen) aus, um zu überprüfen, ob Ihr Dienstprinzipal funktioniert. Wenn Sie fertig sind, wählen Sie **OK**.

Erstellen Sie als Nächstes die Jenkins-Pipeline, die Ihre App erstellt und bereitstellt.

## <a name="create-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline

Erstellen Sie in Jenkins den Pipelineauftrag für die Erstellung und Bereitstellung Ihrer App.

1. Kehren Sie zur Startseite von Jenkins zurück, und wählen Sie **New Item** (Neues Element) aus. 

   ![Erstellen einer Jenkins-Pipeline](media/deploy-from-github-to-azure-app-service/jenkins-select-new-item.png)

1. Geben Sie einen Namen für Ihren Pipelineauftrag an (beispielsweise „My-Java-Web-App“), und wählen Sie **Pipeline** aus. Wählen Sie unten **OK** aus.  

   ![Benennen des Jenkins-Pipelineauftrags](media/deploy-from-github-to-azure-app-service/jenkins-select-pipeline.png)

1. Richten Sie Jenkins mit Ihrem Dienstprinzipal ein, damit Jenkins Bereitstellungsvorgänge für Azure ausführen kann, ohne Ihre eigenen Anmeldeinformationen zu verwenden.

   1. Wählen Sie auf der Registerkarte **General** (Allgemein) die Option **Prepare an environment for the run** (Umgebung für die Ausführung vorbereiten) aus. 

   1. Fügen Sie im daraufhin angezeigten Feld **Properties Content** (Eigenschafteninhalt) die folgenden Umgebungsvariablen und deren Werte hinzu: 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Auswählen einer Umgebung für die Ausführung und Festlegen der Umgebungsvariablen](media/deploy-from-github-to-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

Erstellen Sie als Nächstes Build- und Bereitstellungsskripts für Jenkins.

## <a name="create-build-and-deployment-files"></a>Erstellen von Build- und Bereitstellungsdateien

Erstellen Sie nun die Dateien, die Jenkins für die Erstellung und Bereitstellung Ihrer App benötigt.

1. Erstellen Sie im Ordner `src/main/resources/` Ihres GitHub-Forks eine App-Konfigurationsdatei namens `web.config` mit dem folgenden XML-Code, und ersetzen Sie dabei `$(JAR_FILE_NAME)` durch `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. Erstellen Sie im Stammordner Ihres GitHub-Forks ein Build- und Bereitstellungsskript namens `Jenkinsfile` mit folgendem Text ([GitHub-Quelle](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Committen Sie die Dateien `web.config` und `Jenkinsfile` in Ihrem GitHub-Fork, und pushen Sie Ihre Änderungen.

## <a name="point-pipeline-at-script"></a>Verweisen der Pipeline auf das Skript

Geben Sie nun das Build- und Bereitstellungsskript an, das von Jenkins verwendet werden soll.

1. Wählen Sie in Jenkins Ihren zuvor erstellten Pipelineauftrag aus. 

   ![Auswählen des Jenkins-Pipelineauftrags für Ihre Web-App](media/deploy-from-github-to-azure-app-service/select-pipeline-job.png)

1. Wählen Sie im linken Menü die Option **Configure** (Konfigurieren) aus.

1. Wählen Sie auf der Registerkarte **Pipeline** in der Liste **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.

   1. Wählen Sie im daraufhin angezeigten Feld **SCM** die Option **Git** als Quellcodeverwaltung aus. 

   1. Geben Sie im Abschnitt **Repositories** (Repositorys) für **Repository URL** (Repository-URL) die URL Ihres GitHub-Forks ein. Beispiel: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Wählen Sie unter **Credentials** (Anmeldeinformationen) das persönliche Zugriffstoken für GitHub aus, das Sie zuvor erstellt haben.

   1. Fügen Sie im Feld **Script Path** (Skriptpfad) den Pfad des Skripts „Jenkinsfile“ hinzu.

   Danach sollte Ihre Pipelinedefinition wie im folgenden Beispiel aussehen: 

   ![Verweisen der Jenkins-Pipeline auf das Skript](media/deploy-from-github-to-azure-app-service/set-up-jenkins-github.png)

1. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

Erstellen Sie als Nächstes Ihre App, und stellen Sie sie in Azure App Service bereit. 

## <a name="build-and-deploy-to-azure"></a>Erstellen und Bereitstellen in Azure

1. Erstellen Sie unter Verwendung der Azure-Befehlszeilenschnittstelle (entweder über die Befehlszeile oder über Azure Cloud Shell) eine [Azure App Service-Web-App unter Linux](/azure/app-service/containers/app-service-linux-intro), bei der Jenkins Ihre Web-App nach Abschluss eines Buildvorgangs bereitstellt. Stellen Sie sicher, dass Ihre Web-App einen eindeutigen Namen hat.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Weitere Informationen zu diesen Azure CLI-Befehlen finden Sie auf den folgenden Seiten:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. Wählen Sie in Jenkins Ihren Pipelineauftrag und anschließend **Build Now** (Jetzt erstellen) aus.

   Nach Abschluss des Buildvorgangs stellt Jenkins Ihre App bereit, die nun in Azure unter der Veröffentlichungs-URL live geschaltet ist. Beispiel: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Anzeigen Ihrer bereitgestellten App in Azure](media/deploy-from-github-to-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Pushen von Änderungen und erneutes Bereitstellen

1. Navigieren Sie in Ihrem Webbrowser zum folgenden Ort im GitHub-Fork Ihrer Web-App:

   `complete/src/main/java/Hello/Application.java`
   
1. Wählen Sie in GitHub in der rechten oberen Ecke **Edit this file** (Diese Datei bearbeiten) aus.

1. Nehmen Sie folgende Änderung an der Methode `commandLineRunner()` vor, und committen Sie die Änderung im `master`-Branch des Repositorys. Durch diesen Commit im `master`-Branch wird ein Buildvorgang in Jenkins gestartet. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Aktualisieren Sie Ihre App nach Abschluss des Buildvorgangs und der erneuten Jenkins-Bereitstellung in Azure. Sie verfügt nun über das Update.

   ![Anzeigen Ihrer bereitgestellten App in Azure](media/deploy-from-github-to-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Behandeln von Problemen mit dem Jenkins-Plug-In

Sollten Fehler für das Jenkins-Plug-In auftreten, können Sie diese auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente melden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden von Azure-VMs als Build-Agents](/azure/jenkins/jenkins-azure-vm-agents)
