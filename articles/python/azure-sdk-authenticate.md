---
title: Authentifizieren von Python-Anwendungen mit Azure-Diensten
description: Authentifizieren einer Python-App mit Azure-Diensten mithilfe der Azure-Verwaltungsbibliotheken des SDK
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 8dd434c0a18c0a263573188e04a54f48afcf2b0d
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2020
ms.locfileid: "83403689"
---
# <a name="how-to-authenticate-python-apps-with-azure-services"></a>Authentifizieren von Python-Apps mit Azure-Diensten

Beim Schreiben von App-Code mit dem Azure SDK für Python verwenden Sie das folgende Muster für den Zugriff auf Azure-Ressourcen:

1. Abrufen von Anmeldeinformationen (in der Regel ein einmaliger Vorgang).
1. Verwenden der Anmeldeinformationen, um ein vom SDK bereitgestelltes Clientobjekt für eine Ressource abzurufen.
1. Versuchen, auf die Ressource über das Clientobjekt zuzugreifen oder diese zu ändern, wodurch eine HTTP-Anforderung an die REST-API der Ressource generiert wird.

Die Anforderung an die REST-API ist der Punkt, an dem Azure die Identität der App authentifiziert, wie durch das Anmeldeinformationenobjekt beschrieben. Azure überprüft dann, ob diese Identität autorisiert ist, die angeforderte Aktion auszuführen. Wenn die Identität dazu nicht autorisiert ist, schlägt der Vorgang fehl. (Das Erteilen von Berechtigungen ist abhängig vom Ressourcentyp, z. B. Azure Key Vault, Azure Storage usw. Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp.)

Die an diesen Prozessen beteiligte Identität (also die vom Anmeldeinformationsobjekt beschriebene Identität) wird in der Regel durch einen *Sicherheitsprinzipal* definiert, der einen Benutzer, eine Gruppe, einen Dienst oder eine App darstellt. Eine Reihe von Authentifizierungsmethoden, die in diesem Artikel beschrieben werden, verwenden einen expliziten Prinzipal, der in der Regel als *Dienstprinzipal* bezeichnet wird.

Für die meisten Cloudanwendungen empfiehlt es sich jedoch, das `DefaultAzureCredential`-Objekt (wie im ersten Abschnitt erläutert) zu verwenden, da es Ihnen den Einsatz eines Dienstprinzipals für die Anwendung vollständig abnimmt.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="authenticate-with-defaultazurecredential"></a>Authentifizieren mit DefaultAzureCredential

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Obtain the credential object
credential = DefaultAzureCredential()

# Create the SDK client object to access Key Vault secrets.
vault_url = os.environ["KEY_VAULT_URL"]
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to retrieve a secret value. The operation fails if the principal
# cannot be authenticated or is not authorized for the operation in question.
retrieved_secret = client.get_secret("secret-name-01")
```

Die [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python)-Klasse aus der [`azure-identity`](/python/api/azure-identity/azure.identity?view=azure-python)-Bibliothek bietet die einfachste und empfohlene Authentifizierungsmethode.

Der vorherige Code verwendet `DefaultAzureCredential` beim Zugriff auf Azure Key Vault, bei dem die URL des Schlüsseltresors in einer Umgebungsvariablen mit dem Namen `KEY_VAULT_URL` verfügbar ist. Der Code implementiert eindeutig das Muster, das am Anfang des Artikels beschrieben wird: Abrufen eines Anmeldeinformationenobjekts, Erstellen eines SDK-Clientobjekts und anschließender Versuch, einen Vorgang mit diesem Clientobjekt auszuführen.

Erneut erfolgen Authentifizierung und Autorisierung erst im letzten Schritt. Das Erstellen des SDK [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python)-Objekts umfasst keine Kommunikation mit der fraglichen Ressource. Das `SecretClient`-Objekt ist lediglich ein Wrapper um die zugrunde liegende Azure-REST-API und ist nur im Laufzeitspeicher der App vorhanden. Erst wenn Sie die [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-)-Methode aufrufen, generiert das Clientobjekt den entsprechenden REST-API-Aufruf an Azure. Der Endpunkt von Azure für `get_secret` authentifiziert dann die Identität des Aufrufers und überprüft die Autorisierung.

Wenn Code in Azure bereitgestellt und ausgeführt wird, verwendet `DefaultAzureCredential` automatisch die vom System zugewiesene („verwaltete“) Identität, die Sie für die App in dem Dienst aktivieren können, auf dem sie gehostet wird. Beispielsweise aktivieren Sie für eine Web-App, die für Azure App Service bereitgestellt wird, die verwaltete Identität über die Option **Identität** > **Vom System zugewiesen** im Azure-Portal oder mithilfe des `az webapp identity assign`-Befehls in der Azure CLI. Berechtigungen für bestimmte Ressourcen, z. B. Azure Storage oder Azure Key Vault, werden dieser Identität ebenfalls mithilfe des Azure-Portals oder der Azure CLI zugewiesen. In diesen Fällen maximiert diese von Azure verwaltete Identität die Sicherheit, da Sie in Ihrem Code niemals explizit mit Dienstprinzipalen interagieren.

Wenn Sie den Code lokal ausführen, verwendet `DefaultAzureCredential` automatisch den Dienstprinzipal, der von den Umgebungsvariablen `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` und `AZURE_CLIENT_SECRET` beschrieben wird. Das SDK-Clientobjekt schließt diese Werte dann (sicher) in den HTTP-Anforderungsheader ein, wenn der API-Endpunkt aufgerufen wird. Es sind keine Codeänderungen erforderlich. Ausführliche Informationen zum Erstellen des Dienstprinzipals und zum Einrichten der Umgebungsvariablen finden Sie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure: Konfigurieren der Authentifizierung](configure-local-development-environment.md#configure-authentication).

In beiden Fällen müssen der beteiligten Identität Berechtigungen für die entsprechende Ressource zugewiesen werden: Dies wird in der Dokumentation für die einzelnen Dienste beschrieben. Ausführliche Informationen zu Key Vault-Berechtigungen, wie für den vorherigen Code benötigt, finden Sie unter [Bereitstellen von Key Vault-Authentifizierung mit einer Zugriffssteuerungsrichtlinie](/azure/key-vault/general/group-permissions-for-apps).

<a name="cli-auth-note"></a>
> [!IMPORTANT]
> In Zukunft verwendet `DefaultAzureCredential` die Identität, die bei der Azure CLI über `az login` angemeldet ist, wenn die Dienstprinzipal-Umgebungsvariablen nicht verfügbar sind. Wenn Sie Besitzer oder Administrator Ihres Abonnements sind, ist das praktische Ergebnis dieses Features, dass Ihr Code inhärenten Zugriff auf die meisten Ressourcen in diesem Abonnement besitzt, ohne dass Sie bestimmte Berechtigungen zuweisen müssen. Dieses Verhalten ist für Experimente praktisch. Es wird jedoch dringend empfohlen, dass Sie bestimmte Dienstprinzipale verwenden und bestimmte Berechtigungen zuweisen, wenn Sie beginnen, Produktionscode zu schreiben, da Sie dann lernen, wie Sie den unterschiedlichen Identitäten genaue Berechtigungen zuweisen und diese Berechtigungen in Testumgebungen vor der Bereitstellung in der Produktion genau überprüfen können.

### <a name="using-defaultazurecredential-with-sdk-management-libraries"></a>Verwenden von DefaultAzureCredential mit SDK-Verwaltungsbibliotheken

```python
# WARNING: this code presently fails!

from azure.identity import DefaultAzureCredential

# azure.mgmt.resource is an Azure SDK management library
from azure.mgmt.resource import SubscriptionClient

# Attempt to retrieve the subscription ID
credential = DefaultAzureCredential()
subscription_client = SubscriptionClient(credential)

# The following line produces a "no attribute 'signed_session'" error:
subscription = next(subscription_client.subscriptions.list())

print(subscription.subscription_id)
```

Zurzeit funktioniert `DefaultAzureCredential` nur mit Azure SDK-Clientbibliotheken („Datenebene“) und funktioniert nicht mit Azure SDK-Verwaltungsbibliotheken, deren Namen mit `azure-mgmt` beginnen, wie in diesem Codebeispiel gezeigt. Der Aufruf von `subscription_client.subscriptions.list()` schlägt mit dem eher vagen folgenden Fehler fehl: „Das DefaultAzureCredential-Objekt hat kein Attribut 'signed_session'.“ Dieser Fehler tritt auf, weil die aktuellen SDK-Verwaltungsbibliotheken davon ausgehen, dass das Anmeldeinformationenobjekt eine `signed_session`-Eigenschaft enthält, die `DefaultAzureCredential` fehlt.

Bis diese Bibliotheken später im Jahr 2020 aktualisiert werden, können Sie den Fehler auf zwei Arten umgehen:

1. Verwenden Sie eine der anderen Authentifizierungsmethoden, die in den nachfolgenden Abschnitten dieses Artikels beschrieben werden, die für Code geeignet ist, der *nur* SDK-Verwaltungsbibliotheken verwendet und nicht in der Cloud bereitgestellt wird. in diesem Fall können Sie nur auf lokale Dienstprinzipale zurückgreifen.

1. Verwenden Sie anstelle von `DefaultAzureCredential` die [CredentialWrapper-Klasse („cred_wrapper.py“)](https://gist.github.com/lmazuel/cc683d82ea1d7b40208de7c9fc8de59d), die von einem Mitglied des Azure SDK-Entwicklungsteams bereitgestellt wird. Nachdem Microsoft die aktualisierten Verwaltungsbibliotheken veröffentlicht hat, können Sie einfach wieder zu `DefaultAzureCredential` zurückkehren. Diese Methode hat den Vorteil, dass Sie die gleichen Anmeldeinformationen sowohl mit dem SDK-Client als auch mit den Verwaltungsbibliotheken verwenden können, und sie funktioniert sowohl lokal als auch in der Cloud.

    Wenn Sie eine Kopie von *cred_wrapper. py* in Ihren Projektordner heruntergeladen haben, sieht der vorherige Code wie folgt aus:

    ```python
    from cred_wrapper import CredentialWrapper
    from azure.mgmt.resource import SubscriptionClient

    credential = CredentialWrapper()
    subscription_client = SubscriptionClient(credential)
    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

    Nachdem die Verwaltungsbibliotheken aktualisiert wurden, können Sie `DefaultAzureCredential` direkt verwenden.

## <a name="other-authentication-methods"></a>Andere Authentifizierungsmethoden

Obwohl `DefaultAzureCredential` in den meisten Szenarien die empfohlene Authentifizierungsmethode ist, sind andere Methoden mit den folgenden Einschränkungen verfügbar:

- Die meisten Methoden funktionieren mit expliziten Dienstprinzipalen und nutzen die verwaltete Identität nicht für Code, der in der Cloud bereitgestellt wird. Wenn sie mit Produktionscode verwendet werden, müssen Sie unterschiedliche Dienstprinzipale für Ihre Cloudanwendungen verwalten und warten.

- Einige Methoden (z. B. CLI-basierte Authentifizierung) funktionieren nur mit lokalen Skripts und können nicht mit Produktionscode verwendet werden.

Dienstprinzipale für Anwendungen, die in der Cloud bereitgestellt werden, werden in Ihrem Abonnement-Active Directory verwaltet. Weitere Informationen finden Sie unter [Verwalten von Dienstprinzipalen](how-to-manage-service-principals.md).

### <a name="authenticate-with-a-json-file"></a>Authentifizierung mit einer JSON-Datei

Bei dieser Methode erstellen Sie eine JSON-Datei, die die erforderlichen Anmeldeinformationen für den Dienstprinzipal enthält. Anschließend erstellen Sie mithilfe dieser Datei ein SDK-Clientobjekt. Diese Methode kann sowohl lokal als auch in der Cloud verwendet werden. 

1. Erstellen Sie eine JSON-Datei mit dem folgenden Format:

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

    Ersetzen Sie die vier Platzhalter durch Ihre Azure-Abonnement-ID, die Mandanten-ID, die Client-ID und das Clientgeheimnis.

    > [!TIP]
    > Wie unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-for-development) erläutert wird, können Sie den Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) mit dem `--sdk-auth`-Parameter verwenden, um dieses JSON-Format direkt zu generieren.

1. Speichern Sie die Datei unter einem Namen wie *credentials.json* an einem sicheren Speicherort, auf den Ihr Code zugreifen kann. Um Ihre Anmeldeinformationen zu schützen, müssen Sie diese Datei aus der Quellcodeverwaltung ausschließen und dürfen sie nicht für andere Entwickler freigeben. Das heißt, die Mandanten-ID, die Client-ID und das Clientgeheimnis eines Dienstprinzipals sollten immer auf Ihrer Entwicklungsarbeitsstation isoliert bleiben.

1. Erstellen Sie eine Umgebungsvariable namens `AZURE_AUTH_LOCATION` mit dem Pfad zur JSON-Datei als Wert:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_AUTH_LOCATION="../credentials.json"
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_AUTH_LOCATION=../credentials.json
    ```

    In diesen Beispielen wird davon ausgegangen, dass die JSON-Datei den Namen *credentials.json* trägt und sich im übergeordneten Ordner Ihres Projekts befindet.

    ---

1. Verwenden Sie die Methode [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-), um das Clientobjekt zu erstellen:

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.resource import SubscriptionClient

    # This form of get_client_from_auth_file relies on the AZURE_AUTH_LOCATION
    # environment variable.
    subscription_client = get_client_from_auth_file(SubscriptionClient)

    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

Sie können den Pfad alternativ direkt im Code angeben, indem Sie das `auth_path`-Argument verwenden. In diesem Fall ist die Umgebungsvariable nicht erforderlich:

```python
subscription_client = get_client_from_auth_file(SubscriptionClient, auth_path="../credentials.json")
```

### <a name="authenticate-with-a-json-dictionary"></a>Authentifizieren mit einem JSON-Wörterbuch

```python
import os
from azure.common.client_factory import get_client_from_json_dict
from azure.mgmt.resource import SubscriptionClient

# Retrieve the IDs and secret to use in the JSON dictionary
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

config_dict = {
   "subscriptionId": subscription_id,
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

subscription_client = get_client_from_json_dict(SubscriptionClient, config_dict)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Statt wie im vorherigen Abschnitt beschrieben eine Datei zu verwenden, können Sie die erforderlichen JSON-Daten in einer Variablen erstellen und [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-) aufrufen. In diesem Code wird davon ausgegangen, dass Sie die Umgebungsvariablen erstellt haben, die unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-for-development) beschrieben werden. Für Code, der in der Cloud bereitgestellt wird, können Sie diese Umgebungsvariablen auf Ihrer Server-VM oder als Anwendungseinstellungen erstellen, wenn Sie einen Plattformdienst wie Azure App Service und Azure Functions verwenden.

Sie können Werte auch in Azure Key Vault speichern und zur Laufzeit abrufen, anstatt Umgebungsvariablen zu verwenden.

### <a name="authenticate-with-token-credentials"></a>Authentifizieren mit Zugriffstoken-Anmeldeinformationen

```python
import os
from azure.mgmt.resource import SubscriptionClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

credential = ServicePrincipalCredentials(tenant=tenant_id, client_id=client_id, secret=client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Bei dieser Methode erstellen Sie ein [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials?view=azure-python)-Objekt mithilfe von Anmeldeinformationen, die aus sicherem Speicher wie Azure Key Vault oder Umgebungsvariablen abgerufen werden. Im vorherigen Code wird davon ausgegangen, dass Sie die Umgebungsvariablen erstellt haben, die unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-for-development) beschrieben werden.

Mit dieser Methode können Sie eine [Azure-unabhängige oder nationale Cloud](/azure/active-directory/develop/authentication-national-cloud) anstelle der öffentlichen Azure-Cloud verwenden, indem Sie ein `base_url`-Argument für das Clientobjekt angeben:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

Konstanten für unabhängige Clouds finden Sie in der [msrestazure.azure_cloud-Bibliothek](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="authenticate-with-token-credentials-and-an-adal-context"></a>Authentifizierung mit Tokenanmeldeinformationen und einem ADAL-Kontext

Wenn Sie mehr Kontrolle bei Verwendung von Tokenanmeldeinformationen benötigen, verwenden Sie die [ADAL (Azure Active Directory Authentication Library, Active Directory-Authentifizierungsbibliothek) für Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) und den SDK-ADAL-Wrapper:

```python
import os, adal
from azure.mgmt.resource import SubscriptionClient
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + tenant_id)

credential = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, client_id, client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Wenn Sie die ADAL-Bibliothek benötigen, führen Sie `pip install adal` aus.

Mit dieser Methode können Sie eine [Azure-unabhängige oder nationale Cloud](/azure/active-directory/develop/authentication-national-cloud) anstelle der öffentlichen Azure-Cloud verwenden.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

# ...

LOGIN_ENDPOINT = AZURE_CHINA_CLOUD.endpoints.active_directory
RESOURCE = AZURE_CHINA_CLOUD.endpoints.active_directory_resource_id
```

Ersetzen Sie `AZURE_PUBLIC_CLOUD` einfach durch die entsprechende Konstante für die unabhängige Cloud aus der [msrestazure.azure_cloud-Bibliothek](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="cli-based-authentication-development-purposes-only"></a>CLI-basierte Authentifizierung (nur für Entwicklungszwecke)

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import SubscriptionClient

subscription_client = get_client_from_cli_profile(SubscriptionClient)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Bei dieser Methode erstellen Sie ein Clientobjekt mit den Anmeldeinformationen des Benutzers, der mit dem Azure CLI-Befehl `az login` angemeldet ist.

Das SDK verwendet die Standardabonnement-ID. Alternativ können Sie das Abonnement mit [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli) festlegen.

Diese Methode sollte nur für frühe experimentelle- und Entwicklungszwecke verwendet werden, da ein angemeldeter Benutzer in der Regel über Besitzer- oder Administratorrechte verfügt und ohne zusätzliche Berechtigungen auf die meisten Ressourcen zugreifen kann. Weitere Informationen finden Sie im vorherigen Hinweis zum [Verwenden von CLI-Anmeldeinformationen mit `DefaultAzureCredential`](#cli-auth-note).

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Veraltet: Authentifizierung mit UserPassCredentials

Bevor die [Azure Active Directory-Authentifizierungsbibliothek (ADAL) für Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) verfügbar war, musste die jetzt veraltete Klasse [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials?view=azure-python) verwendet werden. Diese Klasse unterstützt keine zweistufige Authentifizierung und sollte nicht mehr verwendet werden.

## <a name="see-also"></a>Weitere Informationen

- [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md)
- [Beispiel: Verwenden des Azure SDK mit Azure Storage](azure-sdk-example-storage.md)
