---
title: 'Tutorial: Durchführen der Bereitstellung für Azure App Service mit Jenkins und der Azure CLI'
description: Erfahren Sie, wie Sie mit der Azure CLI eine Java-Web-App an Azure in der Jenkins-Pipeline bereitstellen.
keywords: Jenkins, Azure, DevOps, App Service, CLI
ms.topic: tutorial
ms.date: 01/06/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: cac490a111120e7b390e26d020c5a6ad00fdd667
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109063"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Tutorial: Bereitstellen an Azure App Service mit Jenkins und der Azure CLI

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Für die Bereitstellung einer Java-Web-App in Azure können Sie die Azure CLI in einer [Jenkins-Pipeline](https://jenkins.io/doc/book/pipeline/) nutzen. In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Konfigurieren von Jenkins
> * eine Web-App in Azure erstellen
> * ein GitHub-Repository erstellen
> * Erstellen einer Jenkins-Pipeline
> * die Pipeline ausführen und die Web-App überprüfen

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Jenkins** - [Installieren von Jenkins auf einer Linux-VM](configure-on-linux-vm.md)
- **Azure CLI**: Installieren Sie die Azure CLI (Version 2.0.67 oder höher) auf dem Jenkins-Server.

## <a name="configure-jenkins"></a>Konfigurieren von Jenkins

In den folgenden Schritten wird gezeigt, wie Sie das erforderliche Java JDK und Maven auf dem Jenkins-Master installieren:

1. Melden Sie sich per SSH beim Jenkins-Master an.

1. [Laden Sie die Azul Zulu JDKs aus einem apt-get-Repository herunter, und installieren Sie sie:](/azure/developer/java/fundamentals/java-jdk-install#download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository)

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    sudo apt-get -q update
    sudo apt-get -y install zulu-8-azure-jdk
    ```
    
1. Führen Sie den folgenden Befehl aus, um Maven zu installieren:

    ```bash
    sudo apt-get install -y maven
    ```
    
## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Hinzufügen des Azure-Dienstprinzipals zu Jenkins-Anmeldeinformationen

In den folgenden Schritten wird gezeigt, wie Sie Ihre Azure-Anmeldeinformationen angeben:

1. Vergewissern Sie sich, dass das [Plug-In für Anmeldeinformationen](https://plugins.jenkins.io/credentials/) installiert ist.

1. Navigieren Sie auf dem Jenkins-Dashboard zu **Credentials > System** (Anmeldeinformationen > System). 

1. Wählen Sie **Global credentials (unrestricted)** (Globale Anmeldeinformationen (uneingeschränkt)) aus.

1. Wählen Sie zum [Hinzufügen eines Microsoft Azure-Dienstprinzipals](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json) die Option **Add Credentials** (Anmeldeinformationen hinzufügen) aus. Achten Sie darauf, dass als Typ für die Anmeldeinformationen **_Username with password_** (Benutzername mit Kennwort) ausgewählt ist, und geben Sie Folgendes ein:

    **Username** (Benutzername): Dienstprinzipal `appId`
    * **Kennwort**: Dienstprinzipal `password`
    * **ID:** Bezeichner für die Anmeldeinformationen (beispielsweise `AzureServicePrincipal`)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Erstellen eines Azure App Service für die Bereitstellung der Java-Web-App

Verwenden Sie [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create), um einen Azure App Service-Plan mit dem Tarif **FREE** zu erstellen:

```azurecli
az appservice plan create \
    --name <app_service_plan> \ 
    --resource-group <resource_group> \
    --sku FREE
```

**Hinweise**:

- Der App Service-Plan definiert die physischen Ressourcen, die zum Hosten Ihrer Apps verwendet werden.
- Alle einem App Service-Plan zugewiesenen Anwendungen teilen sich diese Ressourcen.
- App Service-Pläne ermöglichen Kosteneinsparungen beim Hosten mehrerer Apps.

## <a name="create-an-azure-web-app"></a>Erstellen einer Azure-Web-App

Verwenden Sie [az webapp create](/cli/azure/webapp#az-webapp-create), um eine Web-App-Definition im App Service-Plan `myAppServicePlan` zu erstellen.

```azurecli
az webapp create \
    --name <app_name> \ 
    --resource-group <resource_group> \
    --plan <app_service_plan>
```

**Hinweise**:

- Die Web-App-Definition enthält eine URL für den Zugriff auf Ihre Anwendung und konfiguriert verschiedene Optionen zum Bereitstellen Ihres Codes in Azure.
- Ersetzen Sie den Platzhalter `<app_name>` durch einen eindeutigen App-Namen.
- Der App-Name ist Teil des Standarddomänennamens für die Web-App. Daher muss er in Azure für alle Apps eindeutig sein.
- Sie können der Web-App einen benutzerdefinierten Domänennameneintrag zuordnen, bevor Sie sie für Ihre Benutzer verfügbar machen.


## <a name="configure-java"></a>Konfigurieren von Java

Verwenden Sie [az appservice web config update](/cli/azure/webapp/config), um die Java-Laufzeitkonfiguration für die App einzurichten:

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group <resource_group> \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>ein GitHub-Repository erstellen

1. Öffnen Sie das Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Einfache Java-Web-App für Azure).

1. Wählen Sie die Schaltfläche **Fork** (Forken) um das Repository in Ihr eigenes GitHub-Konto zu forken.

1. Öffnen Sie die Datei **Jenkinsfile**, indem Sie auf den Dateinamen klicken.

1. Wählen Sie das Stiftsymbol aus, um die Datei zu bearbeiten.

1. Aktualisieren Sie die Abonnement- und die Mandanten-ID.
    
    ```groovy
      withEnv(['AZURE_SUBSCRIPTION_ID=<subscription_id>',
            'AZURE_TENANT_ID=<tenant_id>']) 
    ```
    
1. Aktualisieren Sie in den Zeilen 22 und 23 die Ressourcengruppe und den Namen Ihrer Web-App.

    ```groovy
    def resourceGroup = '<resource_group>'
    def webAppName = '<app_name>'
    ```

1. Aktualisieren Sie die Anmeldeinformationen-ID in Ihrer Jenkins-Instanz.

    ```groovy
    withCredentials([usernamePassword(credentialsId: '<service_princial>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline

Gehen Sie zum Erstellen einer Jenkins-Pipeline wie folgt vor:

1. Öffnen Sie Jenkins in einem Webbrowser.

1. Wählen Sie **Neues Element** aus.

1. Geben Sie einen Namen für den Auftrag ein.

1. Wählen Sie **Pipeline** aus.

1. Klicken Sie auf **OK**.

1. Wählen Sie **Pipeline** aus.

1. Wählen Sie als **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.

1. Wählen Sie als **SCM** die Option **Git** aus.

1. Geben Sie die GitHub-URL für Ihr verzweigtes Repository ein: `https:\<forked_repo\>.git`.

1. Wählen Sie **Speichern** aus.

## <a name="test-your-pipeline"></a>Testen Ihrer Pipeline

1. Navigieren Sie zur erstellten Pipeline.

1. Wählen Sie **Build Now** (Jetzt erstellen) aus.

1. Wählen Sie nach Abschluss des Buildvorgangs die Option **Console Output** (Konsolenausgabe) aus, um die Builddetails anzuzeigen.

## <a name="verify-your-web-app"></a>Überprüfen Ihrer Web-App

Vergewissern Sie sich, dass die WAR-Datei erfolgreich für Ihre Web-App bereitgestellt wurde:

1. Navigieren Sie zur folgenden URL: `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`

1. Der Text sollte in etwa wie folgt aussehen:

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Navigieren Sie zur folgenden URL (ersetzen Sie &lt;x> und &lt;y> durch zwei zu summierende Werte): http://&lt;App-Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>.

    ![Beispiel für die Ausführung der Demo-Addierung](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-app-service-on-linux"></a>Bereitstellen in Azure App Service für Linux

Von App Service können Web-Apps für unterstützte Anwendungsstapel auch nativ unter Linux gehostet werden. Darüber hinaus können benutzerdefinierte Linux-Container ausgeführt werden. (Diese werden auch als Web-App für Container bezeichnet.)

Sie können das Skript ändern, um als Bereitstellungsziel eine Instanz von Azure App Service für Linux zu verwenden. Von App Service für Linux wird Docker unterstützt. Daher stellen Sie eine Dockerfile-Datei bereit, die Ihre Web-App mit Dienstlaufzeit in ein Docker-Image verpackt. Das Plug-In erstellt das Image, übermittelt es per Push an eine Docker-Registrierung und stellt das Image für Ihre Web-App bereit.

1. Informationen zum Erstellen einer Instanz von Azure App Service für Linux und einer Azure Container Registry-Instanz finden Sie unter [Migrieren benutzerdefinierter Software zu Azure App Service mithilfe eines benutzerdefinierten Containers](/azure/app-service/tutorial-custom-container?pivots=container-linux#configure-app-service-to-deploy-the-image-from-the-registry).

    ```azurecli
        az group create --name myResourceGroup2 --location westus2
        az acr create --name myACRName --resource-group myResourceGroup2 --sku Basic --admin-enabled true
        az appservice plan create --name myAppServicePlan --resource-group  myResourceGroup2 --is-linux
        az webapp create --resource-group myResourceGroup2 --plan myAppServicePlan --name myApp --deployment-container-image-name myACRName.azurecr.io/calculator:latest
    ```

1. [Installieren Sie Docker auf Ihrer Jenkins-Instanz](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. Vergewissern Sie sich, dass das [Docker-Pipeline-Plug-In](https://plugins.jenkins.io/docker-workflow/) installiert ist.

1. Bearbeiten Sie im Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample), das Sie verzweigt haben, die **Jenkinsfile2**-Datei wie folgt:

    1. Aktualisieren Sie die Abonnement- und die Mandanten-ID.

        ```groovy
         withEnv(['AZURE_SUBSCRIPTION_ID=<mySubscriptionId>',
                'AZURE_TENANT_ID=<myTenantId>']) {
        ```

    1. Aktualisieren Sie die Namen Ihrer Ressourcengruppe, Web-App und ACR-Instanz (ersetzen Sie die Platzhalter durch Ihre Werte).

        ```bash
        def webAppResourceGroup = '<resource_group>'
        def webAppName = '<app_name>'
        def acrName = '<registry>'
        ```

    1. Aktualisieren Sie `<azsrvprincipal\>` auf Ihre Anmeldeinformations-ID.

        ```bash
        withCredentials([usernamePassword(credentialsId: '<service_principal>', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
        ```

1. Erstellen Sie eine neue Jenkins-Pipeline wie bei der Bereitstellung für die Azure-Web-App unter Windows mit `Jenkinsfile2`.

1. Führen Sie den neuen Auftrag aus.

1. Führen Sie zur Überprüfung in der Azure CLI den folgenden Befehl aus:

    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```

    Die Ergebnisse sollten ähnlich wie hier aussehen:
    
    ```output
    [
    "calculator"
    ]
    ```
    
1. Navigieren Sie zu `http://<app_name>.azurewebsites.net/api/calculator/ping` (Platzhalter ersetzen). Das Ergebnis sollte etwa wie folgt aussehen: 

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Navigieren Sie zu `http://<app_name>.azurewebsites.net/api/calculator/add?x=<x>&y=<y>` (Platzhalter ersetzen). Die Werte, die Sie für `x` und `y` angeben, werden summiert und angezeigt.
    
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/jenkins)