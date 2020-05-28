---
title: Bereitstellen und Verwenden von Speicherblobs Azure Storage mit dem Azure SDK für Python
description: Verwenden Sie die Azure SDK für Python-Bibliotheken, um einen Blobcontainer in einem Azure Storage-Konto bereitzustellen und dann eine Datei in diesen Container hochzuladen.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 904ca66f6e4c065fa0705d2e35b8a7bb46396a0d
ms.sourcegitcommit: b69db02c3358ce7899cef105508e3d17fafc84b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759943"
---
# <a name="example-use-the-azure-sdk-with-azure-storage"></a>Beispiel: Verwenden des Azure SDK mit Azure Storage

In diesem Artikel erfahren Sie, wie Sie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwenden, um eine Ressourcengruppe bereitzustellen, die ein Azure Storage-Konto und einen Blob Storage-Container enthält. Anschließend erfahren Sie, wie Sie die Azure SDK-Clientbibliotheken in Python-Anwendungscode verwenden, um eine Datei in diesen Blob Storage-Container hochzuladen.

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-needed-management-libraries"></a>2: Installieren der benötigten Verwaltungsbibliotheken

1. Erstellen Sie eine Datei *requirements.txt*, in der die in diesem Beispiel verwendeten Verwaltungsbibliotheken aufgeführt sind:

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. Installieren Sie in Ihrem Terminal bei aktivierter virtueller Umgebung die Voraussetzungen:

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Schreiben von Code zum Bereitstellen von Speicherressourcen

Erstellen Sie eine Python-Datei mit dem Namen *provision_blob.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
import os, random

# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonSDKExample-Storage-rg"
LOCATION = "centralus"

# Step 1: Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: Provision the storage account, starting with a management object.

storage_client = get_client_from_cli_profile(StorageManagementClient)

# This example uses the CLI profile credentials because we assume the script
# is being used to provision the resource in the same way the Azure CLI would be used.

STORAGE_ACCOUNT_NAME = f"pythonsdkstorage{random.randint(1,100000):05}"

# You can replace the storage account here with any unique name. A random number is used
# by default, but note that the name changes every time you run this script.
# The name must be 3-24 lower case letters and numbers only.


# Check if the account name is available. Storage account names must be unique across
# Azure because they're used in URLs.
availability_result = storage_client.storage_accounts.check_name_availability(STORAGE_ACCOUNT_NAME)

if not availability_result.name_available:
    print(f"Storage name {STORAGE_ACCOUNT_NAME} is already in use. Try another name.")
    exit()

# The name is available, so provision the account
poller = storage_client.storage_accounts.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME,
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

Dieser Code verwendet die CLI-basierten Authentifizierungsmethoden (`get_client_from_cli_profile`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden, sollten Sie stattdessen `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode verwenden, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md) beschrieben.

## <a name="4-run-the-script"></a>4. Ausführen des Skripts:

```bash
python provision_blob.py
```

Es kann ein oder zwei Minuten dauern, bis das Skript abgeschlossen ist.

## <a name="5-verify-the-resources"></a>5: Überprüfen der Ressourcen

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), um zu überprüfen, ob die Ressourcengruppe und das Speicherkonto wie erwartet bereitgestellt wurden. Möglicherweise müssen Sie **Ausgeblendete Typen anzeigen** in der Ressourcengruppe auswählen, um ein Speicherkonto anzuzeigen, das über ein Python-Skript bereitgestellt wurde:

    ![Die Azure-Portal Seite für die neue Ressourcengruppe mit dem Speicherkonto](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Wählen Sie das Speicherkonto aus, und wählen Sie dann im Menü auf der linken Seite **Blobdienst** > **Container** aus, um zu überprüfen, ob „bloc-container-01“ angezeigt wird:

    ![Die Azure-Portal Seite für das Speicherkonto mit dem Blobcontainer](media/azure-sdk-example-storage/portal-show-blob-containers.png)

Ein weiteres Beispiel das die Azure Storage-Verwaltungsbibliothek verwendet, finden Sie im [Verwalten von Python-Speicher – Beispiel](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Die folgenden Azure CLI-Befehle führen dieselben Bereitstellungsschritte wie das Python-Skript aus:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus \
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg \
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus ^
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg ^
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

---

## <a name="6-use-resources-through-the-sdk-client-libraries"></a>6: Verwenden von Ressourcen durch die SDK-Clientbibliotheken

In den folgenden Abschnitten werden zwei Möglichkeiten für den Zugriff auf den Blobcontainer erläutert, der im vorherigen Abschnitt bereitgestellt wurde. In diesen Beispielen wird mithilfe der entsprechenden SDK-Clientbibliotheken explizit ein Dateiblob in diesen Container hochgeladen.

Führen Sie die [allgemeinen Schritte](#common-steps-for-both-methods) aus, um den Code selbst auszuprobieren.

Die erste Methode authentifiziert die App mit `DefaultAzureCredential`, wie unter [Authentifizieren von Python-Apps](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential) beschrieben. Mit dieser Methode müssen Sie zunächst der App-Identität die geeigneten Berechtigungen zuweisen, wobei es sich um die empfohlene Vorgehensweise handelt.

Die zweite Methode verwendet eine Verbindungszeichenfolge, um direkt auf das Speicherkonto zuzugreifen. Obwohl diese Methode einfacher erscheint, hat Sie zwei bedeutende Nachteile:

- Eine Verbindungszeichenfolge authentifiziert den Verbindungs-Agent von Natur aus mit dem Speicher*konto* anstatt mit einzelnen Ressourcen innerhalb dieses Kontos. Folglich stellt eine Verbindungszeichenfolge eine umfassendere Autorisierung bereit, als sie möglicherweise erforderlich ist.

- Eine Verbindungszeichenfolge enthält einen Zugriffsschlüssel als Klartext und stellt daher potenzielle Sicherheitsrisiken dar, wenn sie mangelhaft aufgebaut oder ungenügend geschützt ist. Wenn eine solche Verbindungszeichenfolge verfügbar gemacht wird, kann sie für den Zugriff auf eine Vielzahl von Ressourcen im Speicherkonto verwendet werden.

Aus diesen Gründen sollte der Produktionscode die Authentifizierungsmethode verwenden. Für Experimente kann jedoch auch die Verbindungszeichenfolge verwendet werden.

### <a name="common-steps-for-both-methods"></a>Allgemeine Schritte für beide Methoden

1. Fügen Sie in Ihrer Datei *requirements.txt* eine Zeile für die erforderlichen Clientbibliotheken hinzu, und speichern Sie die Datei:

    ```text
    azure-storage-blob
    azure-identity
    ```

1. Installieren Sie in Ihrem Terminal oder in der Eingabeaufforderung die Anforderungen neu:

    ```bash
    pip install -r requirements.txt
    ```

1. Erstellen Sie eine Quelldatei mit dem Namen *sample-source.txt* (wie der Code dies erwartet) sowie mit Inhalten wie den folgenden:

    ```text
    Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
    ```

### <a name="use-blob-storage-with-authentication"></a>Verwenden von Blob Storage mit Authentifizierung

1. Erstellen Sie eine Umgebungsvariable namens `AZURE_STORAGE_BLOB_URL`:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    ---

    Ersetzen Sie „pythonsdkstorage12345“ durch den Namen Ihres spezifischen Speicherkontos.

1. Erstellen Sie eine Datei namens *use_blob_auth.py* mit folgendem Code. Die Schritte werden in den Kommentaren erläutert.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonsdkstorage12345.blob.core.windows.net/
    storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Versuchen Sie, den Code auszuführen (bei dem absichtlich ein Fehler auftritt):

    ```bash
    python use_blob_auth.py
    ```

    Da der lokale Dienstprinzipal, den Sie verwenden, nicht über die Berechtigung für den Zugriff auf den Blobcontainer verfügt, wird der folgende Fehler angezeigt: „Diese Anforderung ist nicht berechtigt, diesen Vorgang mit dieser Berechtigung auszuführen.“

1. Um dem Dienstprinzipal Berechtigungen für den Container zu gewähren, verwenden Sie den Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (ein langer Befehl!):

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    Das Argument `--scope` gibt den Bereich an, in dem diese Rollenzuweisung gilt. In diesem Beispiel gewähren Sie dem *spezifischen* Container namens „blob-containter-01“ die Rolle „Mitwirkender an Storage-Blobdaten“.

    Ersetzen Sie `pythonsdkstorage12345` durch den exakten Namen Ihres Speicherkontos. Sie können auch den Namen der Ressourcengruppe und des Blobcontainers anpassen, falls erforderlich. Wenn Sie den falschen Namen verwenden, erhalten Sie die Fehlermeldung „Angeforderter Vorgang kann nicht auf die geschachtelte Ressource angewendet werden. Die übergeordnete Ressource ‚pythonsdkstorage12345‘ wurde nicht gefunden.“

    Das Argument `--scope` in diesem Befehl verwendet auch die Umgebungsvariablen AZURE_CLIENT_ID und AZURE_SUBSCRIPTION_ID, die Sie bereits in Ihrer lokalen Umgebung für Ihren Dienstprinzipal festgelegt haben sollten, indem Sie [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md) befolgt haben.

1. Wenn Sie ein bis zwei Minuten gewartet haben, dass die Berechtigungen weitergegeben wurden, führen Sie den Code erneut aus, um zu überprüfen, ob er nun funktioniert. Wird der Berechtigungsfehler erneut angezeigt wird, warten Sie etwas länger, und führen Sie den Code dann erneut aus.

Weitere Informationen zu Umfängen und Rollenzuweisungen finden Sie unter [Zuweisen von Rollenberechtigungen](how-to-assign-role-permissions.md).

### <a name="use-blob-storage-with-a-connection-string"></a>Verwenden von Blob Storage mit einer Verbindungszeichenfolge

1. Erstellen Sie eine Python-Datei namens *use_blob_conn_string.py* und dem folgenden Code. Die Schritte werden in den Kommentaren erläutert.

    ```python
    import os

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    # Retrieve the connection string from an environment variable.
    conn_string = os.environ["AZURE_STORAGE_CONNECTION_STRING"]

    # Create the client object for the resource identified by the connection string,
    # indicating also the blob container and the name of the specific blob we want.
    blob_client = BlobClient.from_connection_string(conn_string, container_name="blob-container-01", blob_name="sample-blob.txt")

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Führen Sie den Code aus:

    ```bash
    python use_blob_conn_string.py
    ```

Erneut, auch wenn diese Methode einfach ist, autorisiert eine Verbindungszeichenfolge alle Vorgänge in einem Speicherkonto. Bei Produktionscode ist es besser, spezifische Berechtigungen zu verwenden, wie im vorherigen Abschnitt beschrieben.

### <a name="verify-blob-creation"></a>Überprüfen der Blob-Erstellung

Wechseln Sie nach dem Ausführen des Codes einer der beiden Methoden zum [Azure-Portal](https://portal.azure.com), navigieren Sie in den Blobcontainer, um zu überprüfen, ob ein neues Blob mit dem Namen *sample-blob.txt* mit demselben Inhalt wie die Datei *sample-source.txt* vorhanden ist:

![Die Azure-Portal Seite für den Blobcontainer mit der hochgeladenen Datei](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="7-clean-up-resources"></a>7: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonSDKExample-Storage-rg
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellten Ressourcen nicht behalten müssen, und Sie in Ihrem Abonnement laufende Gebühren vermeiden möchten.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Beispiel: Bereitstellen einer Web-App und Bereitstellen von Code >>>](azure-sdk-example-web-app.md)
