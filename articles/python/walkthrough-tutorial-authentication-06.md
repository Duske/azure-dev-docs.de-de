---
title: 'Exemplarische Vorgehensweise, Teil 6: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine Beschreibung des Startcodes der Haupt-App, der das DefaultAzureCredential-Objekt und die Clientobjekte festlegt, die vom API-Endpunkt benötigt werden.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 8098b4effbf4d1d6e79b2f859a5f7eba7e68922f
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764706"
---
# <a name="part-6-main-app-startup-code"></a>Teil 6: Startcode der Haupt-App

[Vorheriger Teil: Abhängigkeiten, Importanweisungen und Umgebungsvariablen](walkthrough-tutorial-authentication-05.md)

Der Startcode der App, der den `import`-Anweisungen folgt, initialisiert verschiedene Variablen, die in den Funktionen verwendet werden, die HTTP-Anforderungen verarbeiten.

Zuerst erstellen wir das Flask-App-Objekt und rufen die API-Endpunkt-URL eines Drittanbieters aus der Umgebungsvariablen ab:

```python
app = Flask(__name__)

number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]
```

Als Nächstes rufen wir das [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential
)-Objekt ab, bei dem es sich um die empfohlenen Anmeldeinformationen für die Authentifizierung bei Azure-Diensten handelt. Siehe [Authentifizieren von Python-Apps](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential).

```python
credential = DefaultAzureCredential()
```

Bei der lokalen Ausführung sucht `DefaultAzureCredential` nach den Umgebungsvariablen `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` und `AZURE_CLIENT_SECRET`, die Informationen für den lokalen Dienstprinzipal enthalten. Bei der Ausführung in der Cloud verwendet `DefaultAzureCredential` automatisch den für die App registrierten Dienstprinzipal, der in der Regel in der verwalteten Identität enthalten ist.

Als nächstes ruft der Code den Zugriffsschlüssel der Drittanbieter-API aus Azure Key Vault ab. Im Bereitstellungsskript wird die Key Vault-Instanz mit [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create)erstellt, und das Geheimnis wird mit [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) gespeichert.

Der Zugriff auf die Key Vault-Ressource selbst erfolgt über eine URL, die in der Umgebungsvariablen `KEY_VAULT_URL` geladen wird.

```python
key_vault_url = os.environ["KEY_VAULT_URL"]
```

Zum Herstellen einer Verbindung mit dem Schlüsseltresor muss ein geeignetes Clientobjekt erstellt werden. Da wir ein Geheimnis abrufen möchten, verwenden wir die [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient)-Klasse, welche die Key Vault-URL und das Anmeldeinformationsobjekts erfordert, das die Identität darstellt, unter der die App ausgeführt wird.

```python
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)
```

Beim Erstellen des `SecretClient`-Objekts werden die Anmeldeinformationen in keiner Weise authentifiziert. Die `SecretClient`-Klasse ist einfach ein clientseitiges Konstrukt, das die Ressourcen-URL und die Anmeldeinformationen intern verwaltet. Authentifizierung und Autorisierung erfolgen nur, wenn Sie einen Vorgang über den Client aufrufen, z. B. [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient#get-secret-name--version-none----kwargs-), der einen REST-API-Aufruf an die Azure-Ressource generiert.

```python
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The key we
# want for the third-party API is in the value property. 
access_key = vault_secret.value
```

Auch wenn die App-Identität zum Zugriff auf den Schlüsseltresor autorisiert ist, muss sie speziell für den Zugriff auf Geheimnisse autorisiert sein.  Andernfalls schlägt der `get_secret`-Aufrufe fehl. Aus diesem Grund ruft das Bereitstellungsskript mithilfe des Azure CLI Befehls [`az keyvault set-policy`](/cli/azure/keyvault#az-keyvault-set-policy) die Zugriffsrichtlinie „Abrufen von Geheimnissen“ für die App ab. Weitere Informationen finden Sie unter [Key Vault-Authentifizierung](/azure/key-vault/general/authentication) und [Gewähren des Zugriffs auf Key Vault für Ihre App](/azure/key-vault/general/managed-identity#grant-your-app-access-to-key-vault). Im letztgenannten Artikel wird gezeigt, wie Sie eine Zugriffsrichtlinie mithilfe des Azure-Portals festlegen. (Der Artikel wurde auch für die verwaltete Identität geschrieben, gilt aber gleichermaßen für einen in der Entwicklung verwendeten lokalen Dienstprinzipal.)

Zuletzt richtet der App-Code das Clientobjekt ein, über das wir Meldungen in eine Azure Storage-Warteschlange schreiben können. Die URL der Warteschlange befindet sich in der Umgebungsvariablen `STORAGE_QUEUE_URL`.

```python
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)
```

Wie bei Key Vault verwenden wir ein bestimmtes Clientobjekt aus den Azure-Bibliotheken – [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) – und dessen [`from_queue_url`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#from-queue-url-queue-url--credential-none----kwargs-)-Methode, um eine Verbindung mit der Ressource herzustellen, die sich unter der betreffenden URL befindet. Auch hier gilt: Beim Versuch, dieses Clientobjekt zu erstellen, wird überprüft, ob die durch die Anmeldeinformationen dargestellte App-Identität für den Zugriff auf die Warteschlange autorisiert ist. Wie bereits erwähnt, wurde diese Autorisierung durch Zuweisen der Rolle „Mitwirkender an Storage-Warteschlangendaten“ zur Haupt-App erteilt.

Vorausgesetzt, dass der gesamte Startcode erfolgreich ist, verfügt die App über alle internen Variablen zur Unterstützung ihres API-Endpunkts */api/v1/getcode*.

> [!div class="nextstepaction"]
> [Teil 7: API-Endpunkt der Haupt-App >>>](walkthrough-tutorial-authentication-07.md)
