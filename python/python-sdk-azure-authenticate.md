---
title: Authentifizieren von Apps mit den Azure-Verwaltungsbibliotheken für Python
description: Authentifizieren einer Python-App mit Azure-Diensten mithilfe der Azure-Verwaltungsbibliotheken des SDK
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: abf1b0b37d782addfd2b772fb7d382ce2815e891
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709817"
---
# <a name="authenticate-by-using-the-azure-management-libraries-for-python"></a>Authentifizieren mit den Azure-Verwaltungsbibliotheken für Python

In diesem Artikel erfahren Sie, wie Sie mithilfe der Verwaltungsbibliotheken des Python SDK eine Anwendung mit Azure Active Directory (Azure AD) unter Verwendung eines Dienstprinzipals authentifizieren. Der Dienstprinzipal ist eine Identität für eine Anwendung, der in Azure AD registriert ist und es der Anwendung ermöglicht, gemäß ihren Berechtigungen auf Ressourcen zuzugreifen oder diese zu ändern.

Zum Registrieren von Anwendungen müssen Sie zunächst eine Active Directory-Instanz mit einem geeigneten Mandanten für Ihre Organisation erstellen. Befolgen Sie hierzu die Anweisungen unter [Schnellstart: Erstellen eines neuen Mandanten in Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). Wenn die Active Directory-Instanz vorhanden ist, befolgen Sie die Anweisungen im Artikel [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](/azure/active-directory/develop/howto-create-service-principal-portal). Im Rahmen dieses Artikels registrieren Sie eine Anwendung, [rufen die IDs des Mandanten und der Anwendung (Client) für den Dienstprinzipal ab](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)) und richten ein [Anwendungsgeheimnis](/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret) ein, mit dem Sie sich über Python-Code authentifizieren.

Sobald Sie diese Werte haben, gibt es verschiedene Möglichkeiten, sich damit unter Verwendung der Python SDK-Bibliotheken zu authentifizieren. Das Ergebnis jeder Methode ist das SDK-Clientobjekt, das Sie beim Zugreifen auf andere Ressourcen über Code verwenden.

Es wird dringend empfohlen, die Mandanten-ID, die Client-ID und das Geheimnis in [Azure Key Vault](/azure/key-vault/) zu speichern, damit diese Werte nicht in Ihren Systemen oder in der Quellcodeverwaltung vorhanden sind. Sie können die Werte problemlos abrufen, wenn Sie sie benötigen.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="mgmt-auth-file"></a>Authentifizieren mit einer JSON-Datei

Bei dieser Methode erstellen Sie eine JSON-Datei, die die erforderlichen Anmeldeinformationen für den Dienstprinzipal enthält. Anschließend erstellen Sie das SDK-Clientobjekt mithilfe der Informationen in der Datei.

1. Erstellen Sie eine JSON-Datei (mit einem beliebigen Namen, etwa *app_credentials.json*) im folgenden Format. Ersetzen Sie die vier Platzhalter durch Ihre Azure-Abonnement-ID, die Azure AD-Mandanten-ID, die Anwendungs-ID (Client) und das Geheimnis:

    ```json
    {
        "subscriptionId": "<azure_aubscription_id>",
        "tenantId": "<tenant_id>",
        "clientId": "<application_id>",
        "clientSecret": "<application_secret>",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com/",
        "activeDirectoryGraphResourceId": "https://graph.windows.net/",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

    > [!TIP]
    > Sie können eine Datei mit Anmeldeinformationen abrufen, in der sich die Abonnement-ID bereits an der richtigen Stelle befindet. Melden Sie sich dazu mit dem Befehl [az login](/cli/azure/reference-index#az-login) gefolgt vom Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) bei Azure an:
    >
    > ```azurecli
    > az login
    > az ad sp create-for-rbac --sdk-auth > credentials.json
    > ```
    >
    > Sie können dann die Werte für `tenantId`, `clientId` und `clientSecret` für die jeweilige Anwendung ersetzen, statt die allgemeinen Werte zu verwenden.

1. Speichern Sie diese Datei an einem sicheren Ort, auf den der Code zugreifen kann.

1. Verwenden Sie die Methode [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-), um das Clientobjekt zu erstellen, und ersetzen Sie `<path_to_file>` durch den Pfad zur JSON-Datei:

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.compute import ComputeManagementClient

    client = get_client_from_auth_file(ComputeManagementClient, auth_path=<path_to_file>)
    ```

1. Sie können den Pfad zur Datei alternativ in einer Umgebungsvariablen mit dem Namen `AZURE_AUTH_LOCATION` speichern und das Argument `auth_path` weglassen.

## <a name="authenticate-with-a-json-dictionary"></a>Authentifizieren mit einem JSON-Wörterbuch

Statt wie im vorherigen Abschnitt beschrieben eine Datei zu verwenden, können Sie den erforderlichen JSON-Code in einer Variablen erstellen und stattdessen [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-) aufrufen. In diesem Fall sollten Sie die Mandanten-ID, die Client-ID und das Geheimnis immer an einem sicheren Ort wie [Azure Key Vault](/azure/key-vault/) speichern.

```python
   from azure.common.client_factory import get_client_from_auth_file
   from azure.mgmt.compute import ComputeManagementClient

    # Retrieve tenant_id, client_id, and client_secret from Azure KeyVault

   config_dict = {
       "subscriptionId": "bfc42d3a-65ca-11e7-95cf-ecb1d756380e",
        "tenantId": tenant_id,
       "clientId": client_id,
       "clientSecret": client_secret,
       "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
       "resourceManagerEndpointUrl": "https://management.azure.com/",
       "activeDirectoryGraphResourceId": "https://graph.windows.net/",
       "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
       "galleryEndpointUrl": "https://gallery.azure.com/",
       "managementEndpointUrl": "https://management.core.windows.net/"
   }
   client = get_client_from_json_dict(ComputeManagementClient, config_dict)
```

## <a name="mgmt-auth-token"></a>Authentifizieren mit Tokenanmeldeinformationen

Wenn Sie die Anmeldeinformationen aus einem sicheren Speicher wie [Azure Key Vault](/azure/key-vault/) abrufen, erstellen Sie zunächst ein Objekt vom Typ [ServicePrincipalCredentials] und anschließend unter Verwendung dieser Anmeldeinformationen und der Abonnement-ID eine Instanz des Clients:

```python
from azure.mgmt.compute import ComputeManagementClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve credentials from secure storage. Never hard-code credentials into code.

credentials = ServicePrincipalCredentials(tenant = <tenant_id>,
    client_id = <client_id>, secret = <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

Wenn Sie mehr Kontrolle benötigen, verwenden Sie die [ADAL (Azure Active Directory Authentication Library, Active Directory-Authentifizierungsbibliothek) für Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) und den SDK-ADAL-Wrapper:

```python
from azure.mgmt.compute import ComputeManagementClient
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve credentials from secure storage. Never hard-code credentials into code.

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)

credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, <client_id>, <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

> [!NOTE]
> Geben Sie bei Verwendung einer Azure-Sovereign Cloud beim Erstellen des Verwaltungsclients die entsprechende Basis-URL (mithilfe einer Konstante in `msrestazure.azure_cloud`) an.
>
> ```python
> client = ComputeManagementClient(credentials, subscription_id,
>     base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
> ```

### <a name="mgmt-auth-legacy"></a>Authentifizieren mit Tokenanmeldeinformationen (veraltet)

Bevor die [Azure Active Directory-Authentifizierungsbibliothek (ADAL) für Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) verfügbar war, wurde die Klasse `UserPassCredentials` verwendet. Diese Klasse wird als veraltet betrachtet und sollte nicht mehr verwendet werden, da sie die zweistufige Authentifizierung nicht unterstützt.

```python
from azure.common.credentials import UserPassCredentials

# DEPRECATED - legacy purposes only - use ADAL instead
credentials = UserPassCredentials(
    'user@domain.com',
    'my_smart_password'
)
```

## <a name="mgmt-auth-msi"></a>Authentifizieren mit verwalteten Azure-Identitäten

Über verwaltete Azure-Identitäten kann eine Ressource in Azure ganz einfach authentifiziert werden, ohne spezielle Anmeldeinformationen verwenden zu müssen.

Wenn Sie verwaltete Identitäten nutzen möchten, müssen Sie über eine andere Azure-Ressource (etwa eine Azure-Funktion oder einen virtuellen Computer) eine Verbindung mit Azure herstellen. Informationen zum Konfigurieren einer verwalteten Identität für eine Ressource finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure-Befehlszeilenschnittstelle](/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) und [Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer für die Anmeldung](/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in).

```python
from msrestazure.azure_active_directory import MSIAuthentication
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

# Create MSI Authentication
credentials = MSIAuthentication()

# Create a Subscription Client
subscription_client = SubscriptionClient(credentials)
subscription = next(subscription_client.subscriptions.list())
subscription_id = subscription.subscription_id

# Create a Resource Management client
client = ResourceManagementClient(credentials, subscription_id)

# List resource groups as an example. The only limit is what role and policy are assigned to this MSI token.
for resource_group in resource_client.resource_groups.list():
    print(resource_group.name)
```

## <a name="mgmt-auth-cli"></a>CLI-basierte Authentifizierung (nur für Entwicklungszwecke)

Das SDK kann mithilfe Ihres aktiven Azure CLI-Abonnements einen Client erstellen, nachdem Sie `az login` ausgeführt haben. Das SDK verwendet die Standardabonnement-ID. Alternativ können Sie das Abonnement mit [az account](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli) festlegen.

Diese Option sollte nur für Entwicklungszwecke verwendet werden.

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_cli_profile(ComputeManagementClient)
```
