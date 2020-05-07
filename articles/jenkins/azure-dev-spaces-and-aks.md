---
title: 'Tutorial: Verwenden von Azure Dev Spaces mit Azure Kubernetes Service'
description: Hier erfahren Sie, wie Sie das Azure Dev Spaces-Plug-In in einer Continuous Integration-Pipeline verwenden.
keywords: Jenkins, Azure, DevOps, Azure Dev Spaces, AKS, Azure Kubernetes Service
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 2528d362a9c95aecd00b938fdeed2756c8643fd4
ms.sourcegitcommit: 8309822d57f784a9c2ca67428ad7e7330bb5e0d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82861253"
---
# <a name="tutorial-use-azure-dev-spaces-with-azure-kubernetes-service"></a>Tutorial: Verwenden von Azure Dev Spaces mit Azure Kubernetes Service

Mit Azure Dev Spaces können Sie Ihre in Azure Kubernetes Service (AKS) ausgeführte Microserviceanwendung testen und iterativ entwickeln, ohne Abhängigkeiten replizieren oder simulieren zu müssen. Das Azure Dev Spaces-Plug-In für Jenkins unterstützt Sie bei der Verwendung von Dev Spaces in Ihrer CI/CD-Pipeline (Continuous Integration/Continuous Delivery).

In diesem Tutorial wird auch Azure Container Registry (ACR) verwendet. ACR dient zum Speichern von Images, und mit ACR Tasks werden Docker- und Helm-Artefakte erstellt. Wenn Sie Artefakte mithilfe von ACR und ACR Tasks generieren, müssen Sie auf Ihrem Jenkins-Server keine zusätzliche Software (wie etwa Docker) installieren. 

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Azure Dev Spaces-fähigen AKS-Clusters
> * Bereitstellen einer Anwendung mit mehreren Diensten in AKS
> * Vorbereiten Ihres Jenkins-Servers
> * Verwenden des Azure Dev Spaces-Plug-Ins in einer Jenkins-Pipeline, um eine Vorschau von Codeänderungen anzuzeigen, bevor sie im Projekt zusammengeführt werden

In diesem Tutorial werden gewisse Kenntnisse im Zusammenhang mit gängigen Azure-Diensten, AKS, ACR, Azure Dev Spaces, Jenkins-[Pipelines](https://jenkins.io/doc/book/pipeline/) und -Plug-Ins sowie GitHub vorausgesetzt. Darüber hinaus sind Grundkenntnisse im Zusammenhang mit Unterstützungstools wie kubectl und Helm hilfreich.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Ein GitHub-Konto. Sollten Sie über kein GitHub-Konto verfügen, erstellen Sie zunächst ein [kostenloses Konto](https://github.com/).

* [Visual Studio Code](https://code.visualstudio.com/download) mit installierter Erweiterung für [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)

* [Installation der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest) (Version 2.0.43 oder höher)

* Ein Jenkins-Masterserver. Sollten Sie noch nicht über einen Jenkins-Masterserver verfügen, stellen Sie [Jenkins](https://aka.ms/jenkins-on-azure) mithilfe dieser [Schnellstartanleitung](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template) in Azure bereit. 

* Der Jenkins-Server muss sowohl über Helm als auch über kubectl verfügen und für das Jenkins-Konto verfügbar sein, wie weiter unten in diesem Tutorial erläutert.

* VS Code, VS Code-Terminal oder WSL sowie Bash 


## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

In diesem Abschnitt werden folgende Azure-Ressourcen erstellt:

* Eine Ressourcengruppe für alle in diesem Tutorial verwendeten Azure-Ressourcen
* Einen AKS-Cluster ([Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/))
* Eine Instanz von [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR) zum Erstellen (mithilfe von ACR Tasks) und Speichern von Docker-Images

1. Erstellen Sie eine Ressourcengruppe.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Erstellen Sie einen AKS-Cluster. Erstellen Sie den AKS-Cluster in einer [Region, die Dev Spaces unterstützt](/azure/dev-spaces/about#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurieren Sie AKS für die Verwendung von Dev Spaces.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    In diesem Schritt wird die CLI-Erweiterung `azds` installiert.

4. Erstellen Sie eine Containerregistrierung.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Bereitstellen von Beispiel-Apps für den AKS-Cluster

In diesem Abschnitt richten Sie einen Entwicklungsbereich ein und stellen eine Beispielanwendung für den AKS-Cluster bereit, den Sie im vorherigen Abschnitt erstellt haben. Die Anwendung besteht aus zwei Teilen: *webfrontend* und *mywebapi*. Beide Komponenten werden in einem Entwicklungsbereich bereitgestellt. Im weiteren Verlauf dieses Tutorials wird ein Pull Request für „mywebapi“ übermittelt, um die CI-Pipeline in Jenkins auszulösen.

Weitere Informationen zur Verwendung von Azure Dev Spaces sowie zur Entwicklung mit mehreren Diensten mit Azure Dev Spaces finden Sie unter [Erste Schritte in Azure Dev Spaces mit Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java) sowie unter [Entwicklung mit mehreren Diensten mit Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). In diesen Tutorials werden zusätzliche Hintergrundinformationen bereitgestellt, die hier nicht enthalten sind.

1. Laden Sie das Repository https://github.com/Azure/dev-spaces aus GitHub herunter.

2. Öffnen Sie den Ordner `samples/java/getting-started/webfrontend` in VS Code. (Sie können alle Standardaufforderungen zum Hinzufügen von Debugressourcen oder zum Wiederherstellen des Projekts ignorieren.)

3. Aktualisieren Sie `/src/main/java/com/ms/sample/webfrontend/Application.java` wie folgt:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Klicken Sie auf **Ansicht** > **Terminal**, um das in VS Code integrierte Terminal zu öffnen.

5. Führen Sie den Befehl `azds prep` aus, um Ihre Anwendung für die Ausführung in einem Entwicklungsbereich vorzubereiten. Dieser Befehl muss unter `dev-spaces/samples/java/getting-started/webfrontend` ausgeführt werden, damit die Anwendung ordnungsgemäß vorbereitet wird:

    ```bash
    azds prep --public
    ```

    Mit dem Dev Spaces-CLI-Befehl `azds prep` werden Docker- und Kubernetes-Ressourcen mit Standardeinstellungen generiert. Diese Dateien werden für die gesamte Lebensdauer des Projekts gespeichert und können angepasst werden:

    * `./Dockerfile` und `./Dockerfile.develop` beschreiben das Containerimage der App sowie die Erstellung und Ausführung des Quellcodes im Container.
    * In einem [Helm-Diagramm](https://helm.sh/docs/topics/charts/) unter `./charts/webfrontend` wird veranschaulicht, wie der Container für Kubernetes bereitgestellt wird.
    * `./azds.yaml` ist die Konfigurationsdatei von Azure Dev Spaces.

    Weitere Informationen finden Sie unter [Funktionsweise und Konfiguration von Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Verwenden Sie den Befehl `azds up`, um die Anwendung in AKS zu erstellen und auszuführen:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Suchen Sie in der Konsolenausgabe nach Informationen zu der URL, die durch den Befehl `up` erstellt wurde. Sie weist folgendes Format auf:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Öffnen Sie diese URL in einem Browserfenster. Daraufhin sollte die Web-App angezeigt werden. Wenn der Container ausgeführt wird, wird die Ausgabe von `stdout` und `stderr` an das Terminalfenster gestreamt.

8. Als Nächstes wird *mywebapi* eingerichtet und bereitgestellt:

    1. Wechseln Sie zum Verzeichnis `dev-spaces/samples/java/getting-started/mywebapi`.

    2. Ausführen

        ```bash
        azds prep
        ```

    3. Ausführen

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Vorbereiten des Jenkins-Servers

In diesem Abschnitt bereiten Sie den Jenkins-Server für die Ausführung der exemplarischen CI-Pipeline vor.

* Installieren von Plug-Ins
* Installieren der Helm- und der Kubernetes-Befehlszeilenschnittstelle
* Hinzufügen von Anmeldeinformationen

### <a name="install-plug-ins"></a>Installieren von Plug-Ins

1. Melden Sie sich bei Ihrem Jenkins-Server an. Wählen Sie **Manage Jenkins > Manage Plugins** (Jenkins verwalten >Plug-Ins verwalten) aus.
2. Wählen Sie auf der Registerkarte **Available** (Verfügbar) die folgenden Plug-Ins aus:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject)
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)

    Sollten diese Plug-Ins nicht in der Liste enthalten sein, überprüfen Sie auf der Registerkarte **Installed** (Installiert), ob sie bereits installiert sind.

3. Wählen Sie zum Installieren der Plug-Ins die Option **Download now and install after restart** (Jetzt herunterladen und nach Neustart installieren) aus.

4. Starten Sie Ihren Jenkins-Server neu, um die Installation abzuschließen.

### <a name="install-helm-and-kubectl"></a>Installieren von Helm und kubectl

In der Beispielpipeline werden Helm und kubectl verwendet, um den Entwicklungsbereich bereitzustellen. Bei der Installation von Jenkins wird ein Administratorkonto namens *jenkins* erstellt. Sowohl Helm als auch kubectl müssen für den Jenkins-Benutzer zugänglich sein.

1. Stellen Sie eine SSH-Verbindung mit dem Jenkins-Master her. 

2. Wechseln Sie zum Benutzer `jenkins`:
    ```bash
    sudo su jenkins
    ```

3. Installieren Sie die Helm-CLI. Weitere Informationen finden Sie unter [Installing Helm](https://helm.sh/docs/using_helm/#installing-helm) (Installieren von Helm).

4. Installieren Sie kubectl. Weitere Informationen finden Sie unter [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Hinzufügen von Anmeldeinformationen zu Jenkins

1. Jenkins benötigt einen Azure-Dienstprinzipal für die Authentifizierung und den Zugriff auf Azure-Ressourcen. Wie Sie einen Dienstprinzipal erstellen, erfahren Sie im Abschnitt  [Erstellen eines Dienstprinzipals](https://docs.microsoft.com/azure/jenkins/deploy-from-github-to-azure-app-service#create-service-principal) des Bereitstellungstutorials für Azure App Service. Speichern Sie unbedingt eine Kopie der Ausgabe von `create-for-rbac`. Diese Informationen werden im nächsten Schritt benötigt. Die Ausgabe sieht in etwa wie folgt aus:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Fügen Sie in Jenkins unter Verwendung der Dienstprinzipalinformationen aus dem vorherigen Schritt Anmeldeinformationen vom Typ *Microsoft Azure-Dienstprinzipal* hinzu. Die Namen im folgenden Screenshot entsprechen der Ausgabe von `create-for-rbac`.

    Das Feld **ID** enthält den Jenkins-Anmeldeinformationsnamen für Ihren Dienstprinzipal. In diesem Beispiel wird der Wert von `displayName` (in diesem Fall: `xxxxxxxjenkinssp`) verwendet. Sie können aber auch einen anderen Text verwenden. Dieser Anmeldeinformationsname ist der Wert für die Umgebungsvariable „AZURE_CRED_ID“ im nächsten Abschnitt.

    ![Hinzufügen von Dienstprinzipal-Anmeldeinformationen zu Jenkins](media/azure-dev-spaces-and-aks/add-service-principal-credentials.png)

    Die Angabe einer **Beschreibung**ist optional. Eine ausführlichere Anleitung finden Sie im Abschnitt [Hinzufügen des Dienstprinzipals zu Jenkins](https://docs.microsoft.com/azure/jenkins/deploy-from-github-to-azure-app-service#add-service-principal-to-jenkins) des Bereitstellungstutorials für Azure App Service. 



3. Führen Sie den folgenden Befehl aus, um Ihre ACR-Anmeldeinformationen anzuzeigen:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Erstellen Sie eine Kopie der JSON-Ausgabe. Diese sollte in etwa wie folgt aussehen:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Fügen Sie in Jenkins Anmeldeinformationen vom Typ *Benutzername mit Kennwort* hinzu. Der **Benutzername** ist der Benutzername aus dem vorherigen Schritt (in diesem Beispiel: `acr01`). Das **Kennwort** ist der Wert für das erste Kennwort (in diesem Beispiel: `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`). Die **ID** dieser Anmeldeinformationen ist der Wert von „ACR_CRED_ID“.

5. Richten Sie AKS-Anmeldeinformationen ein. Fügen Sie in Jenkins Anmeldeinformationen vom Typ *Kubernetes-Konfiguration (kubeconfig)* hinzu, und verwenden Sie die Option für die Direkteingabe. Führen Sie den folgenden Befehl aus, um die Anmeldeinformationen für den Zugriff auf Ihren AKS-Cluster zu erhalten:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   Die **ID** dieser Anmeldeinformationen ist der Wert von „KUBE_CONFIG_ID“ im nächsten Abschnitt.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Das Szenario für die Beispielpipeline basiert auf einem Muster aus der Praxis: Ein Pull Request löst eine CI-Pipeline aus, die die vorgeschlagenen Änderungen erstellt und anschließend zum Testen und Überprüfen in einem Azure-Entwicklungsbereich bereitstellt. Abhängig vom Ergebnis der Überprüfung werden die Änderungen entweder zusammengeführt und in AKS bereitgestellt oder verworfen. Zum Schluss wird der Entwicklungsbereich entfernt.

Die Phasen in der CI-Pipeline werden durch die Jenkins-Pipelinekonfiguration und die Jenkinsfile definiert. Das folgende Flussdiagramm zeigt die durch die Jenkinsfile definierten Pipelinephasen und Entscheidungspunkte:

![Jenkins-Pipelinefluss](media/azure-dev-spaces-and-aks/jenkins-pipeline-flow.png)

1. Laden Sie unter [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi) eine geänderte Version des Projekts *mywebapi* herunter. Dieses Projekt enthält mehrere Dateien, die zum Erstellen einer Pipeline benötigt werden – einschließlich *Jenkinsfile*, *Dockerfiles* und Helm-Diagramm.

2. Melden Sie sich bei Jenkins an. Wählen Sie im Menü auf der linken Seite die Option **Add Item** (Element hinzufügen) aus.

3. Wählen Sie **Pipeline** aus, und geben Sie im Feld **Enter an item name** (Elementname eingeben) einen Namen ein. Wählen Sie **OK** aus. Daraufhin wird automatisch der Pipelinekonfigurationsbildschirm geöffnet.

4. Aktivieren Sie auf der Registerkarte **General** (Allgemein) das Kontrollästchen **Prepare an environment for the run** (Umgebung für die Ausführung vorbereiten). 

5. Aktivieren Sie die Kontrollkästchen **Keep Jenkins Environment Variables** (Jenkins-Umgebungsvariablen beibehalten) und **Keep Jenkins Build Variables** (Jenkins-Buildvariablen beibehalten).

6. Geben Sie im Feld **Properties Content** (Eigenschafteninhalt) die folgenden Umgebungsvariablen ein:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Mit den Beispielwerten aus den vorherigen Abschnitten sollte die Liste der Umgebungsvariablen in etwa wie folgt aussehen:

    ![Umgebungsvariablen für die Jenkins-Pipeline](media/azure-dev-spaces-and-aks/jenkins-pipeline-environment.png)

7. Wählen Sie unter **Pipeline > Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.
8. Wählen Sie in **SCM** die Option **Git** aus, und geben Sie die URL Ihres Repositorys ein.
9. Geben Sie unter **Branch Specifier** (Branchspezifizierer) Folgendes ein: `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Geben Sie SCM-Repository-URL und den Skriptpfad „Jenkinsfile“ an.
11. **Lightweight checkout** (Einfaches Auschecken) muss aktiviert sein.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Erstellen eines Pull Requests zum Auslösen der Pipeline

Für den dritten Schritt in diesem Abschnitt müssen Sie einen Teil der Jenkinsfile kommentieren. Andernfalls tritt beim Versuch, die neue und die alte Version nebeneinander anzuzeigen, ein 404-Fehler auf. Wenn Sie sich dafür entscheiden, den Pull Request zusammenzuführen, wird standardmäßig die gemeinsam genutzte Vorgängerversion von „mywebapi“ entfernt und durch die neue Version ersetzt. Ändern Sie die Jenkinsfile wie folgt, bevor Sie den ersten Schritt ausführen:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Ändern Sie `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`, und erstellen Sie anschließend einen Pull Request. Beispiel:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Melden Sie sich bei Jenkins an, wählen Sie den Pipelinenamen aus, und wählen Sie anschließend **Build Now** (Jetzt erstellen) aus. 

    Sie können auch einen *Webhook* festlegen, um die Jenkins-Pipeline automatisch auszulösen. Wenn ein Pull Request eingegeben wird, richtet GitHub eine POST-Anweisung an Jenkins, wodurch die Pipeline ausgelöst wird. Weitere Informationen zum Einrichten eines Webhooks finden Sie unter [Connect Jenkins to GitHub](deploy-from-github-to-azure-app-service.md#connect-jenkins-to-github) (Herstellen einer Verbindung zwischen Jenkins und GitHub).

3. Vergleichen Sie die Änderungen mit der aktuellen gemeinsam genutzten Version:

    1. Navigieren Sie in Ihrem Browser zur gemeinsam genutzten Version: `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. „TEST_ENDPOINT“ enthält die URL.

    2. Öffnen Sie eine weitere Registerkarte, und geben Sie die Entwicklungsbereichs-URL des Pull Requests ein. Sie ähnelt `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Der Link befindet sich unter **Build History > <Buildnummer> > Console Output** (Build History (Buildverlauf) > <Buildnummer> > Console Output (Konsolenausgabe)) für den Jenkins-Auftrag. Suchen Sie auf der Seite nach `aksapp` (oder nach `azdsprefix`, falls Sie sich nur das Präfix ansehen möchten).

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Erstellen der URL des untergeordneten Entwicklungsbereichs

Wenn Sie einen Pull Request übermitteln, erstellt Jenkins einen untergeordneten Entwicklungsbereich, der auf dem gemeinsam genutzten Entwicklungsbereich des Teams basiert, und führt den Code aus Ihrem Pull Request in diesem untergeordneten Entwicklungsbereich aus. Die URL des untergeordneten Entwicklungsbereichs hat das Format `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** wird im Rahmen der Pipelineausführung durch das Azure Dev Spaces-Plug-In mittels **devSpacesCreate** festgelegt:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` ist die URL der Web-Front-End-App, die Sie zuvor in [Schritt 7 von „Bereitstellen von Beispiel-Apps für den AKS-Cluster“](#test_endpoint) mithilfe von `azds up` bereitgestellt haben. Der Wert von `$env.TEST_ENDPOINT` wird in der Pipelinekonfiguration festgelegt. 

Der folgende Codeausschnitt zeigt die Verwendung der URL des untergeordneten Entwicklungsbereichs in der Phase `smoketest`. Der Code überprüft, ob der untergeordnete Entwicklungsbereich „TEST_ENDPOINT“ verfügbar ist. Falls ja, wird der Begrüßungstext in „stdout“ heruntergeladen:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Beispielanwendung nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die Azure-Ressourcen zu bereinigen:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [CI/CD mit Jenkins in Azure](deploy-from-github-to-aks.md)
