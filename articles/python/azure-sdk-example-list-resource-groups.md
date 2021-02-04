---
title: Auflisten der Ressourcengruppe und Ressourcen unter Verwendung der Azure-Bibliotheken für Python
description: Verwenden Sie die Ressourcenverwaltungsbibliothek im Azure SDK für Python, um Ressourcengruppen und Ressourcen in einer Gruppe aufzulisten.
ms.date: 01/28/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c44698fb7fee0e7f736c51fbf11f664b8642e946
ms.sourcegitcommit: 3843092e47691fbd32452c93d51f894a0cab31db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99069097"
---
# <a name="example-use-the-azure-libraries-to-list-resource-groups-and-resources"></a>Beispiel: Verwenden der Azure-Bibliotheken zum Auflisten von Ressourcengruppen und Ressourcen

In diesem Beispiel wird veranschaulicht, wie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwendet werden, um zwei Aufgaben auszuführen:

- Auflisten aller Ressourcengruppen in einem Azure-Abonnement
- Auflisten von Ressourcen in einer bestimmten Ressourcengruppe

Alle Befehle in diesem Artikel funktionieren in Linux-/macOS-Bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

Den [äquivalenten Azure CLI-Befehl](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Achten Sie darauf, eine virtuelle Umgebung für dieses Projekt zu erstellen und zu aktivieren.

## <a name="2-install-the-azure-library-packages"></a>2: Installieren der Azure-Bibliothekspakete

Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

```text
azure-mgmt-resource>=1.15.0
azure-identity>=1.5.0
```

Installieren Sie in einem Terminal oder einer Eingabeaufforderung bei aktivierter virtueller Umgebung die Voraussetzungen:

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-work-with-resource-groups"></a>3: Schreiben von Code für die Verwendung von Ressourcengruppen

### <a name="3a-list-resource-groups-in-a-subscription"></a>3a. Auflisten von Ressourcengruppen in einem Abonnement

Erstellen Sie eine Python-Datei mit dem Namen *list_groups.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resource groups
group_list = resource_client.resource_groups.list()

# Show the groups in formatted output
column_width = 40

print("Resource Group".ljust(column_width) + "Location")
print("-" * (column_width * 2))

for group in list(group_list):
    print(f"{group.name:<{column_width}}{group.location}")
```

### <a name="3b-list-resources-within-a-specific-resource-group"></a>3b. Auflisten von Ressourcen in einer bestimmten Ressourcengruppe

Erstellen Sie eine Python-Datei mit dem Namen *list_resources.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
# Import the needed credential and management objects from the libraries.
from azure.identity import AzureCliCredential
from azure.mgmt.resource import ResourceManagementClient
import os

# Acquire a credential object using CLI-based authentication.
credential = AzureCliCredential()

# Retrieve subscription ID from environment variable.
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Obtain the management object for resources.
resource_client = ResourceManagementClient(credential, subscription_id)

# Retrieve the list of resources in "myResourceGroup" (change to any name desired).
# The expand argument includes additional properties in the output.
resource_list = resource_client.resources.list_by_resource_group(
    "myResourceGroup",
    expand = "createdTime,changedTime"
)

# Show the resources in formatted output
column_width = 36

print("Resource".ljust(column_width) + "Type".ljust(column_width)
    + "Create date".ljust(column_width) + "Change date".ljust(column_width))
print("-" * (column_width * 4))

for resource in list(resource_list):
    print(f"{resource.name:<{column_width}}{resource.type:<{column_width}}"
       f"{str(resource.created_time):<{column_width}}{str(resource.changed_time):<{column_width}}")
```

### <a name="authentication-in-the-code"></a>Authentifizierung im Code

[!INCLUDE [cli-auth-note](includes/cli-auth-note.md)]

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [AzureCliCredential (azure.identity)](/python/api/azure-identity/azure.identity.azureclicredential)
- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient)

## <a name="4-run-the-scripts"></a>4: Ausführen der Skripts

Auflisten aller Ressourcengruppen im Abonnement:

```cmd
python list_groups.py
```

Auflisten aller Ressourcen in einer Ressourcengruppe:

```cmd
python list_resources.py
```

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Mit dem folgenden Azure CLI-Befehl werden Ressourcengruppen in einem Abonnement mithilfe der JSON-Ausgabe aufgelistet:

```azurecli
az group list
```

Der folgende Befehl listet Ressourcen in „myResourceGroup“ in der Region „centralus“ auf. (Das Argument „location“ ist zum Identifizieren eines bestimmten Rechenzentrums erforderlich.)

```azurecli
az resource list --resource group myResourceGroup --location centralus
```

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
- [Verwenden verwalteter Azure-Datenträger mit den Azure-Bibliotheken (SDK) für Python](azure-sdk-samples-managed-disks.md)
- [Kurze Umfrage zum Azure SDK für Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)
