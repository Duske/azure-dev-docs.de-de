---
title: Bereitstellen einer Web-App mithilfe der Azure SDK-Bibliotheken
description: Verwenden Sie die Verwaltungsbibliotheken in den Azure SDK-Bibliotheken für Python, um eine Web-App bereitzustellen und dann App-Code aus einem GitHub-Repository bereitzustellen.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 9db37e1cc496333c825789cc850ed6750907fa88
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275174"
---
# <a name="example-use-the-azure-libraries-to-provision-and-deploy-a-web-app"></a>Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App

In diesem Beispiel wird gezeigt, wie Sie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwenden, um eine Web-App in Azure App Service bereitzustellen und App-Code aus einem GitHub-Repository bereitzustellen. ([Äquivalente Azure CLI-Befehle](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel.)

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Installieren der erforderlichen Azure-Bibliothekspakete

Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

```text
azure-mgmt-resource
azure-mgmt-web
azure-cli-core
```

Installieren Sie in einem Terminal oder einer Eingabeaufforderung bei aktivierter virtueller Umgebung die Voraussetzungen:

```cmd
pip install -r requirements.txt
```

## <a name="3-fork-the-sample-repository"></a>3: Forken des Beispielrepositorys

Besuchen Sie [https://github.com/Azure-Samples/python-docs-hello-world](https://github.com/Azure-Samples/python-docs-hello-world), und forken Sie das Repository in Ihr eigenes GitHub-Konto. Sie verwenden eine Fork (Verzweigung), um sicherzustellen, dass Sie über die Berechtigungen zum Bereitstellen des Repository in Azure verfügen.

![Forken des Beispielrepositorys auf GitHub](media/azure-sdk-example-web-app/fork-github-repository.png)

Erstellen Sie dann eine Umgebungsvariable namens `REPO_URL` mit der URL Ihrer Fork. Der Beispielcode im nächsten Abschnitt hängt von dieser Umgebungsvariablen ab:

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set REPO_URL=<url_of_your_fork>
```

# <a name="bash"></a>[Bash](#tab/bash)

```bash
REPO_URL=<url_of_your_fork>
```

---

## <a name="4-write-code-to-provision-and-deploy-a-web-app"></a>4: Schreiben von Code zum Bereitstellen einer Web-App

Erstellen Sie eine Python-Datei mit dem Namen *provision_deploy_webapp.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.web import WebSiteManagementClient

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-WebApp-rg'
LOCATION = "centralus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


#Step 2: Provision the App Service plan, which defines the underlying VM for the web app.

# Names for the App Service plan and App Service. We use a random number with the
# latter to create a reasonably unique name. If you've already provisioned a
# web app and need to re-run the script, set the WEB_APP_NAME environment 
# variable to that name instead.
SERVICE_PLAN_NAME = 'PythonAzureExample-WebApp-plan'
WEB_APP_NAME = os.environ.get("WEB_APP_NAME", f"PythonAzureExample-WebApp-{random.randint(1,100000):05}")

# Obtain the client object
app_service_client = get_client_from_cli_profile(WebSiteManagementClient)

# Provision the plan; Linux is the default
poller = app_service_client.app_service_plans.create_or_update(RESOURCE_GROUP_NAME,
    SERVICE_PLAN_NAME,
    {
        "location": LOCATION,
        "reserved": True,
        "sku" : {"name" : "B1"}
    }
)

plan_result = poller.result()

print(f"Provisioned App Service plan {plan_result.name}")


# Step 3: With the plan in place, provision the web app itself, which is the process that can host
# whatever code we want to deploy to it.

poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()

print(f"Provisioned web app {web_app_result.name} at {web_app_result.default_host_name}")

# Step 4: deploy code from a GitHub repository. For Python code, App Service on Linux runs
# the code inside a container that makes certain assumptions about the structure of the code.
# For more information, see How to configure Python apps,
# https://docs.microsoft.com/azure/app-service/configure-language-python.
#
# The create_or_update_source_control method doesn't provision a web app. It only sets the
# source control configuration for the app. In this case we're simply pointing to
# a GitHub repository.
#
# You can call this method again to change the repo.

REPO_URL = 'https://github.com/kraigb/python-docs-hello-world'

poller = app_service_client.web_apps.create_or_update_source_control(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": "GitHub",
        "repo_url": REPO_URL,
        "branch": "master"
    }
)

sc_result = poller.result()

print(f"Set source control on web app to {sc_result.branch} branch of {sc_result.repo_url}")
```

Dieser Code verwendet die CLI-basierten Authentifizierungsmethoden (`get_client_from_cli_profile`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden, sollten Sie stattdessen `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode verwenden, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md) beschrieben.

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [WebSiteManagementClient (azure.mgmt.web import)](/python/api/azure-mgmt-web/azure.mgmt.web.websitemanagementclient?view=azure-python)

## <a name="5-run-the-script"></a>5: Führen Sie das Skript aus.

```cmd
python provision_deploy_web_app.py
```

## <a name="6-verify-the-web-app-deployment"></a>6: Überprüfen der Web-App-Bereitstellung

1. Besuchen Sie die bereitgestellte Website, indem Sie den folgenden Befehl ausführen:

    ```azurecli
    az webapp browse -n PythonAzureExample-WebApp-12345
    ```

    Ersetzen Sie „PythonAzureExample-WebApp-12345“ durch den spezifischen Namen Ihrer Web-App.

    „Hello World!“ muss auf dem Browser angezeigt werden.

1. Besuchen Sie das [Azure-Portal](https://portal.azure.com), wählen Sie **Ressourcengruppen** aus, und vergewissern Sie sich, dass „PythonAzureExample-WebApp-rg“ aufgeführt ist. Navigieren Sie dann in diese Liste, um zu überprüfen, ob die erwarteten Ressourcen vorhanden sind, nämlich der App Service-Plan und der App Service.

## <a name="7-clean-up-resources"></a>7: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonAzureExample-WebApp-rg
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellten Ressourcen nicht behalten müssen, und Sie in Ihrem Abonnement laufende Gebühren vermeiden möchten.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Die folgenden Azure CLI-Befehle führen dieselben Bereitstellungsschritte wie das Python-Skript aus:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 ^
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

rem You can use --deployment-source-url with the first create command. It's shown here
rem to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan ^
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

rem Replace <your_fork> with the specific URL of your forked repository.
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 \
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

# You can use --deployment-source-url with the first create command. It's shown here
# to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan \
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

# Replace <your_fork> with the specific URL of your forked repository.
```

---

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
