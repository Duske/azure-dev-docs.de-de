---
title: Konfigurieren von Proxys bei der Verwendung von Azure-Bibliotheken
description: Verwenden Sie HTTP[S]_PROXY-Umgebungsvariablen, um einen Proxy für ein ganzes Skript oder eine App zu definieren, oder verwenden Sie optionale benannte Argumente für Clientkonstruktoren oder Vorgangsmethoden.
ms.date: 06/16/2020
ms.topic: conceptual
ms.openlocfilehash: c24ad2f5cd58e6cee9b9f61cb216e949ddd446ef
ms.sourcegitcommit: b3e506c6f140d91e6fdd9dcadf22ab1aa67f6978
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947505"
---
# <a name="how-to-configure-proxies-for-the-azure-libraries"></a>Konfigurieren von Proxys für die Azure-Bibliotheken

Eine Proxy Server-URL weist die Form `http[s]://[username:password@]<ip_address_or_domain>:<port>/` auf, in der die Kombination aus Benutzername und Kennwort („username:password“) optional ist.

Anschließend können Sie einen Proxy global konfigurieren, indem Sie Umgebungsvariablen verwenden, oder Sie können einen Proxy angeben, indem Sie ein Argument mit dem Namen `proxies` an einen einzelnen Clientkonstruktor oder eine Vorgangsmethode übergeben.

## <a name="global-configuration"></a>Globale Konfiguration

Um einen Proxy global für Ihr Skript oder Ihre App zu konfigurieren, definieren Sie `HTTP_PROXY`- oder `HTTPS_PROXY`-Umgebungsvariablen mit der Server-URL. Diese Variablen funktionieren in jeder Version der Azure-Bibliotheken.

Diese Umgebungsvariablen werden ignoriert, wenn Sie den Parameter `use_env_settings=False` an einen Clientobjektkonstruktor oder eine Vorgangsmethode übergeben.

### <a name="from-python-code"></a>Aus Python-Code

```python
import os
os.environ["HTTP_PROXY"] = "http://10.10.1.10:1180"

# Alternate URL and variable forms:
# os.environ["HTTP_PROXY"] = "http://username:password@10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://username:password@10.10.1.10:1180"
```

### <a name="from-the-cli"></a>Über die CLI

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
rem Non-authenticated HTTP server:
set HTTP_PROXY=http://10.10.1.10:1180

rem Authenticated HTTP server:
set HTTP_PROXY=http://username:password@10.10.1.10:1180

rem Non-authenticated HTTPS server:
set HTTPS_PROXY=http://10.10.1.10:1180

rem Authenticated HTTPS server:
set HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Non-authenticated HTTP server:
HTTP_PROXY=http://10.10.1.10:1180

# Authenticated HTTP server:
HTTP_PROXY=http://username:password@10.10.1.10:1180

# Non-authenticated HTTPS server:
HTTPS_PROXY=http://10.10.1.10:1180

# Authenticated HTTPS server:
HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

---

## <a name="per-client-or-per-method-configuration"></a>Konfiguration pro Client oder pro Methode

Um einen Proxy für ein bestimmtes Clientobjekt oder eine bestimmte Vorgangsmethode zu konfigurieren, geben Sie einen Proxyserver mit einem Argument mit dem Namen `proxies` an.

Der folgende Code aus dem Artikel [Beispiel: Verwenden von Azure-Speicher](azure-sdk-example-storage.md) gibt z. B. einen HTTPS-Proxy mit Benutzeranmeldeinformationen mit dem `BlobClient`-Konstruktor an. In diesem Fall stammt das Objekt aus der azure.storage.blob-Bibliothek, die auf azure.core basiert.

```python
# Earlier code omitted for brevity

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential,
    proxies={ "https": "https://username:password@10.10.1.10:1180" }
)

# Other forms that the proxy URL might take:
# proxies={ "http": "http://10.10.1.10:1180" }
# proxies={ "http": "http://username:password@10.10.1.10:1180" }
# proxies={ "https": "https://10.10.1.10:1180" }
```
