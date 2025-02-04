---
title: Bereitstellen von Apps in Azure Spring Cloud mithilfe von Jenkins und der Azure CLI
description: Hier erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle in einer Continuous Integration- und Deployment-Pipeline verwenden, um Microservice für den Azure Spring Cloud-Dienst bereitzustellen.
keywords: Jenkins, Azure, DevOps, Azure Spring Cloud, Azure CLI
ms.topic: tutorial
ms.date: 11/10/2020
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: ef078589506af95fc05bfd7a85f48757f3cb0242
ms.sourcegitcommit: 0eb25e1fdafcd64118843748dc061f60e7e48332
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625990"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Tutorial: Bereitstellen von Apps in Azure Spring Cloud mithilfe von Jenkins und der Azure-Befehlszeilenschnittstelle

[Azure Spring Cloud](/azure/spring-cloud/spring-cloud-overview) ist eine vollständig verwaltete Microserviceentwicklung mit integrierter Dienstermittlung und Konfigurationsverwaltung. Der Dienst vereinfacht die Bereitstellung Spring Boot-basierter Microserviceanwendungen in Azure. In diesem Tutorial erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle in Jenkins verwenden, um Continuous Integration und Continuous Delivery (CI/CD) für Azure Spring Cloud zu automatisieren.

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Bereitstellen einer Dienstinstanz und Starten einer Java Spring-Anwendung
> * Vorbereiten Ihres Jenkins-Servers
> * Verwenden der Azure-Befehlszeilenschnittstelle in einer Jenkins-Pipeline, um die Microserviceanwendungen zu erstellen und bereitzustellen 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Jenkins**: [Installieren von Jenkins auf einer Linux-VM](configure-on-linux-vm.md)
- **GitHub-Konto:** Sollten Sie über kein GitHub-Konto verfügen, erstellen Sie zunächst ein [kostenloses Konto](https://github.com/).

## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Bereitstellen einer Dienstinstanz und Starten einer Java Spring-Anwendung

Wir verwenden [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) als exemplarische Microsoft-Dienstanwendung und gehen wie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) beschrieben vor, um die Dienstinstanz bereitzustellen und die Anwendungen einzurichten. Falls Sie diesen Prozess bereits durchlaufen haben, können Sie direkt mit dem nächsten Abschnitt fortfahren. Andernfalls finden Sie im Anschluss die Azure CLI-Befehle. Weitere Hintergrundinformationen finden Sie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli).

Ihr lokaler Computer muss die gleichen Voraussetzungen erfüllen wie der Jenkins-Buildserver. Zum Erstellen und Bereitstellen der Microserviceanwendungen muss Folgendes installiert sein:

* [Git-Client](https://git-scm.com/)
* [JDK 8](/java/azure/jdk)
* [Maven 3.0 oder höher](https://maven.apache.org/download.cgi)
* [Installation der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (Version 2.0.67 oder höher)

1. Installieren Sie die Azure Spring Cloud-Erweiterung:

    ```azurecli
    az extension add --name spring-cloud
    ```

1. Erstellen Sie eine Ressourcengruppe für Ihren Azure Spring Cloud-Dienst:

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

1. Stellen Sie eine Instanz von Azure Spring Cloud bereit:

    ```azurecli
    az spring-cloud create -n <service name> -g <resource group name>
    ```

1. Forken Sie das Repository [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) zu Ihrem eigenen GitHub-Konto. Klonen Sie das Repository auf Ihrem lokalen Computer in einem Verzeichnis mit dem Namen `source-code`:

    ```bash
    mkdir source-code
    git clone https://github.com/<your GitHub id>/piggymetrics
    ```

1. Richten Sie Ihren Konfigurationsserver ein. Ersetzen Sie „&lt;your GitHub id&gt;“ durch den korrekten Wert.

    ```azurecli
    az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

1. Erstellen Sie das Projekt:

    ```bash
    cd piggymetrics
    mvn clean package -D skipTests
    ```

1. Erstellen Sie die drei Microservices **gateway**, **auth-service** und **account-service**:

    ```azurecli
    az spring-cloud app create --n gateway -s <service name> -g <resource group name>
    az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
    az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

1. Stellen Sie die Anwendungen bereit:

    ```azurecli
    az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

1. Weisen Sie den öffentlichen Endpunkt dem Gateway zu:

    ```azurecli
    az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

1. Fragen Sie die URL der Gatewayanwendung ab, um überprüfen zu können, ob die Anwendung ausgeführt wird.

    ```azurecli
    az spring-cloud app show --name gateway | grep url
    ```
    
 1. Navigieren Sie zu der URL, die mit dem vorherigen Befehl zurückgegeben wurde, um die PiggyMetrics-Anwendung auszuführen.

## <a name="prepare-jenkins-server"></a>Vorbereiten des Jenkins-Servers

In diesem Abschnitt wird der Jenkins-Server für die Ausführung eines Builds vorbereitet. Dies ist zu Testzwecken ausreichend. Aus Sicherheitsgründen sollten Sie jedoch einen [Azure-VM-Agent](https://plugins.jenkins.io/azure-vm-agents) oder den [Azure Container-Agent](https://plugins.jenkins.io/azure-container-agents) verwenden, um einen Agent zum Ausführen Ihrer Builds in Azure zu starten. Weitere Informationen finden Sie im Jenkins-Artikel [Sicherheitsaspekte beim Erstellen auf Masterknoten](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) (in englischer Sprache).

### <a name="install-plug-ins"></a>Installieren von Plug-Ins

1. Melden Sie sich bei Ihrem Jenkins-Server an. Wählen Sie **Manage Jenkins > Manage Plugins** (Jenkins verwalten >Plug-Ins verwalten) aus.

1. Wählen Sie auf der Registerkarte **Available** (Verfügbar) die folgenden Plug-Ins aus:

    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)
    * [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

    Sollten diese Plug-Ins nicht in der Liste enthalten sein, überprüfen Sie auf der Registerkarte **Installed** (Installiert), ob sie bereits installiert sind.

1. Wählen Sie zum Installieren der Plug-Ins die Option **Download now and install after restart** (Jetzt herunterladen und nach Neustart installieren) aus.

1. Starten Sie Ihren Jenkins-Server neu, um die Installation abzuschließen.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Hinzufügen Ihrer Anmeldeinformationen für den Azure-Dienstprinzipal im Jenkins-Anmeldeinformationsspeicher

1. Für Bereitstellungen in Azure ist ein Azure-Dienstprinzipal erforderlich. Weitere Informationen finden Sie im Abschnitt [Erstellen eines Dienstprinzipals](./deploy-to-azure-app-service-using-azure-cli.md#add-azure-service-principal-to-a-jenkins-credential) des Bereitstellungstutorials für Azure App Service. Die Ausgabe von `az ad sp create-for-rbac` sieht in etwa wie folgt aus:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

1. Wählen Sie im Jenkins-Dashboard die Option **Anmeldeinformationen** > **System** aus. Wählen Sie anschließend **Globale Anmeldeinformationen (uneingeschränkt)** aus.

1. Wählen Sie **Add Credentials** (Anmeldeinformationen hinzufügen) aus.

1. Wählen Sie den Typ **Microsoft Azure Service Principal** (Microsoft Azure-Dienstprinzipal) aus.

1. Geben Sie Werte für die folgenden Felder ein:

    - **Abonnement-ID**: Azure-Abonnement-ID
    - **Client-ID**: App-ID des Dienstprinzipals
    - **Geheimer Clientschlüssel**: Kennwort des Dienstprinzipals
    - **Mandanten-ID**: Mandanten-ID des Microsoft-Kontos
    - **Azure-Umgebung**: Wählen Sie den passenden Wert für Ihre Umgebung aus. Verwenden Sie beispielsweise **Azure** für Azure Global.
    - **ID:** Legen Sie `azure_service_principal` als Wert fest. Wir verwenden diese ID in einem späteren Schritt in diesem Artikel.
    - **Beschreibung**: Diese ist optional, wird aber empfohlen.
    
### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Installieren von Maven und der Azure CLI-Erweiterung für Spring Cloud

Die Beispielpipeline verwendet Maven zum Erstellen und die Azure-Befehlszeilenschnittstelle zum Bereitstellen der Dienstinstanz. Bei der Installation von Jenkins wird ein Administratorkonto namens *jenkins* erstellt. Stellen Sie sicher, dass der Benutzer *jenkins* zum Ausführen der Spring Cloud-Erweiterung berechtigt ist.

1. Stellen Sie eine SSH-Verbindung mit dem Jenkins-Controller her.

1. Installieren Sie Maven.

    ```bash
    sudo apt-get install maven
    ```

1. Überprüfen Sie, ob die Azure CLI installiert ist, indem Sie `az version` eingeben. Wenn die Azure CLI nicht installiert ist, finden Sie weitere Informationen unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

1. Wechseln Sie zum Benutzer `jenkins`:

    ```bash
    sudo su jenkins
    ```

1. Fügen Sie die Erweiterung **spring-cloud** hinzu:

    ```bash
    az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Erstellen einer Jenkinsfile

1. Erstellen Sie in Ihrem eigenen Repository (https://github.com/&lt ;Ihre GitHub-ID&gt; /piggymetrics), eine **Jenkinsfile** im Stammverzeichnis.

1. Aktualisieren Sie die Datei wie folgt. Ersetzen Sie die Werte von **\<resource group name>** und **\<service name>** . Falls Sie beim Hinzufügen der Anmeldeinformationen in Jenkins einen anderen Wert verwendet haben, ersetzen Sie **azure_service_principal** durch die korrekte ID.

   ```groovy
       node {
         stage('init') {
           checkout scm
         }
         stage('build') {
           sh 'mvn clean package'
         }
         stage('deploy') {
           withCredentials([azureServicePrincipal('azure_service_principal')]) {
             // login to Azure
             sh '''
               az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
               az account set -s $AZURE_SUBSCRIPTION_ID
             '''  
             // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
             sh 'az configure --defaults group=<resource group name>'
             sh 'az configure --defaults spring-cloud=<service name>'
             // Deploy applications
             sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
             sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
             sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
             sh 'az logout'
           }
         }
       }
   ```

1. Speichern und committen Sie die Änderung.

## <a name="create-the-job"></a>Erstellen des Auftrags

1. Klicken Sie auf dem Jenkins-Dashboard auf **New Item** (Neues Element).

1. Geben Sie einen Namen (*Deploy-PiggyMetrics*) für den Auftrag an, und wählen Sie **Pipeline** aus. Klicken Sie auf OK.

1. Klicken Sie dann auf die Registerkarte **Pipeline**.

1. Wählen Sie als **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.

1. Wählen Sie als **SCM** die Option **Git** aus.

1. Geben Sie die GitHub-URL für Ihr geforktes Repository ein: **https://github.com/&lt ;Ihre GitHub-ID&gt; /piggymetrics.git**

1. Vergewissern Sie sich, dass **Branch Specifier (black for 'any')** (Branchspezifizierer (schwarz für „beliebig“)) auf **_/Azure_* festgelegt ist.

1. Behalten Sie für **Script Path** (Skriptpfad) die Einstellung **Jenkinsfile** bei.

1. Klicken Sie unten auf der Seite auf **Speichern**.

## <a name="validate-and-run-the-job"></a>Überprüfen und Ausführen des Auftrags

Vor dem Ausführen des Auftrags ändern wir noch den Text im Eingabefeld für die Anmeldung in **enter login ID** (Anmelde-ID eingeben).

1. Öffnen Sie hierzu in Ihrem eigenen Repository `index.html` (unter **/gateway/src/main/resources/static/** ).

1. Suchen Sie nach „enter your login“ (Anmeldename eingeben), und ändern Sie den Text in „enter login ID“ (Anmelde-ID eingeben).

    ```HTML
    <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

1. Übergeben der Änderungen

1. Führen Sie den Auftrag manuell in Jenkins aus. Klicken Sie auf dem Jenkins-Dashboard auf den Auftrag *Deploy-PiggyMetrics* und anschließend auf **Build Now** (Jetzt erstellen).

Navigieren Sie nach Abschluss des Auftrags zur öffentlichen IP-Adresse der Anwendung **gateway**, und vergewissern Sie sich, dass die Anwendung aktualisiert wurde. 

![Aktualisierte Version von Piggy Metrics](./media/deploy-to-azure-spring-cloud-using-azure-cli/piggymetrics.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/jenkins/)