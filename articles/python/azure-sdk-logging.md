---
title: Konfigurieren von Protokollierung in den Azure-Bibliotheken für Python
description: In den Azure-Bibliotheken wird das Python-Standardprotokollierungsmodul verwendet, das pro Bibliothek oder pro Vorgang konfiguriert wird.
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: f42941ec54876fec5854a0a82cee1cbcf30506ce
ms.sourcegitcommit: b09d3aa79113af04a245b05cec2f810e43062152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99476446"
---
# <a name="configure-logging-in-the-azure-libraries-for-python"></a>Konfigurieren von Protokollierung in den Azure-Bibliotheken für Python

Azure-Bibliotheken für Python, die auf der Seite [azure.core](azure-sdk-library-package-index.md#libraries-using-azurecore) basieren, stellen die Protokollierungsausgabe mithilfe der standardmäßigen Python-[Protokollierungsbibliothek](https://docs.python.org/3/library/logging.html) bereit.

Der allgemeine Prozess zum Arbeiten mit der Protokollierung ist wie folgt:

1. Rufen Sie das Protokollierungsobjekt für die gewünschte Bibliothek ab, und legen Sie den Protokolliergrad fest.
1. Registrieren Sie einen Handler für den Protokollierungsdatenstrom.
1. Um HTTP-Informationen aufzunehmen, übergeben Sie einen `logging_enable=True`-Parameter an einen Clientobjektkonstruktor, einen Anmeldeinformations-Objektkonstruktor oder an eine bestimmte Methode übergeben.

Detaillierte Informationen finden Sie in den restlichen Abschnitten dieses Artikels.

Als allgemeine Regel gilt: Die beste Ressource zum Verständnis der Protokollierungsverwendung innerhalb der Bibliotheken ist das Durchsuchen des SDK-Quellcodes unter [github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Es wird empfohlen, dieses Repository lokal zu klonen, damit Sie bei Bedarf problemlos nach Details suchen können, wie in den folgenden Abschnitten vorgeschlagen.

## <a name="set-logging-levels"></a>Festlegen der Protokolliergrade

```python
import logging

# ...

# Acquire the logger for a library (azure.storage.blob in this example)
logger = logging.getLogger('azure.storage.blob')

# Set the desired logging level
logger.setLevel(logging.DEBUG)
```

- In diesem Beispiel wird die Protokollierung für die `azure.storage.blob`-Bibliothek abgerufen und dann der Protokolliergrad auf `logging.DEBUG`festgelegt.

- Sie können `logger.setLevel` jederzeit aufrufen, um den Protokolliergrad für verschiedene Codesegmente zu ändern.

Um einen Protokolliergrad für eine andere Bibliothek festzulegen, verwenden Sie den Namen der betreffenden Bibliothek im `logging.getLogger`-Aufruf. Die azure-eventhubs-Bibliothek stellt z. B. eine Protokollierung namens `azure.eventhubs` bereit, die azure-storage-queue-Bibliothek eine Protokollierung mit dem Namen `azure.storage.queue` usw. (Der SDK-Quellcode verwendet häufig die Anweisung `logging.getLogger(__name__)`, die eine Protokollierung mit dem Namen des enthaltenden Moduls abruft.)

Sie können auch allgemeinere Namespaces verwenden. Beispiel:

```python
import logging

# Set the logging level for all azure-storage-* libraries
logger = logging.getLogger('azure.storage')
logger.setLevel(logging.INFO)

# Set the logging level for all azure-* libraries
logger = logging.getLogger('azure')
logger.setLevel(logging.ERROR)
```

Sie können die `logger.isEnabledFor`-Methode verwenden, um zu überprüfen, ob ein bestimmter Protokolliergrad aktiviert ist:

```python
print(f"Logger enabled for ERROR={logger.isEnabledFor(logging.ERROR)}, " \
    f"WARNING={logger.isEnabledFor(logging.WARNING)}, " \
    f"INFO={logger.isEnabledFor(logging.INFO)}, " \
    f"DEBUG={logger.isEnabledFor(logging.DEBUG)}")
```

Protokolliergrade sind identisch mit den [Standardprotokolliergraden der Bibliothek](https://docs.python.org/3/library/logging.html#levels). In der folgenden Tabelle wird die allgemeine Verwendung dieser Protokolliergrade in den Azure-Bibliotheken für Python beschrieben:

| Protokolliergrad             | Typische Verwendung |
| ---                       | ---         |
| logging.ERROR             | Fehler, bei denen die Anwendung wahrscheinlich nicht wiederhergestellt werden kann (z. B. nicht genügend Arbeitsspeicher). |
| logging.WARNING (Standard) | Eine Funktion kann die beabsichtigte Aufgabe nicht ausführen (wird jedoch nicht protokolliert, wenn die Funktion wiederhergestellt werden kann, z. B. durch Wiederholen eines REST-API-Aufrufs). Funktionen protokollieren in der Regel eine Warnung, wenn Ausnahmen ausgelöst werden. Die Warnstufe aktiviert automatisch die Fehlerstufe. |
| logging.INFO              | Die Funktion funktioniert ordnungsgemäß, oder ein Dienstaufruf wird abgebrochen. Informationsereignisse enthalten in der Regel Anforderungen, Antworten und Header. Die Informationsstufe aktiviert automatisch die Fehler- und Warnstufen. |
| logging.DEBUG             | Ausführliche Informationen, die häufig für die Problembehandlung verwendet werden und eine Stapelüberwachung für Ausnahmen enthalten. Die Debugstufe aktiviert automatisch die Informations-, Warnungs- und Fehlerstufen. VORSICHT: Wenn Sie außerdem `logging_enable=True` festlegen, umfasst die Debugebene vertrauliche Informationen wie Kontoschlüssel in Headern und andere Anmeldeinformationen. Stellen Sie sicher, dass diese Protokolle geschützt sind, um eine Beeinträchtigung der Sicherheit zu vermeiden. |
| logging.NOTSET            | Deaktiviert die gesamte Protokollierung. |

### <a name="library-specific-logging-level-behavior"></a>Bibliotheksspezifisches Verhalten des Protokolliergrads

Das genaue Protokollierungsverhalten auf jeder Stufe hängt von der betreffenden Bibliothek ab. Einige Bibliotheken (z. B. azure.eventhub) führen umfangreiche Protokollierung durch, während andere Bibliotheken sehr wenig protokollieren.

Die beste Möglichkeit, die genaue Protokollierung für eine Bibliothek zu untersuchen, besteht darin, die Protokolliergrade im Azure SDK für Python-Quellcode zu suchen:

1. Navigieren Sie im Repositoryordner zum Ordner *sdk*, und navigieren Sie dann zu dem Ordner für den betreffenden Dienst.

1. Suchen Sie in diesem Ordner nach einer der folgenden Zeichenfolgen:

    - `_LOGGER.error`
    - `_LOGGER.warning`
    - `_LOGGER.info`
    - `_LOGGER.debug`

## <a name="register-a-log-stream-handler"></a>Registrieren eines Protokolldatenstrom-Handlers

Um die Protokollierungsausgabe zu erfassen, müssen Sie mindestens einen Protokolldatenstrom-Handler in Ihrem Code registrieren:

```python
import logging

# Direct logging output to stdout. Without adding a handler,
# no logging output is captured.
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)
```

In diesem Beispiel wird ein Handler registriert, der die Protokollausgabe an stdout weiterleitet. Sie können andere Arten von Handlern verwenden, wie unter [logging.handlers](https://docs.python.org/3/library/logging.handlers.html) in der Python-Dokumentation beschrieben, oder Sie verwenden die Standardmethode [logging.basicConfig](https://docs.python.org/3/library/logging.html#logging.basicConfig).

## <a name="enable-http-logging-for-a-client-object-or-operation"></a>Aktivieren der HTTP-Protokollierung für ein Clientobjekt oder einen Vorgang

Standardmäßig enthält die Protokollierung innerhalb der Azure-Bibliotheken keine HTTP-Informationen. Um HTTP-Informationen in die Protokollausgabe aufzunehmen (als DEBUG-Ebene), müssen Sie ganz spezifisch `logging_enable=True` an einen Client- oder Anmeldeinformations-Objektkonstruktor oder an eine bestimmte Methode übergeben.

**VORSICHT**: Die HTTP-Protokollierung kann vertrauliche Informationen wie Kontoschlüssel in Headern und andere Anmeldeinformationen enthalten und somit kompromittieren. Stellen Sie sicher, dass diese Protokolle geschützt sind, um eine Beeinträchtigung der Sicherheit zu vermeiden.

### <a name="enable-http-logging-for-a-client-object-debug-level"></a>Aktivieren der HTTP-Protokollierung für ein Clientobjekt (DEBUG-Ebene)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Enable HTTP logging on the client object when using DEBUG level
# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential(), logging_enable=True)
```

Durch die Aktivierung der HTTP-Protokollierung für ein Clientobjekt wird die Protokollierung aller Vorgänge, die über dieses Objekt aufgerufen werden, ermöglicht.

### <a name="enable-http-logging-for-a-credential-object-debug-level"></a>Aktivieren der HTTP-Protokollierung für ein Anmeldeinformationsobjekt (DEBUG-Ebene)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Enable HTTP logging on the credential object when using DEBUG level
credential = DefaultAzureCredential(logging_enable=True)

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, credential)
```

Durch die Aktivierung der HTTP-Protokollierung für ein Anmeldeinformationsobjekt wird die Protokollierung spezifisch für alle Vorgänge ermöglicht, die über dieses Objekt aufgerufen werden, ermöglicht, aber nicht für Vorgänge in einem Clientobjekt, die keine Authentifizierung beinhalten.

### <a name="enable-logging-for-an-individual-method-debug-level"></a>Aktivieren der Protokollierung für eine einzelne Methode (DEBUG-Ebene)

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential())

# Enable HTTP logging for only this operation when using DEBUG level
client.create_container("container01", logging_enable=True)
```

## <a name="example-logging-output"></a>Beispiel für eine Protokollierungsausgabe

Der folgende Code stammt aus [Beispiel: Verwenden eines Speicherkontos](azure-sdk-example-storage-use.md). Zusätzlich wurden DEBUG- und HTTP-Protokollierung (Kommentare werden aus Platzgründen ausgelassen) aktiviert:

```python
import os, sys, logging
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobClient

logger = logging.getLogger('azure.storage.blob')
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

credential = DefaultAzureCredential()
storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential)

with open("./sample-source.txt", "rb") as data:
    blob_client.upload_blob(data, logging_enable=True)
```

Die Protokollierungsausgabe sieht wie folgt aus:

<pre>
Request URL: 'https://pythonsdkstorage12345.blob.core.windows.net/blob-container-01/sample-blob.txt'
Request method: 'PUT'
Request headers:
    'Content-Type': 'application/octet-stream'
    'Content-Length': '79'
    'x-ms-version': '2019-07-07'
    'x-ms-blob-type': 'BlockBlob'
    'If-None-Match': '*'
    'x-ms-date': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'User-Agent': 'azsdk-python-storage-blob/12.3.1 Python/3.8.3 (Windows-10-10.0.18362-SP0)'
    'Authorization': '*****'
Request body:
b"Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.\r\n"
Response status: 201
Response headers:
    'Content-Length': '0'
    'Content-MD5': 'kvMIzjEi6O8EqTVnZJNakQ=='
    'Last-Modified': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'ETag': '"0x8D8067EB52FF7BC"'
    'Server': 'Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0'
    'x-ms-request-id': '5df479b1-f01e-00d0-5b67-382916000000'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'x-ms-version': '2019-07-07'
    'x-ms-content-crc64': 'QmecNePSHnY='
    'x-ms-request-server-encrypted': 'true'
    'Date': 'Mon, 01 Jun 2020 22:54:14 GMT'
Response content:
</pre>
