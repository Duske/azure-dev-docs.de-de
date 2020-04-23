---
title: Azure SDK für Python
description: Übersicht über die Features und Funktionen des Azure SDK für Python, das Entwicklern zu mehr Produktivität beim Bereitstellen, Verwenden und Verwalten von Azure-Ressourcen verhilft.
ms.date: 03/17/2020
ms.topic: conceptual
ms.openlocfilehash: 3d24a512420610f37285a03fe6a39d81e97510ee
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82026113"
---
# <a name="azure-sdk-for-python"></a>Azure SDK für Python

Das Open-Source-basierte Azure SDK für Python vereinfacht die Bereitstellung, Verwendung und Verwaltung von Azure-Ressourcen über Python-Anwendungscode. Das SDK unterstützt Python 2.7 und Python 3.5.3 sowie höhere Versionen.

Das SDK besteht aus einzelnen Komponentenbibliotheken, die jeweils mithilfe von `pip install <library>` installiert werden. Eine ausführlichere Anleitung finden Sie unter [Installieren des Azure SDK für Python](azure-sdk-install.md). Die Namen der Bibliotheken finden Sie in der [Dokumentation zum Azure SDK für Python](https://azure.github.io/azure-sdk-for-python/).

In der exemplarischen Vorgehensweise unter [Erste Schritte bei der Cloudentwicklung mit den Azure-Bibliotheken für Python](azure-sdk-get-started.yml) können Sie sich außerdem mit den Bibliotheken vertraut machen.

> [!TIP]
> Informationen zu Änderungen am SDK finden Sie in den [SDK-Versionshinweisen](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="management-and-client-libraries"></a>Verwaltungs- und Clientbibliotheken

Das Azure SDK für Python enthält zwei Arten von Bibliotheken:

- *Verwaltungsbibliotheken* unterstützen Sie bei der Bereitstellung und Verwaltung über das [Azure-Portal](https://portal.azure.com) oder mithilfe von Befehlszeilentools wie [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli) und [Azure PowerShell](https://docs.microsoft.com/powershell/azure/). Die Verwaltungsbibliotheken werden in der Regel in Konfigurations- und Bereitstellungsskripts verwendet. (Die Azure-Befehlszeilenschnittstelle wurde ebenfalls mit den Python-Verwaltungsbibliotheken geschrieben.)

- *Clientbibliotheken* ermöglichen die Interaktion von Anwendungscode mit bereits bereitgestellten Diensten über natürliche Python-Ausdrücke. Im Hintergrund verwenden die Clientbibliotheken zwar die REST-APIs von Azure, dank des SDK müssen Sie sich jedoch nicht mit REST-Details auseinandersetzen.

Die folgenden Abschnitte enthalten ausführlichere Informationen und Beispiele zu den einzelnen Kategorien.

## <a name="manage-azure-resources-with-management-libraries"></a>Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken

Die Verwaltungsbibliotheken des Azure SDK für Python heißen jeweils `azure-mgmt-<service name>` und unterstützen Sie bei der Erstellung, Bereitstellung und Verwaltung von Azure-Ressourcen.

Ein Beispiel: Angenommen, Sie möchten eine SQL Server-Instanz erstellen. Installieren Sie zunächst die passende Verwaltungsbibliothek:

```bash
pip install azure-mgmt-sql
```

Importieren Sie die Bibliothek in Ihrem Python-Code:

```python
from azure.mgmt.sql import SqlManagementClient
```

Erstellen Sie als Nächstes das Verwaltungsclientobjekt unter Verwendung Ihrer Anmeldeinformationen (siehe [Authentifizieren mit den Azure-Verwaltungsbibliotheken für Python](azure-sdk-authenticate.md)) und Ihrer Azure-Abonnement-ID:

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Erstellen Sie abschließend unter Verwendung dieses Verwaltungsclientobjekts die Ressource, und verwenden Sie dabei geeignete Werte für Ressourcengruppenname, Servername, Standort und Administratoranmeldeinformationen:

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

Ausführliche Informationen zur Verwendung der Verwaltungsbibliotheken finden Sie im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) des SDK in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](https://docs.microsoft.com/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="connect-and-use-azure-services-with-client-libraries"></a>Verbinden und Verwenden von Azure-Diensten mit Clientbibliotheken

Die *Clientbibliotheken* des Azure SDK unterstützt Sie dabei, eine Verbindung mit vorhandenen Azure-Ressourcen herzustellen und sie in Ihren Apps zu verwenden – beispielsweise, um Dateien hochzuladen, auf Tabellendaten zuzugreifen oder die verschiedenen Dienste von Azure Cognitive Services zu nutzen. Das SDK ermöglicht die Nutzung dieser Ressourcen über vertraute Python-Programmierparadigmen anstatt über die generische REST-API des Diensts. (Dienste ohne REST-API werden nicht durch eine Clientbibliothek dargestellt.)

Angenommen, Sie haben eine Web-App in Azure App Service bereitgestellt und möchten das Hochladen einer Datei in ein Azure Storage-Konto ermöglichen. Als Erstes müssen Sie die passende Bibliothek installieren:

```bash
pip install azure-storage-blob
```

Importieren Sie dann die Bibliothek in Ihrem Code:

```python
from azure.storage.blob import BlobClient
```

Verwenden Sie abschließend die API der Bibliothek, um eine Verbindung herzustellen und die Daten hochzuladen. In diesem Beispiel sind die Verbindungszeichenfolge und der Containername bereits in Ihrem Speicherkonto angegeben. Der Blobname ist der Name, den Sie den hochgeladenen Daten zuweisen:

```python
blob = BlobClient.from_connection_string("my_connection_string", container_name="mycontainer", blob_name="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Ausführliche Informationen zur Verwendung der Verwaltungsbibliotheken finden Sie im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) des SDK in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](https://docs.microsoft.com/samples/browse/?languages=python&products=azure) zur Verfügung.

### <a name="the-azure-core-library"></a>Die Azure Core-Bibliothek

Die Clientbibliotheken des Azure SDK für Python werden momentan mit allgemeinen Cloudmustern wie Authentifizierungsprotokollen, Protokollierung, Ablaufverfolgung, Transportprotokollen, gepufferten Antworten und Wiederholungen aktualisiert. Diese gemeinsam genutzten Funktionen sind in der Bibliothek [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) enthalten. Die Bibliotheken, die aktuell mit der Core-Bibliothek verwendet werden können, sind auf der [Seite mit den neuesten Azure SDK-Releases](https://azure.github.io/azure-sdk/releases/latest/#python-packages) aufgeführt.

Ausführliche Informationen zur Azure Core-Bibliothek und ihren Richtlinien finden Sie unter [Python-Richtlinien: Einführung](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="get-help-and-give-feedback"></a>Hilfe und Feedback

- Sehen Sie sich die [Dokumentation des Azure SDK für Python](https://aka.ms/python-docs) an.
- Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python) Fragen an die Community.
- Dokumentieren Sie Probleme für das SDK auf [GitHub](https://github.com/Azure/azure-sdk-for-python/issues).
- Erwähnen Sie [@AzureSDK](https://twitter.com/AzureSdk/) auf Twitter.
