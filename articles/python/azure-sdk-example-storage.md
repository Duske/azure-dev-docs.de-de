---
title: Bereitstellen von Azure Storage mit den Azure-Bibliotheken für Python
description: Verwenden Sie die Azure SDK für Python-Bibliotheken, um einen Blobcontainer in einem Azure Storage-Konto bereitzustellen und dann eine Datei in diesen Container hochzuladen.
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f3b27dd0a09bbf17c5797e3a5c0be9bc7d48b416
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92689028"
---
# <a name="example-provision-azure-storage-using-the-azure-libraries-for-python"></a>Beispiel: Bereitstellen von Azure Storage mit den Azure-Bibliotheken für Python

In diesem Artikel erfahren Sie, wie Sie die Azure-Verwaltungsbibliotheken in einem Python-Skript verwenden, um eine Ressourcengruppe bereitzustellen, die ein Azure Storage-Konto und einen Blobspeichercontainer enthält. ([Äquivalente Azure CLI-Befehle](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel. Wenn Sie die Azure-Portal bevorzugen, finden Sie weitere Informationen unter [Erstellen eines Azure Storage-Kontos](/azure/storage/common/storage-account-create?tabs=azure-portal) und [Erstellen eines Blobcontainers](/azure/storage/blobs/storage-quickstart-blobs-portal).)

Sehen Sie sich nach der Bereitstellung der Ressourcen das [Beispiel zur Verwendung von Azure Storage](azure-sdk-example-storage-use.md) an, um zu erfahren, wie Sie die Azure-Clientbibliotheken in Python-Anwendungscode verwenden, um eine Datei in den Blobspeichercontainer hochzuladen.

Alle Befehle in diesem Artikel funktionieren in Linux-/macOS-Bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Installieren der erforderlichen Azure-Bibliothekspakete

1. Erstellen Sie eine Datei *requirements.txt* , in der die in diesem Beispiel verwendeten Verwaltungsbibliotheken aufgeführt sind:

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-identity
    ```

1. Installieren Sie in Ihrem Terminal bei aktivierter virtueller Umgebung die Voraussetzungen:

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Schreiben von Code zum Bereitstellen von Speicherressourcen

In diesem Abschnitt erfahren Sie, wie Sie Speicherressourcen über Python-Code bereitstellen. Ressourcen können auf Wunsch aber auch über das Azure-Portal oder über die [äquivalenten Azure CLI-Befehle](#for-reference-equivalent-azure-cli-commands) bereitgestellt werden.

Erstellen Sie eine Python-Datei mit dem Namen *provision_blob.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
import os, random

# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonAzureExample-Storage-rg"
LOCATION = "centralus"

# Step 1: Provision the resource group.

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


# Step 2: Provision the storage account, starting with a management object.

storage_client = StorageManagementClient(credential, subscription_id)

# This example uses the CLI profile credentials because we assume the script
# is being used to provision the resource in the same way the Azure CLI would be used.

STORAGE_ACCOUNT_NAME = f"pythonazurestorage{random.randint(1,100000):05}"

# You can replace the storage account here with any unique name. A random number is used
# by default, but note that the name changes every time you run this script.
# The name must be 3-24 lower case letters and numbers only.


# Check if the account name is available. Storage account names must be unique across
# Azure because they're used in URLs.
availability_result = storage_client.storage_accounts.check_name_availability(
    { "name": STORAGE_ACCOUNT_NAME }
)

if not availability_result.name_available:
    print(f"Storage name {STORAGE_ACCOUNT_NAME} is already in use. Try another name.")
    exit()

# The name is available, so provision the account
poller = storage_client.storage_accounts.begin_create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME,
    {
        "location" : LOCATION,
        "kind": "StorageV2",
        "sku": {"name": "Standard_LRS"}
    }
)

# Long-running operations return a poller object; calling poller.result()
# waits for completion.
account_result = poller.result()
print(f"Provisioned storage account {account_result.name}")


# Step 3: Retrieve the account's primary access key and generate a connection string.
keys = storage_client.storage_accounts.list_keys(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME)

print(f"Primary key for storage account: {keys.keys[0].value}")

conn_string = f"DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={STORAGE_ACCOUNT_NAME};AccountKey={keys.keys[0].value}"

print(f"Connection string: {conn_string}")

# Step 4: Provision the blob container in the account (this call is synchronous)
CONTAINER_NAME = "blob-container-01"
container = storage_client.blob_containers.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME, CONTAINER_NAME, {})

# The fourth argument is a required BlobContainer object, but because we don't need any
# special values there, so we just pass empty JSON.

print(f"Provisioned blob container {container.name}")
```

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [AzureCliCredential (azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)
- [StorageManagementClient (azure.mgmt.storage)](/python/api/azure-mgmt-storage/azure.mgmt.storage.storagemanagementclient)

## <a name="4-run-the-script"></a>4. Führen Sie das Skript aus.

```cmd
python provision_blob.py
```

Es kann ein oder zwei Minuten dauern, bis das Skript abgeschlossen ist.

## <a name="5-verify-the-resources"></a>5: Überprüfen der Ressourcen

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), um zu überprüfen, ob die Ressourcengruppe und das Speicherkonto wie erwartet bereitgestellt wurden. Möglicherweise müssen Sie **Ausgeblendete Typen anzeigen** in der Ressourcengruppe auswählen, um ein Speicherkonto anzuzeigen, das über ein Python-Skript bereitgestellt wurde:

    ![Die Azure-Portal Seite für die neue Ressourcengruppe mit dem Speicherkonto](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Wählen Sie das Speicherkonto aus, und wählen Sie dann im Menü auf der linken Seite **Blobdienst** > **Container** aus, um zu überprüfen, ob „bloc-container-01“ angezeigt wird:

    ![Die Azure-Portal Seite für das Speicherkonto mit dem Blobcontainer](media/azure-sdk-example-storage/portal-show-blob-containers.png)

1. Wenn Sie diese bereitgestellten Ressourcen über Anwendungscode verwenden möchten, fahren Sie mit [Beispiel: Verwenden von Azure Storage](azure-sdk-example-storage-use.md).

Ein weiteres Beispiel mit der Azure Storage-Verwaltungsbibliothek finden Sie unter [Erste Schritte mit der Azure Storage-Verwaltung in Python](/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Die folgenden Azure CLI-Befehle führen dieselben Bereitstellungsschritte wie das Python-Skript aus:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
rem Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

rem Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus ^
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

rem Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg ^
    -n pythonazurestorage12345

rem Provision the blob container; NOTE: this command assumes you have an environment variable
rem named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus \
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg \
    -n pythonazurestorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

---

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

Falls Sie die Ressourcen in App-Code verwenden möchten, wie im Artikel [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md) beschrieben, überspringen Sie die Bereinigung der Ressourcen.

Führen Sie andernfalls den folgenden Befehl aus, um laufende Gebühren in Ihrem Abonnement zu vermeiden:

```azurecli
az group delete -n PythonAzureExample-Storage-rg  --no-wait
```

[!INCLUDE [resource_group_begin_delete](includes/resource-group-begin-delete.md)]

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Auflisten von Ressourcengruppen in einem Abonnement](azure-sdk-example-list-resource-groups.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
