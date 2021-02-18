---
title: Herstellen einer Verbindung mit allen Regionen unter Verwendung der Azure-Bibliotheken für Python (mehrere Clouds)
description: Verwenden des Moduls „azure_cloud“ von „msrestazure“, um eine Verbindung mit Azure in verschiedenen unabhängigen Regionen herzustellen
ms.date: 11/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c5d074a8e775fc1766df1ab8c4ed73aabc333fcc
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004753"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>Mehrere Clouds: Herstellen einer Verbindung mit allen Regionen unter Verwendung der Azure-Bibliotheken für Python

Sie können die Azure-Bibliotheken für Python verwenden, um eine Verbindung mit allen Regionen herzustellen, in denen Azure [verfügbar](https://azure.microsoft.com/regions/services) ist.

Standardmäßig sind die Azure-Bibliotheken für die Verbindungsherstellung mit der globalen Azure-Cloud konfiguriert.

## <a name="using-pre-defined-sovereign-cloud-constants"></a>Verwenden von vordefinierten Sovereign Cloud-Konstanten

Vordefinierte Sovereign Cloud-Konstanten werden vom Modul `azure_cloud` der Bibliothek `msrestazure` (ab 0.4.11) bereitgestellt:

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

Importieren Sie zum Verwenden einer Definition die entsprechende Konstante aus `msrestazure.azure_cloud`, und wenden Sie sie beim Erstellen von Clientobjekten an. 

Bei Verwendung von `DefaultAzureCredential` (wie im folgenden Beispiel gezeigt) muss auch der entsprechende Wert von `CLOUD.endpoints.active_directory` verwendet werden.

```python
import os
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD as CLOUD
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential

# Assumes the subscription ID and tenant ID to use are in the AZURE_SUBSCRIPTION_ID and
# AZURE_TENANT_ID environment variables
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]

# When using sovereign domains (that is, any cloud other than AZURE_PUBLIC_CLOUD),
# you must use an authority with DefaultAzureCredential.
credential = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)

resource_client = ResourceManagementClient(
    credential, subscription_id,
    base_url=CLOUD.endpoints.resource_manager,
    credential_scopes=[CLOUD.endpoints.resource_manager + "/.default"])

subscription_client = SubscriptionClient(
    credential,
    base_url=CLOUD.endpoints.resource_manager,
    credential_scopes=[CLOUD.endpoints.resource_manager + "/.default"])
```
  
## <a name="using-your-own-cloud-definition"></a>Verwenden Ihrer eigenen Clouddefinition

Im folgenden Code wird `get_cloud_from_metadata_endpoint` mit dem Azure Resource Manager-Endpunkt für eine private Cloud (z. B. eine Cloud, die auf Azure Stack basiert) verwendet:

```python
import os
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
from azure.identity import DefaultAzureCredential
from azure.profiles import KnownProfiles

# Assumes the subscription ID and tenant ID to use are in the AZURE_SUBSCRIPTION_ID and
# AZURE_TENANT_ID environment variables
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")

# When using a private cloud, you must use an authority with DefaultAzureCredential.
# The active_directory endpoint should be a URL like https://login.microsoftonline.com.
credential = DefaultAzureCredential(authority=stack_cloud.endpoints.active_directory, tenant_id=tenant_id)

resource_client = ResourceManagementClient(
    credential, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager,
    profile=KnownProfiles.v2019_03_01_hybrid,
    credential_scopes=[stack_cloud.endpoints.active_directory_resource_id + "/.default"])

subscription_client = SubscriptionClient(
    credential,
    base_url=stack_cloud.endpoints.resource_manager,
    profile=KnownProfiles.v2019_03_01_hybrid,
    credential_scopes=[stack_cloud.endpoints.active_directory_resource_id + "/.default"])
```
