---
title: 'Tutorial: Durchführen der Bereitstellung für Azure App Service mit Jenkins und der Azure CLI'
description: Erfahren Sie, wie Sie mit der Azure CLI eine Java-Web-App an Azure in der Jenkins-Pipeline bereitstellen.
keywords: Jenkins, Azure, DevOps, App Service, CLI
ms.topic: tutorial
ms.date: 04/25/2020
ms.openlocfilehash: 03eb896717350a242fb2f504a4da9b57ecc8a209
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169886"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Tutorial: Durchführen der Bereitstellung für Azure App Service mit Jenkins und der Azure CLI

Für die Bereitstellung einer Java-Web-App in Azure können Sie die Azure CLI in einer [Jenkins-Pipeline](https://jenkins.io/doc/book/pipeline/) nutzen. In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Konfigurieren von Jenkins
> * eine Web-App in Azure erstellen
> * ein GitHub-Repository erstellen
> * Erstellen einer Jenkins-Pipeline
> * die Pipeline ausführen und die Web-App überprüfen

## <a name="create-and-configure-jenkins-instance"></a>Erstellen und Konfigurieren einer Jenkins-Instanz

Wenn Sie noch nicht über einen Jenkins-Master verfügen, können Sie die [Jenkins-Lösungsvorlage](configure-on-linux-vm.md) für die Installation verwenden. Standardmäßig wird mit der Vorlage das erforderliche Plug-In für die [Azure-Anmeldeinformationen](https://plugins.jenkins.io/azure-credentials) installiert. 

Das Azure-Anmeldeinformationen-Plug-In ermöglicht Ihnen das Speichern von Anmeldeinformationen des Microsoft Azure-Dienstprinzipals in Jenkins. In Version 1.2 haben wir Support hinzugefügt, durch den die Jenkins-Pipeline die Azure-Anmeldeinformationen abrufen kann. 

Stellen Sie sicher, dass Sie über Version 1.2 oder höher verfügen:

* Klicken Sie im Jenkins-Dashboard auf **Manage Jenkins -> Plugin Manager ->** (Jenkins verwalten -> Plug-In-Manager ->), und suchen Sie nach **Azure-Anmeldeinformationen**. 
* Aktualisieren Sie das Plug-In, wenn die Version älter als 1.2 ist.

Java JDK und Maven werden auch im Jenkins-Master benötigt. Melden Sie sich zum Installieren mithilfe von SSH beim Jenkins-Master an, und führen Sie die folgenden Befehle aus:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Hinzufügen des Azure-Dienstprinzipals zu Jenkins-Anmeldeinformationen

Eine Azure-Anmeldeinformation ist erforderlich, um die Azure CLI auszuführen.

* Klicken Sie im Jenkins-Dashboard auf **Credentials -> System ->** (Anmeldeinformationen -> System ->). Klicken Sie auf **Global credentials (unrestricted)** (Globale Anmeldeinformationen (uneingeschränkt)).
* Klicken Sie auf **Add Credentials** (Anmeldeinformationen hinzufügen), um einen [Microsoft Azure-Dienstprinzipal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) hinzuzufügen, indem Sie die Abonnement-ID, die Client-ID, den geheimen Clientschlüssel und den OAuth 2.0-Token-Endpunkt hinzufügen. Stellen Sie eine ID zur Verwendung im nächsten Schritt bereit.

![Hinzufügen von Anmeldeinformationen](./media/deploy-to-azure-app-service-using-azure-cli/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Erstellen eines Azure App Service für die Bereitstellung der Java-Web-App

Erstellen Sie mit dem CLI-Befehl [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) einen Azure App Service-Plan mit dem Tarif **FREE**. Der App Service-Plan definiert die physischen Ressourcen, die zum Hosten Ihrer Apps verwendet werden. Alle einem App Service-Plan zugewiesenen Anwendungen teilen sich diese Ressourcen. Das spart Kosten, wenn Sie mehrere Apps hosten. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Wenn der Plan fertig ist, zeigt die Azure CLI eine ähnliche Ausgabe wie im folgenden Beispiel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Erstellen einer Azure-Web-App

 Erstellen Sie mit dem CLI-Befehl [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) eine Web-App-Definition im App Service-Plan `myAppServicePlan`. Die Web-App-Definition enthält eine URL für den Zugriff auf Ihre Anwendung und konfiguriert verschiedene Optionen zum Bereitstellen Ihres Codes in Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Ersetzen Sie den Platzhalter `<app_name>` durch Ihren eigenen eindeutigen App-Namen. Dieser eindeutige Name ist Teil des Standarddomänennamens für die Web-App. Daher muss er für alle Apps in Azure eindeutig sein. Sie können der Web-App einen benutzerdefinierten Domänennameneintrag zuordnen, bevor Sie sie für Ihre Benutzer verfügbar machen.

Wenn die Web-App-Definition fertig ist, zeigt die Azure CLI Informationen wie im folgenden Beispiel an: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Konfigurieren von Java

Richten Sie mit dem Befehl [az appservice web config update](/cli/azure/webapp/config) die für Ihre App erforderliche Java-Laufzeitkonfiguration ein.

Der folgende Befehl konfiguriert die Web-App für die Ausführung mit einer aktuellen Java 8 JDK-Version und [Apache Tomcat](https://tomcat.apache.org/) 8.0.

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>ein GitHub-Repository erstellen

1. Öffnen Sie das Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample) (Einfache Java-Web-App für Azure). Klicken Sie auf die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke, um das Repository in Ihr GitHub-Konto zu verzweigen.

1. Öffnen Sie in der GitHub-Webbenutzeroberfläche die **Jenkinsfile**-Datei. Klicken Sie auf das Stiftsymbol, um diese Datei zu bearbeiten und die Ressourcengruppe und den Namen Ihrer Web-App in Zeile 20 bzw. 21 zu aktualisieren.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<app_name>'
    ```
    
1. Ändern Sie Zeile 23, um die Anmeldeinformationen-ID in Ihrer Jenkins-Instanz zu aktualisieren.

    ```java
    withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline

Öffnen Sie Jenkins in einem Webbrowser, und klicken Sie auf **New Item** (Neues Element).

1. Geben Sie einen Namen für den Auftrag ein.
1. Wählen Sie **Pipeline** aus. 
1. Klicken Sie auf **OK**.
1. Wählen Sie **Pipeline** aus.
1. Wählen Sie als **Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM) aus.
1. Wählen Sie als **SCM** die Option **Git** aus.
1. Geben Sie die GitHub-URL für Ihr verzweigtes Repository ein: `https:\<your forked repo\>.git`.
1. Wählen Sie **Speichern** aus.

## <a name="test-your-pipeline"></a>Testen Ihrer Pipeline

1. Navigieren Sie zur erstellten Pipeline.
1. Klicken Sie auf **Build now** (Jetzt erstellen).
1. Wählen Sie nach Abschluss des Buildvorgangs die Option **Console Output** (Konsolenausgabe) aus, um die Builddetails anzuzeigen.

## <a name="verify-your-web-app"></a>Überprüfen Ihrer Web-App

Gehen Sie wie unten angegeben vor, um zu überprüfen, ob die Bereitstellung der WAR-Datei für Ihre Web-App erfolgreich war. 

1. Öffnen Sie einen Webbrowser:

1. Navigieren Sie zu `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`.

1. Der Text sollte in etwa wie folgt aussehen:

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Gehen Sie zu http://&lt;App_Name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (ersetzen Sie &lt;x> und &lt;y> durch beliebige Zahlen), um die Summe aus x und y zu erhalten.

    ![Rechner: Addieren](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Bereitstellen an Azure-Web-App unter Linux

Wenn Sie die Nutzung der Azure CLI in Ihrer Jenkins-Pipeline eingerichtet haben, können Sie das Skript zur Bereitstellung für eine Azure-Web-App unter Linux ändern. Für Web-Apps unter Linux wird Docker unterstützt. Daher stellen Sie eine Dockerfile-Datei bereit, die Ihre Web-App mit Dienstlaufzeit in ein Docker-Image verpackt. Das Plug-In erstellt das Image, übermittelt es per Push an eine Docker-Registrierung und stellt das Image für Ihre Web-App bereit.

1. [Erstellen Sie eine Azure-Web-App, die unter Linux ausgeführt wird](/azure/app-service/containers/quickstart-nodejs.md).

1. [Installieren Sie Docker auf Ihrer Jenkins-Instanz](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. [Erstellen Sie eine Containerregistrierung im Azure-Portal](/azure/container-registry/container-registry-get-started-azure-cli).

1. Bearbeiten Sie im Repository [Simple Java Web App for Azure](https://github.com/azure-devops/javawebappsample), das Sie verzweigt haben, die **Jenkinsfile2**-Datei wie folgt:

    1. Aktualisieren Sie die Namen Ihrer Ressourcengruppe, Web-App und ACR-Instanz (ersetzen Sie die Platzhalter durch Ihre Werte).

        ```bash
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    1. Aktualisieren Sie `<azsrvprincipal\>` auf Ihre Anmeldeinformations-ID.

        ```bash
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
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