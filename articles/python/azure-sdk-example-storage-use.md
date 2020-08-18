---
title: Verwenden von Speicherblobs Azure Storage mit dem Azure SDK für Python
description: Hier erfahren Sie, wie Sie die Bibliotheken des Azure SDK für Python verwenden, um auf einen vorab bereitgestellten Blobcontainer in einem Azure Storage-Konto zuzugreifen und eine Datei in diesen Container hochzuladen.
ms.date: 08/05/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: bcb8d680950e2c42d3d7f092478c4d6096454b12
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982692"
---
# <a name="example-access-azure-storage-using-the-azure-libraries-for-python"></a>Beispiel: Zugriff auf Azure Storage mit den Azure-Bibliotheken für Python

In diesem Beispiel erfahren Sie, wie Sie die Azure-Clientbibliotheken in Python-Anwendungscode verwenden, um eine Datei in diesen Blobspeichercontainer hochzuladen. In dem Beispiel wird davon ausgegangen, dass Sie die Ressourcen aus [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md) bereitgestellt haben.

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-library-packages"></a>2: Installieren von Bibliothekspaketen

1. Fügen Sie in der Datei *requirements.txt* eine Zeile für das erforderliche Clientbibliothekspaket hinzu, und speichern Sie die Datei:

    ```text
    azure-storage-blob
    azure-identity
    ```

1. Installieren Sie in Ihrem Terminal oder in der Eingabeaufforderung die Anforderungen neu:

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-create-a-file-to-upload"></a>3: Erstellen einer Datei zum Hochladen

Erstellen Sie eine Quelldatei mit dem Namen *sample-source.txt* (wie der Code dies erwartet) sowie mit Inhalten wie den folgenden:

```text
Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
```

## <a name="4-use-blob-storage-from-app-code"></a>4: Verwenden von Blobspeicher über App-Code

In den folgenden Abschnitten (4a und 4b) werden zwei Möglichkeiten für den Zugriff auf den Blobcontainer gezeigt, den Sie über [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md) bereitgestellt haben.

Bei der [ersten Methode (Abschnitt 4a unten)](#4a-use-blob-storage-with-authentication) wird die App mit `DefaultAzureCredential` authentifiziert, wie im Artikel [Authentifizieren von Python-Apps](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential) beschrieben. Mit dieser Methode müssen Sie zunächst der App-Identität die geeigneten Berechtigungen zuweisen, wobei es sich um die empfohlene Vorgehensweise handelt.

Bei der [zweiten Methode (Abschnitt 4b unten)](#4b-use-blob-storage-with-a-connection-string) wird eine Verbindungszeichenfolge verwendet, um direkt auf das Speicherkonto zuzugreifen. Obwohl diese Methode einfacher erscheint, hat Sie zwei bedeutende Nachteile:

- Eine Verbindungszeichenfolge authentifiziert den Verbindungs-Agent von Natur aus mit dem Speicher*konto* anstatt mit einzelnen Ressourcen innerhalb dieses Kontos. Folglich stellt eine Verbindungszeichenfolge eine umfassendere Autorisierung bereit, als sie möglicherweise erforderlich ist.

- Eine Verbindungszeichenfolge enthält einen Zugriffsschlüssel als Klartext und stellt daher potenzielle Sicherheitsrisiken dar, wenn sie mangelhaft aufgebaut oder ungenügend geschützt ist. Wenn eine solche Verbindungszeichenfolge verfügbar gemacht wird, kann sie für den Zugriff auf eine Vielzahl von Ressourcen im Speicherkonto verwendet werden.

Aus diesen Gründen empfiehlt es sich, in Produktionscode die Authentifizierungsmethode zu verwenden.

### <a name="4a-use-blob-storage-with-authentication"></a>4a: Verwenden von Blob Storage mit Authentifizierung

1. Erstellen Sie eine Umgebungsvariable namens `STORAGE_BLOB_URL`:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    ---

    Ersetzen Sie „pythonazurestorage12345“ durch den Namen Ihres spezifischen Speicherkontos.

    Diese Umgebungsvariable wird nur in diesem Beispiel und nicht von den Azure-Bibliotheken verwendet.

1. Erstellen Sie eine Datei namens *use_blob_auth.py* mit folgendem Code. Die Schritte werden in den Kommentaren erläutert.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the Azure library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonazurestorage12345.blob.core.windows.net/
    storage_url = os.environ["STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

    Referenzlinks:
      - [DefaultAzureCredential (azure.identity)](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python)
      - [BlobClient (azure.storage.blob)](/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)

1. Versuchen Sie, den Code auszuführen (bei dem absichtlich ein Fehler auftritt):

    ```cmd
    python use_blob_auth.py
    ```

    Da der lokale Dienstprinzipal, den Sie verwenden, nicht über die Berechtigung für den Zugriff auf den Blobcontainer verfügt, wird der folgende Fehler angezeigt: „Diese Anforderung ist nicht berechtigt, diesen Vorgang mit dieser Berechtigung auszuführen.“

1. Um dem Dienstprinzipal Containerberechtigungen zu gewähren, verwenden Sie den Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (ein langer Befehl!):

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    Das Argument `--scope` gibt den Bereich an, in dem diese Rollenzuweisung gilt. In diesem Beispiel gewähren Sie dem *spezifischen* Container namens „blob-containter-01“ die Rolle „Mitwirkender an Storage-Blobdaten“.

    Ersetzen Sie `pythonazurestorage12345` durch den exakten Namen Ihres Speicherkontos. Sie können auch den Namen der Ressourcengruppe und des Blobcontainers anpassen, falls erforderlich. Wenn Sie den falschen Namen verwenden, erhalten Sie die Fehlermeldung „Angeforderter Vorgang kann nicht auf die geschachtelte Ressource angewendet werden. Die übergeordnete Ressource ‚pythonazurestorage12345‘ wurde nicht gefunden.“

    Das Argument `--scope` in diesem Befehl verwendet auch die Umgebungsvariablen AZURE_CLIENT_ID und AZURE_SUBSCRIPTION_ID, die Sie bereits in Ihrer lokalen Umgebung für Ihren Dienstprinzipal festgelegt haben sollten, indem Sie [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md) befolgt haben.

1. Wenn Sie ein bis zwei Minuten gewartet haben, dass die Berechtigungen weitergegeben wurden, führen Sie den Code erneut aus, um zu überprüfen, ob er nun funktioniert. Wird der Berechtigungsfehler erneut angezeigt wird, warten Sie etwas länger, und führen Sie den Code dann erneut aus.

Weitere Informationen zu Umfängen und Rollenzuweisungen finden Sie unter [Zuweisen von Rollenberechtigungen](how-to-assign-role-permissions.md).

### <a name="4b-use-blob-storage-with-a-connection-string"></a>4b: Verwenden von Blob Storage mit einer Verbindungszeichenfolge

1. Erstellen Sie eine Umgebungsvariable namens `AZURE_STORAGE_CONNECTION_STRING` mit dem Wert der vollständigen Verbindungszeichenfolge für das Speicherkonto. (Diese Umgebungsvariable wird auch von verschiedenen Azure CLI-Befehlen verwendet.)

1. Erstellen Sie eine Python-Datei namens *use_blob_conn_string.py* und dem folgenden Code. Die Schritte werden in den Kommentaren erläutert.

    ```python
    import os

    # Import the client object from the Azure library
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

## <a name="5-verify-blob-creation"></a>5. Überprüfen der Blob-Erstellung

Wechseln Sie nach dem Ausführen des Codes einer der beiden Methoden zum [Azure-Portal](https://portal.azure.com), navigieren Sie in den Blobcontainer, um zu überprüfen, ob ein neues Blob mit dem Namen *sample-blob.txt* mit demselben Inhalt wie die Datei *sample-source.txt* vorhanden ist:

![Die Azure-Portal Seite für den Blobcontainer mit der hochgeladenen Datei](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellten Ressourcen nicht behalten müssen, und Sie in Ihrem Abonnement laufende Gebühren vermeiden möchten.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
