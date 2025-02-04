---
title: Verbinden von GitHub und Azure
description: Ressourcen zum Herstellen einer Verbindung mit GitHub aus Azure und anderen Diensten
author: N-Usha
ms.author: ushan
ms.topic: reference
ms.service: azure
ms.date: 02/17/2021
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 136e11c6059ab8c25af85212f0f0f96b88652a56
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118238"
---
# <a name="use-github-actions-to-connect-to-azure"></a>Verwenden von GitHub Actions zum Herstellen einer Verbindung mit Azure

Informieren Sie sich über die Verwendung der [Azure-Anmeldung](https://github.com/Azure/login) mit [Azure PowerShell](https://github.com/Azure/PowerShell) oder [Azure CLI](https://github.com/Azure/CLI), um die Interaktion mit Ihren Azure-Ressourcen zu ermöglichen.

Für die Verwendung von Azure PowerShell oder der Azure CLI in einem GitHub Actions-Workflow müssen Sie sich zuerst mit der Aktion [Azure-Anmeldung](https://github.com/marketplace/actions/azure-login) anmelden.
Mit der Aktion „Azure-Anmeldung“ können Sie Befehle in einem Workflow im Kontext eines [Azure AD-Dienstprinzipals](/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) ausführen.

Standardmäßig meldet sich die Aktion über die Azure CLI an und richtet die Ausführungsumgebung für GitHub-Aktionen für die Azure CLI ein. Azure PowerShell kann mithilfe der Eigenschaft `enable-AzPSSession` der Azure-Anmeldeaktion genutzt werden. Dadurch wird die Ausführungsumgebung für GitHub-Aktionen mit dem Azure PowerShell-Modul eingerichtet.

Sie können die Azure-Anmeldung verwenden, um eine Verbindung mit öffentlichen oder unabhängigen (Sovereign) Clouds herzustellen, z. B. Azure Government und Azure Stack Hub.

## <a name="create-a-service-principal-and-add-it-to-github-secret"></a>Erstellen eines Dienstprinzipals und Hinzufügen zum GitHub-Geheimnis

Zur Verwendung der [Azure-Anmeldung](https://github.com/marketplace/actions/azure-login) müssen Sie Ihren Azure-Dienstprinzipal zunächst Ihrem GitHub-Repository als Geheimnis hinzufügen.

In diesem Beispiel erstellen Sie ein Geheimnis mit dem Namen `AZURE_CREDENTIALS`, das Sie für die Authentifizierung bei Azure verwenden können.  

1. Falls Sie nicht über eine bereits vorhandene Anwendung verfügen, sollten Sie eine [neue Active Directory-Anwendung](/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal&preserve-view=true) für die Verwendung mit Ihrem Dienstprinzipal registrieren.

    ```azurecli-interactive
        appName="myApp"

        az ad app create \
        --display-name $appName \
        --homepage "http://localhost/$appName" \
        --identifier-uris http://localhost/$appName
    ```

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview) im Azure-Portal oder lokal die [Azure CLI](/cli/azure/install-azure-cli).

    > [!NOTE]
    > Bei Verwendung von Azure Stack Hub müssen Sie Ihren SQL Management-Endpunkt auf `not supported` festlegen.
    > `az cloud update -n {environmentName} --endpoint-sql-management https://notsupported`

1. [Erstellen Sie im Azure-Portal für Ihre App einen neuen Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli). Dem Dienstprinzipal muss die Rolle „Mitwirkender“ zugewiesen werden.

    ```azurecli-interactive
        az ad sp create-for-rbac --name "myApp" --role contributor \
                                    --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                    --sdk-auth
    ```
    
1. Kopieren Sie das JSON-Objekt für Ihren Dienstprinzipal.

    ```json
    {
        "clientId": "<GUID>",
        "clientSecret": "<GUID>",
        "subscriptionId": "<GUID>",
        "tenantId": "<GUID>",
        (...)
    }
    ```

1. Öffnen Sie Ihr GitHub-Repository, und navigieren Sie zu **Einstellungen**.

    :::image type="content" source="media/github-repo-settings.png" alt-text="Auswählen von „Einstellungen“ im Navigationsbereich":::

1. Wählen Sie **Geheimnisse** und dann **Neues Geheimnis** aus.

    :::image type="content" source="media/select-secrets.png" alt-text="Auswählen eines Geheimnisses zum Hinzufügen":::

1. Fügen Sie Ihr JSON-Objekt für Ihren Dienstprinzipal mit dem Namen `AZURE_CREDENTIALS` ein. 

    :::image type="content" source="media/azure-secret-add.png" alt-text="Hinzufügen eines Geheimnisses in GitHub":::

1. Speichern Sie die Änderungen, indem Sie die Option **Add secret** (Geheimnis hinzufügen) auswählen.

## <a name="use-the-azure-login-action"></a>Verwenden der Aktion „Azure-Anmeldung“

Verwenden Sie das Dienstprinzipalgeheimnis mit der [Aktion „Azure-Anmeldung“](https://github.com/Azure/login), um die Authentifizierung bei Azure durchzuführen.

In diesem Workflow authentifizieren Sie sich mit der Aktion „Azure-Anmeldung“ und den Dienstprinzipaldetails, die in `secrets.AZURE_CREDENTIALS` gespeichert sind. Anschließend führen Sie eine Azure CLI-Aktion aus. Weitere Informationen zum Verweisen auf GitHub-Geheimnisse in einer Workflowdatei finden Sie in den GitHub-Dokumentationen unter [Verwenden verschlüsselter Geheimnisse in einem Workflow](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow).

Wenn Sie über einen funktionierenden Schritt zur Azure-Anmeldung verfügen, können Sie die [Azure PowerShell](https://github.com/Azure/PowerShell)- oder [Azure CLI](https://github.com/Azure/CLI)-Aktionen verwenden. Sie können auch andere Azure-Aktionen wie die [Azure-Web-App-Bereitstellung](https://github.com/Azure/webapps-deploy) und [Azure-Funktionen](https://github.com/Azure/functions-action) verwenden.

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
```

## <a name="use-the-azure-powershell-action"></a>Verwenden der Azure PowerShell-Aktion

In diesem Beispiel melden Sie sich mit der [Aktion „Azure-Anmeldung“](https://github.com/Azure/login) an und rufen dann mit der [Azure PowerShell-Aktion](https://github.com/azure/powershell) eine Ressourcengruppe ab.

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
          enable-AzPSSession: true
      - name: Azure PowerShell Action
        uses: Azure/powershell@v1
        with:
          inlineScript: Get-AzVM -ResourceGroupName "< YOUR RESOURCE GROUP >"
          azPSVersion: 3.1.0
```

## <a name="use-the-azure-cli-action"></a>Verwenden der Azure CLI-Aktion

In diesem Beispiel melden Sie sich mit der [Aktion „Azure-Anmeldung“](https://github.com/Azure/login) an und rufen dann mit der [Azure CLI-Aktion](https://github.com/Azure/CLI) eine Ressourcengruppe ab.


```yaml
on: [push]

name: AzureLoginSample

jobs:
build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - name: Log in with Azure
        uses: azure/login@v1
        with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
        uses: azure/CLI@v1
        with:
        azcliversion: 2.0.72
        inlineScript: |
            az account show
            az storage -h
```

## <a name="connect-to-azure-government-and-azure-stack-hub-clouds"></a>Herstellen einer Verbindung mit Azure Government- und Azure Stack Hub-Clouds

Legen Sie für die Anmeldung bei einer Azure Government-Cloud die optionale Parameterumgebung mit den unterstützten Cloudnamen `AzureUSGovernment` oder `AzureChinaCloud` fest. Wenn dieser Parameter nicht angegeben ist, wird der Standardwert `AzureCloud` verwendet und eine Verbindung mit der öffentlichen Azure-Cloud hergestellt.

```yaml
   - name: Login to Azure US Gov Cloud with CLI
     uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_US_GOV_CREDENTIALS }}
          environment: 'AzureUSGovernment'
          enable-AzPSSession: false
   - name: Login to Azure US Gov Cloud with Az Powershell
      uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_US_GOV_CREDENTIALS }}
          environment: 'AzureUSGovernment'
          enable-AzPSSession: true
```

## <a name="connect-with-other-azure-services"></a>Herstellen einer Verbindung mit anderen Azure-Diensten

In den folgenden Artikeln finden Sie ausführliche Informationen zum Herstellen einer Verbindung mit GitHub aus Azure und anderen Diensten.  

### <a name="azure-active-directory"></a>Azure Active Directory 

- [Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit GitHub](/azure/active-directory/saas-apps/github-tutorial)   

### <a name="power-bi"></a>Power BI

- [Herstellen einer Verbindung mit GitHub mithilfe von Power BI](/power-bi/service-connect-to-github)   

### <a name="connectors"></a>Connectors

- [GitHub-Connector für Azure Logic Apps, Power Automate und Power Apps](/connectors/github/)   

### <a name="azure-databricks"></a>Azure Databricks

- [GitHub-Versionskontrolle](/azure/databricks/notebooks/github-version-control) 

> [!div class="nextstepaction"]
> [Bereitstellen von Apps aus GitHub in Azure](deploy-to-azure.md)
