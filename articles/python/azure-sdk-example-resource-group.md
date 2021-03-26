---
title: Bereitstellen einer Ressourcengruppe unter Verwendung der Azure-Bibliotheken für Python
description: Verwenden Sie die Ressourcenverwaltungsbibliothek im Azure SDK für Python, um eine Ressourcengruppe aus Python-Code zu erstellen.
ms.date: 01/28/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 503ed658fd216edb6308f39b7a721398d6d1fc3a
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117804"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe

In diesem Beispiel wird veranschaulicht, wie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwendet werden, um eine Ressourcengruppe bereitzustellen. (Den [äquivalenten Azure CLI-Befehl](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel. Wenn Sie die Azure-Portal bevorzugen, finden Sie weitere Informationen unter [Erstellen von Ressourcengruppen](/azure/azure-resource-manager/management/manage-resource-groups-portal).)

Alle Befehle in diesem Artikel funktionieren in Linux-/macOS-Bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Achten Sie darauf, eine virtuelle Umgebung für dieses Projekt zu erstellen und zu aktivieren.

## <a name="2-install-the-azure-library-packages"></a>2: Installieren der Azure-Bibliothekspakete

Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

```text
azure-mgmt-resource>=1.15.0
azure-identity>=1.5.0
```

Achten Sie darauf, dass Sie diese Versionen der Bibliotheken verwenden. Die Verwendung von älteren Versionen führt zu Fehlern, z. B. vom Typ „'AzureCliCredential'-Objekt hat kein Attribut 'signed_session'“.

Installieren Sie in einem Terminal oder einer Eingabeaufforderung bei aktivierter virtueller Umgebung die Voraussetzungen:

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: Schreiben von Code zum Bereitstellen einer Ressourcengruppe

Erstellen Sie eine Python-Datei mit dem Namen *provision_rg.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
# Import the needed credential and management objects from the libraries.
from azure.mgmt.resource import ResourceManagementClient
from azure.identity import AzureCliCredential
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-rg",
    {
        "location": "centralus"
    }
)

# Within the ResourceManagementClient is an object named resource_groups,
# which is of class ResourceGroupsOperations, which contains methods like
# create_or_update.
#
# The second parameter to create_or_update here is technically a ResourceGroup
# object. You can create the object directly using ResourceGroup(location=LOCATION)
# or you can express the object as inline JSON as shown here. For details,
# see Inline JSON pattern for object arguments at
# https://docs.microsoft.com/azure/developer/python/azure-sdk-overview#inline-json-pattern-for-object-arguments.

print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# The return value is another ResourceGroup object with all the details of the
# new group. In this case the call is synchronous: the resource group has been
# provisioned by the time the call returns.

# Update the resource group with tags
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-rg",
    {
        "location": "centralus",
        "tags": { "environment":"test", "department":"tech" }
    }
)

print(f"Updated resource group {rg_result.name} with tags")

# Optional lines to delete the resource group. begin_delete is asynchronous.
# poller = resource_client.resource_groups.begin_delete(rg_result.name)
# result = poller.result()
```

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [AzureCliCredential (azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)

## <a name="4-run-the-script"></a>4: Führen Sie das Skript aus.

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: Überprüfen der Ressourcengruppe

Sie können mithilfe des Azure-Portals oder der Azure CLI überprüfen, ob die Gruppe vorhanden ist.

- Azure-Portal: Öffnen Sie das [Azure-Portal](https://portal.azure.com), wählen Sie **Ressourcengruppen** aus, und überprüfen Sie, ob die Gruppe aufgeführt ist. Wenn das Portal bereits geöffnet war, verwenden Sie den Befehl **Aktualisieren**, um die Liste zu aktualisieren.

- Azure CLI: Führen Sie den folgenden Befehl aus:

    ```azurecli
    az group show -n PythonAzureExample-rg
    ```

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonAzureExample-rg  --no-wait
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellte Ressourcengruppe nicht behalten müssen. Ressourcengruppen verursachen keine laufenden Gebühren in Ihrem Abonnement, aber es ist eine bewährte Methode, alle Gruppen zu bereinigen, die Sie nicht aktiv verwenden. Das Argument `--no-wait` ermöglicht die direkte Rückgabe des Befehls, und es muss nicht auf den Abschluss des Vorgangs gewartet werden.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Die folgenden Azure CLI-Befehle führen dieselben Bereitstellungsschritte wie das Python-Skript aus:

```azurecli
az group create -n PythonAzureExample-rg -l centralus
```

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Auflisten von Ressourcengruppen in einem Abonnement](azure-sdk-example-list-resource-groups.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
- [Verwenden verwalteter Azure-Datenträger mit den Azure-Bibliotheken (SDK) für Python](azure-sdk-samples-managed-disks.md)
- [Kurze Umfrage zum Azure SDK für Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)
