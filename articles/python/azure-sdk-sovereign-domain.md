---
title: Herstellen einer Verbindung mit allen Regionen unter Verwendung der Azure-Bibliotheken für Python (mehrere Clouds)
description: Verwenden des Moduls „azure_cloud“ von „msrestazure“, um eine Verbindung mit Azure in verschiedenen unabhängigen Regionen herzustellen
ms.date: 07/13/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: caf3f90fb9d21535dd6bf8974a6bdc719f3758ab
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983180"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-libraries-for-python"></a>Mehrere Clouds: Herstellen einer Verbindung mit allen Regionen unter Verwendung der Azure-Bibliotheken für Python

Sie können die Azure-Bibliotheken für Python verwenden, um eine Verbindung mit allen Regionen herzustellen, in denen Azure [verfügbar](https://azure.microsoft.com/regions/services) ist.

Standardmäßig sind die Azure-Bibliotheken für die Verbindungsherstellung mit der globalen Azure-Umgebung konfiguriert.

## <a name="using-pre-defined-sovereign-cloud-constants"></a>Verwenden von vordefinierten Sovereign Cloud-Konstanten

Vordefinierte Sovereign Cloud-Konstanten werden vom `azure_cloud`-Modul von `msrestazure` (0.4.11+) bereitgestellt:

- `AZURE_PUBLIC_CLOUD`
- `AZURE_CHINA_CLOUD`
- `AZURE_US_GOV_CLOUD`
- `AZURE_GERMAN_CLOUD`

Wenn Sie eine Konstante über den gesamten Code anwenden möchten, definieren Sie eine Umgebungsvariable mit dem Namen `AZURE_CLOUD` mithilfe eines der Werte in der vorherigen Liste. (`AZURE_PUBLIC_CLOUD` ist der Standardwert.)

Zum Anwenden einer Konstante in bestimmten Vorgängen importieren Sie die gewünschte Konstante aus `msrest.azure_cloud`, und verwenden Sie sie beim Erstellen von Anmeldeinformationen und Clientobjekten:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

credentials = UserPassCredentials(login, password,
    cloud_environment=AZURE_CHINA_CLOUD)

client = ResourceManagementClient(credentials,
    subscription_id, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```
  
## <a name="using-your-own-cloud-definition"></a>Verwenden Ihrer eigenen Clouddefinition

Im folgenden Code wird `get_cloud_from_metadata_endpoint` mit dem Azure Resource Manager-Endpunkt für eine private Cloud (z. B. eine Cloud, die auf Azure Stack basiert) verwendet:

```python
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

stack_cloud = get_cloud_from_metadata_endpoint("https://contoso-azurestack-arm-endpoint.com")
credentials = UserPassCredentials(login, password,
    cloud_environment=stack_cloud)

client = ResourceManagementClient(credentials, subscription_id,
    base_url=stack_cloud.endpoints.resource_manager)
```

## <a name="using-adal"></a>Verwenden von ADAL

Beim Herstellen einer Verbindung mit einer anderen Region sollten Sie die folgenden Fragen beachten:

- An welchem Endpunkt soll nach einem Token (Authentifizierung) gefragt werden?
- An welchem Endpunkt wird dieses Token (Nutzung) verwendet?

Im Folgenden sehen Sie ein allgemeines Beispiel:

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Public Azure - default values
authentication_endpoint = 'https://login.microsoftonline.com/'
azure_endpoint = 'https://management.azure.com/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-government"></a>Azure Government

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Government
authentication_endpoint = 'https://login.microsoftonline.us/'
azure_endpoint = 'https://management.usgovcloudapi.net/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-germany"></a>Azure Deutschland

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure Germany
authentication_endpoint = 'https://login.microsoftonline.de/'
azure_endpoint = 'https://management.microsoftazure.de/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure China
authentication_endpoint = 'https://login.chinacloudapi.cn/'
azure_endpoint = 'https://management.chinacloudapi.cn/'

context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    azure_endpoint, client_id, password)

subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(credentials,
    subscription_id, base_url=azure_endpoint)
```
