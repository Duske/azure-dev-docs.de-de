---
title: Authentifizieren von Python-Anwendungen mit Azure-Diensten
description: Hier erfahren Sie, wie Sie die Anmeldeinformationsobjekte abrufen, die erforderlich sind, um eine Python-App mit Azure-Diensten unter Verwendung der Azure-Bibliotheken zu authentifizieren.
ms.date: 01/19/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 19a69cf0379dd290893724db6527ebd30b5bab63
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759469"
---
# <a name="how-to-authenticate-and-authorize-python-apps-on-azure"></a>Authentifizieren und Autorisieren von Python-Apps in Azure

Die meisten Cloudanwendungen, die in Azure bereitgestellt werden, müssen auf andere Azure-Ressourcen zugreifen, z. B. Speicher, Datenbanken, gespeicherte Geheimnisse usw. Für den Zugriff auf diese Ressourcen muss die Anwendung sowohl authentifiziert als auch autorisiert werden:

- Bei der **Authentifizierung** wird die Identität der App mit Azure Active Directory überprüft.

- Bei der **Autorisierung** wird ermittelt, welche Vorgänge von der authentifizierten App für eine Ressource durchgeführt werden können. Die autorisierten Vorgänge werden durch die **Rollen** definiert, die der App-Identität für die jeweilige Ressource zugewiesen sind. In einigen Fällen, z. B. bei Azure Key Vault, wird die Autorisierung zudem durch zusätzliche **Zugriffsrichtlinien** bestimmt, die der App-Identität zugewiesen sind.

In diesem Artikel werden die Details der Authentifizierung und Autorisierung beschrieben:

- Zuweisen einer App-Identität
- Gewähren von Berechtigungen für eine Identität
- Ablauf und Zeitpunkt der Authentifizierung und Autorisierung
- Unterschiedliche Verfahren, mit denen eine App für Azure mit den Azure-Bibliotheken authentifiziert wird. Die Verwendung von `DefaultAzureCredential` wird empfohlen, aber sie ist nicht zwingend erforderlich.

## <a name="how-to-assign-an-app-identity"></a>Zuweisen einer App-Identität

In Azure wird eine App-Identität über einen **Dienstprinzipal** definiert. (Bei einem Dienstprinzipal handelt es sich um eine bestimmte Art von „Sicherheitsprinzipal“, der zum Identifizieren einer App oder eines Diensts verwendet wird. Es wird also Code verwendet, und die Identifizierung wird nicht von einem Benutzer oder einer Gruppe von Benutzern durchgeführt.)

Welcher Dienstprinzipal genutzt wird, hängt davon ab, wo die App ausgeführt wird. Dies ist in den folgenden Abschnitten beschrieben.

### <a name="identity-when-running-the-app-on-azure"></a>Identität beim Ausführen der App in Azure

Bei der Ausführung in der Cloud (z. B. in der Produktion) wird für eine App meist eine **systemseitig zugewiesene verwaltete Identität** (zuvor als „MSI“ bezeichnet) genutzt. Bei einer [verwalteten Identität](/azure/active-directory/managed-identities-azure-resources/overview) verwenden Sie den Namen der App, wenn Sie Rollen und Berechtigungen für Ressourcen zuweisen. Von Azure wird der zugrunde liegende Dienstprinzipal automatisch verwaltet und für die App automatisch die Authentifizierung gegenüber den anderen Azure-Ressourcen durchgeführt. Daher müssen Sie den Dienstprinzipal nicht direkt verarbeiten. Darüber hinaus müssen in Ihrem App-Code auch keine Zugriffstoken, Geheimnisse oder Verbindungszeichenfolgen für Azure-Ressourcen verarbeitet werden. Dies mindert das Risiko, dass Informationen dieser Art unter Umständen in falsche Hände geraten oder anderweitig kompromittiert werden.

Die Konfiguration der verwalteten Identität richtet sich nach dem Dienst, den Sie zum Hosten Ihrer App verwenden. Der Artikel [Dienste, die verwaltete Identitäten unterstützen](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) enthält Links zu den Anleitungen für die verschiedenen Dienste. Beispielsweise aktivieren Sie für Web-Apps, die für Azure App Service bereitgestellt werden, die verwaltete Identität im Azure-Portal über die Option **Identität** > **Vom System zugewiesen** oder in der Azure CLI mit dem Befehl `az webapp identity assign`.

Wenn Sie keine verwaltete Identität verwenden können, müssen Sie die Anwendung stattdessen manuell bei Azure Active Directory registrieren. Bei der Registrierung wird der App ein Dienstprinzipal zugewiesen, den Sie beim Zuweisen von Rollen und Berechtigungen verwenden. Weitere Informationen finden Sie unter [Registrieren einer Client-App](/azure/active-directory/develop/quickstart-register-app).

### <a name="identity-when-running-the-app-locally"></a>Identität beim lokalen Ausführen der App

Während der Entwicklung kann es häufig vorkommen, dass Sie Ihren App-Code auf einer Entwicklerarbeitsstation ausführen und debuggen möchten, während mit diesem Code weiterhin auf Azure-Ressourcen in der Cloud zugegriffen werden soll. In diesem Fall erstellen Sie über Azure Active Directory einen separaten Dienstprinzipal, der speziell für die lokale Entwicklung bestimmt ist. Diesem Dienstprinzipal weisen Sie für die betreffenden Ressourcen erneut Rollen und Berechtigungen zu. Normalerweise autorisieren Sie diese Entwicklungsidentität so, dass nur auf Ressourcen zugegriffen wird, die nicht für die Produktion genutzt werden.

Ausführliche Informationen zur Erstellung des lokalen Dienstprinzipals und seiner Verfügbarmachung für die Azure-Bibliotheken finden Sie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md). Nach Abschluss dieser einmaligen Konfiguration können Sie denselben App-Code lokal und in der Cloud ausführen, ohne dass Sie umgebungsspezifische Änderungen vornehmen müssen.

Jeder Entwickler sollte über seinen eigenen Dienstprinzipal verfügen, der innerhalb seines Benutzerkontos auf der Arbeitsstation gesichert ist und niemals in einem Quellcodeverwaltungsrepository gespeichert wird. Wenn ein beliebiger Dienstprinzipal gestohlen oder kompromittiert wird, können Sie ihn problemlos löschen, um alle Berechtigungen aufzuheben, und den Dienstprinzipal dann für diesen Entwickler erneut erstellen. Weitere Informationen finden Sie unter [Verwalten von Dienstprinzipalen](how-to-manage-service-principals.md).

> [!NOTE]
> Sie können eine App zwar mit Ihren eigenen Benutzeranmeldeinformationen für Azure ausführen, aber mit dieser Vorgehensweise nicht die spezifischen Ressourcenberechtigungen einrichten, die Ihre App bei der Bereitstellung in der Cloud benötigt. Es ist viel besser, einen Dienstprinzipal für die Entwicklung einzurichten und ihm die benötigten Rollen und Berechtigungen zuzuweisen. Diese können Sie dann replizieren, indem Sie die verwaltete Identität oder den Dienstprinzipal der bereitgestellten App verwenden.

## <a name="assign-roles-and-permissions-to-an-identity"></a>Zuweisen von Rollen und Berechtigungen zu einer Identität

Nachdem Ihnen die Identitäten für die App sowohl in Azure als auch für die lokale Ausführung bekannt sind, nutzen Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC), um Berechtigungen über das Azure-Portal oder die Azure CLI zu gewähren. Alle Details finden Sie unter [Informationen zum Zuweisen von Rollenberechtigungen zu einer App-Identität oder einem Dienstprinzipal](/azure/role-based-access-control/role-assignments-steps).

## <a name="when-does-authentication-and-authorization-occur"></a>Wann wird die Authentifizierung und Autorisierung durchgeführt?

Beim Schreiben von App-Code mit den Azure-Bibliotheken (SDK) für Python wird für den Zugriff auf Azure-Ressourcen das folgende Muster verwendet:

1. Beschaffen von Anmeldeinformationen zum Beschreiben der App-Identität, indem eine der später in diesem Artikel beschriebenen Methoden verwendet wird.

1. Verwenden der Anmeldeinformationen zum Abrufen eines Clientobjekts für die betreffende Ressource. (Jeder Ressourcentyp verfügt in den Azure-Bibliotheken über ein eigenes Clientobjekt, für das Sie die URL der Ressource bereitstellen.)

1. Versuchen, auf die Ressource über das Clientobjekt zuzugreifen oder diese zu ändern, wodurch eine HTTP-Anforderung an die REST-API der Ressource generiert wird. Der API-Aufruf ist der Punkt, an dem von Azure dann sowohl die App-Identität authentifiziert als auch die Autorisierung überprüft wird.

Im folgenden Code werden diese Schritte beschrieben und veranschaulicht, indem versucht wird, auf Azure Key Vault zuzugreifen.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL. In this code we assume the resource URL is in an
# environment variable, KEY_VAULT_URL in this case.

vault_url = os.environ["KEY_VAULT_URL"]


# Acquire a credential object for the app identity. When running in the cloud,
# DefaultAzureCredential uses the app's managed identity (MSI) or user-assigned service principal.
# When run locally, DefaultAzureCredential relies on environment variables named
# AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.

credential = DefaultAzureCredential()


# Acquire an appropriate client object for the resource identified by the URL. The
# client object only stores the given credential at this point but does not attempt
# to authenticate it.
#
# **NOTE**: SecretClient here is only an example; the same process applies to all
# other Azure client libraries.

secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation on the resource using the client object (in
# this case, retrieve a secret from Key Vault). The operation fails for any of
# the following reasons:
#
# 1. The information in the credential object is invalid (for example, the AZURE_CLIENT_ID
#    environment variable cannot be found).
# 2. The app identity cannot be authenticated using the information in the credential object.
# 3. The app identity is not authorized to perform the requested operation on the
#    resource (identified in this case by the vault_url.

retrieved_secret = secret_client.get_secret("secret-name-01")
```

Auch hier wird keine Authentifizierung oder Autorisierung durchgeführt, bis von Ihrem Code über ein Clientobjekt eine spezifische Anforderung an die Azure-REST-API gesendet wird. Mit der Anweisung zum Erstellen von `DefaultAzureCredential` (siehe nächster Abschnitt) wird nur ein clientseitiges Objekt im Arbeitsspeicher erstellt, aber es werden keine weiteren Überprüfungen durchgeführt.

Für die Erstellung des [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient)-Objekts des SDK ist auch keine Kommunikation mit der betreffenden Ressource erforderlich. Das `SecretClient`-Objekt ist nur ein Wrapper für die zugrunde liegende Azure-REST-API und nur im Laufzeitarbeitsspeicher der App vorhanden. 

Erst wenn im Code die [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient#get-secret-name--version-none----kwargs-)-Methode aufgerufen wird, generiert das Clientobjekt den entsprechenden REST-API-Aufruf an Azure. Der Endpunkt von Azure für `get_secret` authentifiziert dann die Identität des Aufrufers und überprüft die Autorisierung.

## <a name="authenticate-with-defaultazurecredential"></a>Authentifizieren mit DefaultAzureCredential

Für die meisten Anwendungen ist die [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential)-Klasse aus der [`azure-identity`](/python/api/azure-identity/azure.identity)-Bibliothek die einfachste und empfohlene Authentifizierungsmethode.

`DefaultAzureCredential` verwendet automatisch die verwaltete Identität der App (MSI) in der Cloud und lädt bei der lokalen Ausführung automatisch einen lokalen Dienstprinzipal aus Umgebungsvariablen (wie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md#configure-authentication) beschrieben).

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL
vault_url = os.environ["KEY_VAULT_URL"]

# Acquire a credential object
credential = DefaultAzureCredential()

# Acquire a client object
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation
retrieved_secret = secret_client.get_secret("secret-name-01")
```

Im obigen Code wird das Objekt `DefaultAzureCredential` beim Zugreifen auf Azure Key Vault verwendet. Hierbei ist die URL des Schlüsseltresors in einer Umgebungsvariablen mit dem Namen `KEY_VAULT_URL` verfügbar. Vom Code wird das typische Nutzungsmuster für Bibliotheken implementiert: Objekt mit Anmeldeinformationen beschaffen, passendes Clientobjekt für die Azure-Ressource erstellen und dann versuchen, mit diesem Clientobjekt einen Vorgang auf dieser Ressource durchzuführen. Erneut erfolgt die Authentifizierung und Autorisierung erst in diesem letzten Schritt.

Wenn Code in Azure bereitgestellt und ausgeführt wird, verwendet `DefaultAzureCredential` automatisch die systemseitig zugewiesene verwaltete Identität (MSI), die Sie für die App im jeweils als Host fungierenden Dienst aktivieren können. Berechtigungen für bestimmte Ressourcen, z. B. Azure Storage oder Azure Key Vault, werden dieser Identität mit dem Azure-Portal oder der Azure CLI zugewiesen. In diesen Fällen maximiert diese von Azure verwaltete Identität die Sicherheit, da Sie in Ihrem Code niemals explizit mit Dienstprinzipalen interagieren.

Wenn Sie den Code lokal ausführen, verwendet `DefaultAzureCredential` automatisch den Dienstprinzipal, der von den Umgebungsvariablen `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` und `AZURE_CLIENT_SECRET` beschrieben wird. Das Clientobjekt schließt diese Werte dann (sicher) in den HTTP-Anforderungsheader ein, wenn der API-Endpunkt aufgerufen wird. Bei Ausführung in der lokalen Umgebung oder in der Cloud sind keine Codeänderungen erforderlich. Ausführliche Informationen zum Erstellen des Dienstprinzipals und zum Einrichten der Umgebungsvariablen finden Sie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure: Konfigurieren der Authentifizierung](configure-local-development-environment.md#configure-authentication).

In beiden Fällen müssen der verwendeten Identität Berechtigungen für die jeweilige Ressource zugewiesen werden. Der allgemeine Prozess ist unter [Zuweisen von Rollenberechtigungen](/azure/role-based-access-control/role-assignments-steps) beschrieben. Ausführlichere Informationen finden Sie in der Dokumentation zu den einzelnen Diensten. Ausführliche Informationen zu Key Vault-Berechtigungen (wie für den vorherigen Code benötigt) finden Sie beispielsweise unter [Bereitstellen der Key Vault-Authentifizierung mit einer Zugriffssteuerungsrichtlinie](/azure/key-vault/general/group-permissions-for-apps).

### <a name="using-defaultazurecredential-with-sdk-management-libraries"></a>Verwenden von DefaultAzureCredential mit SDK-Verwaltungsbibliotheken

`DefaultAzureCredential` kann mit neueren Versionen der Azure SDK-Verwaltungsbibliotheken (Ressourcenverwaltungsbibliotheken mit „mgmt“ im Namen) verwendet werden, die auch in der Liste [Bibliotheken mit azure.core](azure-sdk-library-package-index.md#libraries-using-azurecore) enthalten sind. (Außerdem enthält die pypi-Seite für eine aktualisierte Bibliothek üblicherweise die Zeile „Credential system has been completely revamped“ (Das System für Anmeldeinformationen wurde vollständig überarbeitet.), um auf die Änderung hinzuweisen.)

Beispielsweise können Sie `DefaultAzureCredential` mit Version 15.0.0 oder höher von „azure-mgmt-resource“ verwenden:

```python
from azure.identity import DefaultAzureCredential
from azure.mgmt.resource import SubscriptionClient

credential = DefaultAzureCredential()
subscription_client = SubscriptionClient(credential)

sub_list = subscription_client.subscriptions.list()
print(list(sub_list))
```

Wenn die Bibliothek nicht aktualisiert wurde, wird für Code mit `DefaultAzureCredential` ein Fehler mit dem Hinweis zurückgegeben, dass das Objekt über kein Attribut vom Typ „signed-session“ verfügt, wie im nächsten Abschnitt beschrieben.

### <a name="defaultazurecredential-object-has-no-attribute-signed-session"></a>„Das DefaultAzureCredential-Objekt hat kein Attribut 'signed-session'.“

Wenn Sie versuchen, `DefaultAzureCredential` mit einer Bibliothek zu verwenden, die nicht für die Verwendung von azure.core aktualisiert wurde, tritt bei Aufrufen durch ein Clientobjekt der eher vage folgende Fehler auf: „Das DefaultAzureCredential-Objekt hat kein Attribut 'signed_session'.“ Ein solcher Fehler tritt beispielsweise auf, wenn Sie den Code im vorherigen Abschnitt mit einer azure-mgmt-resource-Bibliothek mit einer niedrigeren Version als Version 15 verwenden.

Dieser Fehler tritt auf, weil azure.core-fremde Versionen von SDK-Verwaltungsbibliotheken davon ausgehen, dass das Anmeldeinformationsobjekt eine `signed_session`-Eigenschaft enthält, die `DefaultAzureCredential` fehlt.

Wenn die Verwaltungsbibliothek, die Sie verwenden möchten, noch nicht aktualisiert wurde, können Sie die folgenden alternativen Methoden verwenden:

1. Verwenden Sie eine der anderen Authentifizierungsmethoden, die in den nachfolgenden Abschnitten dieses Artikels beschrieben werden, die für Code geeignet ist, der *nur* SDK-Verwaltungsbibliotheken verwendet und nicht in der Cloud bereitgestellt wird. in diesem Fall können Sie nur auf lokale Dienstprinzipale zurückgreifen.

1. Verwenden Sie anstelle von `DefaultAzureCredential` die [CredentialWrapper-Klasse („cred_wrapper.py“)](https://gist.github.com/lmazuel/cc683d82ea1d7b40208de7c9fc8de59d), die von einem Mitglied des Azure SDK-Entwicklungsteams bereitgestellt wird. Wenn die gewünschte Verwaltungsbibliothek verfügbar ist, wechseln Sie zurück zu `DefaultAzureCredential`. Diese Methode hat den Vorteil, dass Sie die gleichen Anmeldeinformationen sowohl mit dem SDK-Client als auch mit den Verwaltungsbibliotheken verwenden können, und sie funktioniert sowohl lokal als auch in der Cloud.

    Wenn Sie eine Kopie von *cred_wrapper. py* in Ihren Projektordner heruntergeladen haben, sieht der vorherige Code wie folgt aus:

    ```python
    from cred_wrapper import CredentialWrapper
    from azure.mgmt.resource import SubscriptionClient

    credential = CredentialWrapper()
    subscription_client = SubscriptionClient(credential)
    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

    Wenn aktualisierte Verwaltungsbibliotheken verfügbar sind, können Sie `DefaultAzureCredential` wieder direkt verwenden, wie im ursprünglichen Codebeispiel gezeigt.

## <a name="other-authentication-methods"></a>Andere Authentifizierungsmethoden

Obwohl `DefaultAzureCredential` in den meisten Szenarien die empfohlene Authentifizierungsmethode ist, sind andere Methoden mit den folgenden Einschränkungen verfügbar:

- Die meisten Methoden funktionieren mit expliziten Dienstprinzipalen und nutzen die verwaltete Identität nicht für Code, der in der Cloud bereitgestellt wird. Wenn sie mit Produktionscode verwendet werden, müssen Sie unterschiedliche Dienstprinzipale für Ihre Cloudanwendungen verwalten und warten.

- Einige Methoden (z. B. CLI-basierte Authentifizierung) funktionieren nur mit lokalen Skripts und können nicht mit Produktionscode verwendet werden. Die CLI-basierte Authentifizierung ist praktisch für Entwicklungsarbeiten, da dabei die Berechtigungen der Azure-Anmeldung verwendet werden und keine expliziten Rollenzuweisungen erforderlich sind.

Dienstprinzipale für Anwendungen, die in der Cloud bereitgestellt werden, werden in Ihrem Abonnement-Active Directory verwaltet. Weitere Informationen finden Sie unter [Verwalten von Dienstprinzipalen](how-to-manage-service-principals.md).

In allen Fällen muss der entsprechende Dienstprinzipal bzw. Benutzer über die richtigen Berechtigungen für die jeweiligen Ressourcen und den Vorgang verfügen.

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
    > Wie unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development) erläutert wird, können Sie den Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) mit dem `--sdk-auth`-Parameter verwenden, um dieses JSON-Format direkt zu generieren.

1. Speichern Sie die Datei unter einem Namen wie *credentials.json* an einem sicheren Speicherort, auf den Ihr Code zugreifen kann. Um Ihre Anmeldeinformationen zu schützen, müssen Sie diese Datei aus der Quellcodeverwaltung ausschließen und dürfen sie nicht für andere Entwickler freigeben. Das heißt, die Mandanten-ID, die Client-ID und das Clientgeheimnis eines Dienstprinzipals sollten immer auf Ihrer Entwicklungsarbeitsstation isoliert bleiben.

1. Erstellen Sie eine Umgebungsvariable namens `AZURE_AUTH_LOCATION` mit dem Pfad zur JSON-Datei als Wert:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_AUTH_LOCATION=../credentials.json
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_AUTH_LOCATION="../credentials.json"
    ```

    ---

    In diesen Beispielen wird davon ausgegangen, dass die JSON-Datei den Namen *credentials.json* trägt und sich im übergeordneten Ordner Ihres Projekts befindet.

1. Verwenden Sie die Methode [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory#get-client-from-auth-file-client-class--auth-path-none----kwargs-), um das Clientobjekt zu erstellen:

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

Statt wie im vorherigen Abschnitt beschrieben eine Datei zu verwenden, können Sie die erforderlichen JSON-Daten in einer Variablen erstellen und [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory#get-client-from-json-dict-client-class--config-dict----kwargs-) aufrufen. In diesem Code wird davon ausgegangen, dass Sie die Umgebungsvariablen erstellt haben, die unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development) beschrieben werden. Für Code, der in der Cloud bereitgestellt wird, können Sie diese Umgebungsvariablen auf Ihrer Server-VM oder als Anwendungseinstellungen erstellen, wenn Sie einen Plattformdienst wie Azure App Service und Azure Functions verwenden.

Sie können Werte auch in Azure Key Vault speichern und zur Laufzeit abrufen, anstatt Umgebungsvariablen zu verwenden.

### <a name="authenticate-with-token-credentials"></a>Authentifizieren mit Zugriffstoken-Anmeldeinformationen

Sie können sich bei den Azure-Bibliotheken authentifizieren und dabei explizite Abonnement-, Mandanten- und Clientbezeichner sowie einen geheimen Clientschlüssel verwenden.

Bei Verwendung neuerer SDK-Bibliotheken, die auf „azure.core“ basieren, muss das [Objekt `ClientSecretCredential` aus der Bibliothek „azure.identity“](#using-clientsecretcredential-azureidentity) verwendet werden. Bei Verwendung älterer SDK-Bibliotheken muss [`ServicePrincipalCredentials` aus der Bibliothek „azure.common“](#using-serviceprincipalcredentials-azurecommon) verwendet werden.

Wenn Sie vorhandenen Code, in dem `ServicePrincipalCredentials` verwendet wird, zu einer neueren Bibliotheksversion migrieren möchten, ersetzen Sie Vorkommen der Verwendung dieser Klasse durch `ClientSecretCredential`, wie in den folgenden Abschnitten gezeigt. Beachten Sie die geringfügigen Änderungen bei den Parameternamen zwischen den beiden Konstruktoren: `tenant` wird zu `tenant_id`, und `secret` wird zu `client_secret`.

#### <a name="using-clientsecretcredential-azureidentity"></a>Verwenden von „ClientSecretCredential“ (azure.identity)

```python
import os
from azure.mgmt.resource import SubscriptionClient
from azure.identity import ClientSecretCredential

# Retrieve the IDs and secret to use with ClientSecretCredential
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Bei dieser Methode, die mit neueren, auf „azure.core“ basierenden Bibliotheken verwendet wird, erstellen Sie ein Objekt vom Typ [`ClientSecretCredential`](/python/api/azure-identity/azure.identity.clientsecretcredential) unter Verwendung von Anmeldeinformationen, die aus sicherem Speicher wie Azure Key Vault oder Umgebungsvariablen abgerufen werden. Im vorherigen Code wird davon ausgegangen, dass Sie die Umgebungsvariablen erstellt haben, die unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development) beschrieben werden.

#### <a name="using-serviceprincipalcredentials-azurecommon"></a>Verwenden von „ServicePrincipalCredentials“ (azure.common)

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

Bei dieser Methode, die mit älteren, nicht auf „azure.core“ basierenden Bibliotheken verwendet wird, erstellen Sie ein Objekt vom Typ [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials) unter Verwendung von Anmeldeinformationen, die aus sicherem Speicher wie Azure Key Vault oder Umgebungsvariablen abgerufen werden. Im vorherigen Code wird davon ausgegangen, dass Sie die Umgebungsvariablen erstellt haben, die unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development) beschrieben werden.

#### <a name="use-an-azure-sovereign-national-cloud"></a>Verwenden einer Sovereign Cloud oder nationalen Cloud von Azure

Mit diesen Methoden für Tokenanmeldeinformationen können Sie anstelle der öffentlichen Azure-Cloud eine [Sovereign Cloud oder nationale Cloud von Azure](/azure/active-directory/develop/authentication-national-cloud) verwenden, indem Sie für das Clientobjekt ein Argument vom Typ `base_url` angeben:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

Sovereign Cloud-Konstanten finden Sie in der [msrestazure.azure_cloud-Bibliothek](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

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

Führen Sie `pip install adal` aus, wenn Sie die ADAL-Bibliothek benötigen.

Mit dieser Methode können Sie eine [Azure-unabhängige oder nationale Cloud](/azure/active-directory/develop/authentication-national-cloud) anstelle der öffentlichen Azure-Cloud verwenden.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

# ...

LOGIN_ENDPOINT = AZURE_CHINA_CLOUD.endpoints.active_directory
RESOURCE = AZURE_CHINA_CLOUD.endpoints.active_directory_resource_id
```

Ersetzen Sie `AZURE_PUBLIC_CLOUD` einfach durch die entsprechende Sovereign Cloud-Konstante aus der [msrestazure.azure_cloud-Bibliothek](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="cli-based-authentication-development-purposes-only"></a>CLI-basierte Authentifizierung (nur für Entwicklungszwecke)

Bei dieser Methode erstellen Sie ein Clientobjekt mit den Anmeldeinformationen des Benutzers, der mit dem Azure CLI-Befehl `az login` angemeldet ist. Die CLI-basierte Authentifizierung funktioniert nur bei der Entwicklung, da sie nicht in Produktionsumgebungen verwendet werden kann.

Die Azure-Bibliotheken verwenden die Standardabonnement-ID. Sie können das Abonnement aber auch vor dem Ausführen des Codes festlegen, indem Sie Folgendes verwenden: [`az account`](/cli/azure/manage-azure-subscriptions-azure-cli).

Bei der Verwendung der CLI-basierten Authentifizierung ist die Anwendung für alle Vorgänge autorisiert, die von den CLI-Anmeldeinformationen zugelassen werden. Wenn Sie Besitzer oder Administrator Ihres Abonnements sind, besitzt Ihr Code daher inhärenten Zugriff auf die meisten Ressourcen in diesem Abonnement, ohne dass Sie bestimmte Berechtigungen zuweisen müssen. Dieses Verhalten ist für Experimente praktisch. Es wird jedoch dringend empfohlen, dass Sie bestimmte Dienstprinzipale verwenden und bestimmte Berechtigungen zuweisen, wenn Sie beginnen, Produktionscode zu schreiben, da Sie dann lernen, wie Sie den unterschiedlichen Identitäten genaue Berechtigungen zuweisen und diese Berechtigungen in Testumgebungen vor der Bereitstellung in der Produktion genau überprüfen können.

#### <a name="cli-based-authentication-with-azurecore-libraries"></a>CLI-basierte Authentifizierung mit azure.core-Bibliotheken

Bei Verwendung der [Azure-Bibliotheken, die für azure.core aktualisiert werden](./azure-sdk-library-package-index.md#libraries-using-azurecore), verwenden Sie das Objekt [`AzureCliCredential`](/python/api/azure-identity/azure.identity.azureclicredential) aus der Bibliothek „azure-identity“ (mindestens Version 1.4.0). Der folgende Code kann beispielsweise mit Version 15.0.0 und höheren Versionen von „azure-mgmt-resource“ verwendet werden:

```python
from azure.identity import AzureCliCredential
from azure.mgmt.resource import SubscriptionClient

credential = AzureCliCredential()
subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

#### <a name="cli-based-authentication-with-older-non-azurecore-libraries"></a>CLI-basierte Authentifizierung mit älteren Bibliotheken (nicht azure.core)

Wenn Sie ältere Azure-Bibliotheken verwenden, die nicht für azure.core aktualisiert wurden, können Sie die Methode [`get_client_from_cli_profile`](/python/api/azure-common/azure.common.client_factory#get-client-from-cli-profile-client-class----kwargs-) aus der Bibliothek „azure-cli-core“ verwenden. Der folgende Code kann beispielsweise mit niedrigeren Versionen als Version 15.0.0 von „azure-mgmt-resource“ verwendet werden:

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import SubscriptionClient

subscription_client = get_client_from_cli_profile(SubscriptionClient)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Falls Sie in demselben Skript auf unterschiedliche Abonnements verweisen müssen, sollten Sie die Methode ['get_client_from_auth_file'](#authenticate-with-a-json-file) oder [`get_client_from_json_dict`](#authenticate-with-a-json-dictionary) verwenden, die weiter oben in diesem Artikel beschrieben wurden.

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Veraltet: Authentifizierung mit UserPassCredentials

Bevor die [Azure Active Directory-Authentifizierungsbibliothek (ADAL) für Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) verfügbar war, musste die jetzt veraltete Klasse [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials) verwendet werden. Diese Klasse unterstützt keine zweistufige Authentifizierung und sollte nicht mehr verwendet werden.

## <a name="see-also"></a>Weitere Informationen

- [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md)
- [Zuweisen von Rollenberechtigungen](/azure/role-based-access-control/role-assignments-steps)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
- [Verwenden verwalteter Azure-Datenträger mit den Azure-Bibliotheken (SDK) für Python](azure-sdk-samples-managed-disks.md)
- [Kurze Umfrage zum Azure SDK für Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)
