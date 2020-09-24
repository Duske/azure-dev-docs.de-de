---
title: Bereitstellen einer Ressourcengruppe unter Verwendung der Azure-Bibliotheken für Python
description: Verwenden Sie die Ressourcenverwaltungsbibliothek im Azure SDK für Python, um eine Ressourcengruppe aus Python-Code zu erstellen.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 992232c23368f3f7dcd173f1f711e6c69650bdb6
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110522"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe

In diesem Beispiel wird veranschaulicht, wie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwendet werden, um eine Ressourcengruppe bereitzustellen. (Den [äquivalenten Azure CLI-Befehl](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel. Wenn Sie die Azure-Portal bevorzugen, finden Sie weitere Informationen unter [Erstellen von Ressourcengruppen](/azure/azure-resource-manager/management/manage-resource-groups-portal).)

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-azure-library-packages"></a>2: Installieren der Azure-Bibliothekspakete

Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

```text
azure-mgmt-resource
azure-cli-core
```

Installieren Sie in einem Terminal oder einer Eingabeaufforderung bei aktivierter virtueller Umgebung die Voraussetzungen:

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: Schreiben von Code zum Bereitstellen einer Ressourcengruppe

Erstellen Sie eine Python-Datei mit dem Namen *provision_rg.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-ResourceGroup-rg",
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

# Optional line to delete the resource group
#resource_client.resource_groups.delete("PythonAzureExample-ResourceGroup-rg")
```

Dieser Code verwendet die CLI-basierten Authentifizierungsmethoden (`get_client_from_cli_profile`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden, sollten Sie stattdessen `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode verwenden, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md) beschrieben.

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4: Führen Sie das Skript aus.

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: Überprüfen der Ressourcengruppe

Sie können mithilfe des Azure-Portals oder der Azure CLI überprüfen, ob die Gruppe vorhanden ist.

- Azure-Portal: Öffnen Sie das [Azure-Portal](https://portal.azure.com), wählen Sie **Ressourcengruppen** aus, und überprüfen Sie, ob die Gruppe aufgeführt ist. Wenn das Portal bereits geöffnet war, verwenden Sie den Befehl **Aktualisieren**, um die Liste zu aktualisieren.

- Azure CLI: Führen Sie den folgenden Befehl aus:

    ```azurecli
    az group show -n PythonAzureExample-ResourceGroup-rg
    ```

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonAzureExample-ResourceGroup-rg  --no-wait
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellte Ressourcengruppe nicht behalten müssen. Ressourcengruppen verursachen keine laufenden Gebühren in Ihrem Abonnement, aber es ist eine bewährte Methode, alle Gruppen zu bereinigen, die Sie nicht aktiv verwenden.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Die folgenden Azure CLI-Befehle führen dieselben Bereitstellungsschritte wie das Python-Skript aus:

```azurecli
az group create -n PythonAzureExample-ResourceGroup-rg -l centralus
```

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
