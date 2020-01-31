---
title: Azure SDK für Python
description: Übersicht über die Features und Funktionen des Azure SDK für Python, die Entwickler bei der effizienteren Verwendung von Azure-Diensten unterstützen.
ms.date: 10/30/2019
ms.topic: conceptual
ms.openlocfilehash: 17ab0cea512bb47a448492422a789f50a283ca9e
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76872054"
---
# <a name="azure-sdk-for-python"></a>Azure SDK für Python

Das Azure SDK für Python vereinfacht die Verwendung und Verwaltung von Azure-Ressourcen über Python-Anwendungscode. Das SDK unterstützt Python 2.7 und Python 3.5.3 sowie höhere Versionen.

Um das SDK zu installieren, können Sie mithilfe von `pip install <library>` eine beliebige der zugehörigen Komponentenbibliotheken installieren. Die Namen der Bibliotheken für verschiedene Dienste finden Sie in der [Dokumentation zum Azure SDK für Python](https://azure.github.io/azure-sdk-for-python/).

Ausführlichere Informationen zum Installieren von Bibliotheken sowie zum Importieren der Bibliotheken in Projekte finden Sie unter [Installieren des Azure SDK für Python](python-sdk-azure-install.md). Unter [Erste Schritte bei der Cloudentwicklung mit den Azure-Bibliotheken für Python](python-sdk-azure-get-started.yml) erfahren Sie dann, wie Sie Ihre Authentifizierung einrichten und Beispielcode für Ihr eigenes Azure-Abonnement ausführen.

> [!TIP]
> Informationen zu Änderungen am SDK finden Sie in den [SDK-Versionshinweisen](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-and-use-azure-services"></a>Verbinden mit und Verwenden von Azure-Diensten

Eine Reihe von *Clientbibliotheken* im SDK unterstützt Sie dabei, eine Verbindung mit vorhandenen Azure-Ressourcen herzustellen und sie in Ihren Apps zu verwenden – beispielsweise, um Dateien hochzuladen, auf Tabellendaten zuzugreifen oder die verschiedenen Azure Cognitive Services zu nutzen. Das SDK ermöglicht die Nutzung dieser Ressourcen über vertraute Python-Programmierparadigmen anstatt über die generische REST-API des Diensts.

Ein Beispiel: Angenommen, Sie möchten ein Blob in ein zuvor bereitgestelltes Azure Storage-Konto hochladen. Als Erstes müssen Sie die passende Bibliothek installieren:

```bash
pip install azure-storage-blob
```

Importieren Sie dann die Bibliothek in Ihrem Code:

```python
from azure.storage.blob import BlobClient
```

Verwenden Sie abschließend die API der Bibliothek, um eine Verbindung herzustellen und die Daten hochzuladen. In diesem Beispiel sind die Verbindungszeichenfolge und der Containername bereits in Ihrem Speicherkonto angegeben. Der Blobname ist der Name, den Sie den hochgeladenen Daten zuweisen:

```python
blob = BlobClient.from_connection_string("my_connection_string", container="mycontainer", blob="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Ausführliche Informationen zur Verwendung der einzelnen Bibliotheken finden Sie in unserem [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) im Projektordner der jeweiligen Bibliothek in der Datei *README.md* oder *README.rst*. Sehen Sie sich außerdem die verfügbaren [Azure-Beispiele](https://docs.microsoft.com/samples/browse/?languages=python) an.

Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) zur Verfügung.

### <a name="the-azure-core-library"></a>Die Azure Core-Bibliothek

Wir arbeiten momentan an der Aktualisierung unserer Python-Clientbibliotheken, um die gemeinsame Nutzung grundlegender Funktionen wie Wiederholungen, Protokollierung, Transportprotokolle, Authentifizierungsprotokolle und Ähnliches zu implementieren. Diese gemeinsam genutzten Funktionen sind in der Bibliothek [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) enthalten. Ausführliche Informationen zu dieser Bibliothek und ihren Richtlinien finden Sie unter [Python-Richtlinien: Einführung](https://azure.github.io/azure-sdk/python_introduction.html).

Die Core-Bibliothek wird aktuell von folgenden Bibliotheken verwendet:

- `azure-storage-blob`
- `azure-storage-queue`
- `azure-storage-file-share`
- `azure-keyvault-keys`
- `azure-keyvault-secrets`
- `azure-identity`

## <a name="manage-azure-resources"></a>Verwalten von Azure-Ressourcen

Das Azure SDK für Python enthält auch zahlreiche Bibliotheken, die Sie bei der Erstellung, Bereitstellung und Verwaltung von Azure-Ressourcen unterstützen. Diese werden als *Verwaltungsbibliotheken* bezeichnet. Verwaltungsbibliotheken sind wie folgt benannt: `azure-mgmt-<service name>`. Mit den Verwaltungsbibliotheken können Sie Python-Code schreiben, um die gleichen Aufgaben auszuführen, die auch über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli) ausgeführt werden können.

Ein Beispiel: Angenommen, Sie möchten eine SQL Server-Instanz erstellen. Installieren Sie zunächst die passende Verwaltungsbibliothek:

```bash
pip install azure-mgmt-sql
```

Importieren Sie die Bibliothek in Ihrem Python-Code:

```python
from azure.mgmt.sql import SqlManagementClient

```

Erstellen Sie als Nächstes das Verwaltungsclientobjekt unter Verwendung Ihrer Anmeldeinformationen und Ihrer Azure-Abonnement-ID:

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Erstellen Sie abschließend unter Verwendung dieses Clientobjekts die Ressource, und verwenden Sie dabei geeignete Werte für Ressourcengruppenname, Servername, Standort und Administratoranmeldeinformationen:

```python
server = sql_client.servers.create_or_update(
    'myresourcegroup',
    'myservername',
    {
        'location': 'eastus',
        'version': '12.0', # Required for create
        'administrator_login': 'mysecretname', # Required for create
        'administrator_login_password': 'HusH_Sec4et' # Required for create
    }
)
```

Ausführliche Informationen zur Verwendung der einzelnen Verwaltungsbibliotheken finden Sie genau wie bei Clientbibliotheken in unserem [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) im Projektordner der jeweiligen Bibliothek in der Datei *README.md* oder *README.rst*.

Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) zur Verfügung. 

## <a name="get-help-and-give-feedback"></a>Hilfe und Feedback

- Sehen Sie sich die [Dokumentation des Azure SDK für Python](https://aka.ms/python-docs) an.
- Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python) Fragen an die Community.
- Dokumentieren Sie Probleme für das SDK auf [GitHub](https://github.com/Azure/azure-sdk-for-python/issues).
- Senden Sie uns eine Nachricht über Twitter: [@AzureSDK](https://twitter.com/AzureSdk/).
