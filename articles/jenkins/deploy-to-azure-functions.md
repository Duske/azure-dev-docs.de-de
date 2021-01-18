---
title: 'Tutorial: Durchführen einer Bereitstellung in Azure Functions mit Jenkins'
description: Hier wird beschrieben, wie Sie mit dem Jenkins-Azure Functions-Plug-In Bereitstellungen in Azure Functions durchführen.
keywords: Jenkins, Azure, DevOps, Java, Azure Functions
ms.topic: tutorial
ms.date: 01/11/2021
ms.custom: devx-track-jenkins,devx-track-cli
ms.openlocfilehash: 51807b1a3038d17278a6015d387b84e68aac71f5
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109028"
---
# <a name="tutorial-deploy-to-azure-functions-using-jenkins"></a>Tutorial: Durchführen einer Bereitstellung in Azure Functions mit Jenkins

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

[Azure Functions](/azure/azure-functions/) ist ein serverloser Computedienst. Mit Azure Functions können Sie Code bedarfsgesteuert ausführen, ohne dass eine Infrastruktur für die Bereitstellung oder Verwaltung verwendet werden muss. In diesem Tutorial wird beschrieben, wie Sie mit dem Azure Functions-Plug-In eine Java-Funktion in Azure Functions bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- **Jenkins-Server**: Wenn Sie keinen Jenkins-Server installiert haben, hilft Ihnen der Artikel [Erstellen eines Jenkins-Servers in Azure](./configure-on-linux-vm.md) weiter.

## <a name="view-the-source-code"></a>Anzeigen des Quellcodes

Den Quellcode für dieses Tutorial finden Sie im [Visual Studio China-GitHub-Repository](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Erstellen einer Java-Funktion

Verwenden Sie zum Erstellen einer Java-Funktion mit dem Java-Runtimestapel entweder das [Azure-Portal](https://portal.azure.com) oder die [Azure CLI](/cli/azure/).

Die folgenden Schritte veranschaulichen, wie Sie mit der Azure CLI eine Java-Funktion erstellen:

1. Erstellen Sie eine Ressourcengruppe, und ersetzen Sie den Platzhalter **&lt;resource_group>** durch den Namen Ihrer Ressourcengruppe.

    ```azurecli
    az group create --name <resource_group> --location eastus
    ```

1. Erstellen Sie ein Azure-Speicherkonto, und ersetzen Sie die Platzhalter durch die entsprechenden Werte.
 
    ```azurecli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Erstellen Sie die Test-Funktions-App, und ersetzen Sie die Platzhalter durch die entsprechenden Werte.

    ```azurecli
    az functionapp create --resource-group <resource_group> --runtime java --consumption-plan-location eastus --name <function_app> --storage-account <storage_account> --functions-version 2
    ```

## <a name="prepare-jenkins-server"></a>Vorbereiten des Jenkins-Servers

In den folgenden Schritten wird beschrieben, wie Sie den Jenkins-Server vorbereiten:

1. Stellen Sie einen [Jenkins-Server](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins) bereit. Wenn Sie noch keine Instanz des installierten Jenkins-Servers installiert haben, hilft Ihnen die Anleitung im Artikel zum Thema [Erstellen eines Jenkins-Servers in Azure](./configure-on-linux-vm.md) weiter.

1. Melden Sie sich per SSH an der Jenkins-Instanz an.

1. Installieren Sie in der Jenkins-Instanz die Azure CLI (Version 2.0.67 oder höher).

1. Installieren Sie Maven mithilfe des folgenden Befehls:

    ```bash
    sudo apt install -y maven
    ```

1. Installieren Sie auf der Jenkins-Instanz die [Azure Functions Core Tools](/azure/azure-functions/functions-run-local), indem Sie an einer Eingabeaufforderung des Terminals die folgenden Befehle ausführen:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    cat /etc/apt/sources.list.d/dotnetdev.list
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools-3
    ```

1. Jenkins benötigt einen Azure-Dienstprinzipal zum Authentifizieren und Zugreifen auf Azure-Ressourcen. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Tutorial: Bereitstellen über GitHub in Azure App Service mit Continuous Integration und Continuous Deployment von Jenkins](./deploy-from-github-to-azure-app-service.md).

1. Vergewissern Sie sich, dass das [Plug-In für Anmeldeinformationen](https://plugins.jenkins.io/credentials/) installiert ist.

    1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

    1. Wählen Sie unter **System Configuration** (Systemkonfiguration) die Option **Manage Plugins** (Plug-Ins verwalten) aus.

    1. Wählen Sie die Registerkarte **Installiert** aus.

    1. Geben Sie im Feld **Filter** den Text `credentials` ein.
    
    1. Vergewissern Sie sich, dass das **Plug-In für Anmeldeinformationen** installiert ist. Falls nicht, installieren Sie es über die Registerkarte **Available** (Verfügbar).

    ![Das Plug-In für Anmeldeinformationen muss installiert sein.](./media/deploy-to-azure-functions/credentials-plugin.png)

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **Security** (Sicherheit) die Option **Manage Credentials** (Anmeldeinformationen verwalten) aus.

1. Wählen Sie unter **Credentials** (Anmeldeinformationen) die Option **(global)** aus.

1. Wählen Sie im Menü die Option **Add Credentials** (Anmeldeinformationen hinzufügen) aus.

1. Geben Sie die folgenden Werte für Ihren [Microsoft Azure-Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%252fazure%252fazure-resource-manager%252ftoc.json) ein:

    - **Variante**: Vergewissern Sie sich, dass die Variante auf **_Username with password_** (Benutzername mit Kennwort) festgelegt ist.
    - _*Username*_ (Benutzername): App-ID (_ *_appId_*_) des erstellten Dienstprinzipals.
    - _*Password*_ (Kennwort): Kennwort (_ *_password_*_) des erstellten Dienstprinzipals.
    - _*ID*_: Bezeichner für die Anmeldeinformationen (beispielsweise `as azuresp`).

1. Klicken Sie auf **OK**.

## <a name="fork-the-sample-github-repo"></a>Forken des GitHub-Beispielrepositorys

1. [Melden Sie sich beim GitHub-Repository für die Beispiel-App für „ungerade“ oder „gerade“ an](https://github.com/VSChina/odd-or-even-function.git).

1. Wählen Sie in GitHub in der rechten oberen Ecke **Fork** aus.

1. Folgen Sie den Anweisungen, um Ihr GitHub-Konto auszuwählen und das Forken abzuschließen.

## <a name="create-a-jenkins-pipeline"></a>Erstellen einer Jenkins-Pipeline

In diesem Abschnitt erstellen Sie die [Jenkins-Pipeline](https://jenkins.io/doc/book/pipeline/).

1. Erstellen Sie im Jenkins-Dashboard eine Pipeline.

1. Aktivieren Sie **Prepare an environment for the run** (Umgebung für die Ausführung vorbereiten).

1. Wählen Sie im Abschnitt **Pipeline > Definition** die Option **Pipeline script from SCM** (Pipeline-Skript von SCM).

1. Geben Sie die URL Ihres GitHub-Forks und den Skriptpfad („doc/resources/jenkins/JenkinsFile“) für die Verwendung im [JenkinsFile-Beispiel](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile) ein.

   ```nodejs
    node {
    withEnv(['AZURE_SUBSCRIPTION_ID=99999999-9999-9999-9999-999999999999',
            'AZURE_TENANT_ID=99999999-9999-9999-9999-999999999999']) {
        stage('Init') {
            cleanWs()
            checkout scm
        }

        stage('Build') {
            sh 'mvn clean package'
        }

        stage('Publish') {
            def RESOURCE_GROUP = '<resource_group>' 
            def FUNC_NAME = '<function_app>'
            // login Azure
            withCredentials([usernamePassword(credentialsId: 'azuresp', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
            sh '''
                az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
                az account set -s $AZURE_SUBSCRIPTION_ID
            '''
            }
            sh 'cd $PWD/target/azure-functions/odd-or-even-function-sample && zip -r ../../../archive.zip ./* && cd -'
            sh "az functionapp deployment source config-zip -g $RESOURCE_GROUP -n $FUNC_NAME --src archive.zip"
            sh 'az logout'
            }
        }
    }
    ```

## <a name="build-and-deploy"></a>Erstellen und Bereitstellen

Nun kann der Jenkins-Auftrag ausgeführt werden.

1. Rufen Sie zuerst den Autorisierungsschlüssel ab, indem Sie die Anleitung im Artikel [HTTP-Trigger und -Bindungen in Azure Functions](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys) verwenden.

1. Geben Sie in Ihrem Browser die URL der App ein. Ersetzen Sie die Platzhalter durch die entsprechenden Werte, und geben Sie einen numerischen Wert für **&lt;input_number>** als Eingabe für die Java-Funktion an.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Es werden Ergebnisse angezeigt, die der folgenden Beispielausgabe ähneln (zu Testzwecken wurde die ungerade Zahl 365 verwendet):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie die von Ihnen erstellten Ressourcen wie folgt löschen:

```azurecli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure-Funktionen](/azure/azure-functions/)