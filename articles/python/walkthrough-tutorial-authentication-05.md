---
title: 'Exemplarische Vorgehensweise, Teil 5: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine Erörterung der Abhängigkeiten der Haupt-App (hauptsächlich Azure SDK-Bibliotheken), der erforderlichen Importanweisungen und der Umgebungsvariablen, die für die Anwendung festgelegt werden müssen.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2da18ac4b1d27e976d0713fba16dbfc0ba644168
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379484"
---
# <a name="part-5-main-app-dependencies-import-statements-and-environment-variables"></a>Teil 5: Abhängigkeiten der Haupt-App, Importanweisungen und Umgebungsvariablen

[Vorheriger Teil: Beispiel für die Implementierung einer Haupt-App](walkthrough-tutorial-authentication-04.md)

Dieser Teil enthält Informationen zu den in die Haupt-App eingefügten Python-Bibliotheken und zu den für den Code erforderlichen Umgebungsvariablen. Bei der Bereitstellung in Azure verwenden Sie Anwendungseinstellungen in Azure App Service, um Umgebungsvariablen bereitzustellen.

## <a name="dependencies-and-import-statements"></a>Abhängigkeiten und Importanweisungen

Der App-Code erfordert eine Reihe von Bibliotheken: Flask, die Standardbibliothek für HTTP-Anforderungen, und die Azure-Bibliotheken für Active Directory ([azure.identity](/python/api/overview/azure/identity-readme?view=azure-python)), Key Vault ([azure.keyvault.secrets](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)) und Queue Storage ([azure.storage.queue](/python/api/overview/azure/storage-queue-readme?view=azure-python)). Diese Bibliotheken sind in der Datei *requirements.txt* der App enthalten:

```txt
flask
requests
azure.identity
azure.keyvault.secrets
azure.storage.queue
```

Wenn Sie die App in Azure App Service bereitstellen, installiert Azure diese Anforderungen automatisch auf dem Hostserver. Bei der lokalen Ausführung installieren Sie sie mit `pip install -r requirements.txt` in Ihrer Umgebung.

Am Anfang des Codes befinden sich dann die erforderlichen Importanweisungen für die Teile, die wir aus Bibliotheken verwenden:

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient
from azure.storage.queue import QueueClient
```

## <a name="environment-variables"></a>Umgebungsvariablen

Der App-Code hängt von vier Umgebungsvariablen ab:

| Variable | Wert |
| --- | --- |
| THIRD_PARTY_API_ENDPOINT | Die URL der Drittanbieter-API, z. B. `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`, die in [Teil 3](walkthrough-tutorial-authentication-03.md) beschrieben wird. |
| KEY_VAULT_URL | Die URL der Azure Key Vault-Instanz, in der Sie den Zugriffsschlüssel für die Drittanbieter-API gespeichert haben. |
| THIRD_PARTY_API_SECRET_NAME | Der Name des Geheimnisses in Key Vault, das den Zugriffsschlüssel für die Drittanbieter-API enthält. |
| STORAGE_QUEUE_URL | Die URL einer Azure Storage-Warteschlange, die in Azure konfiguriert wurde, z. B. `https://msdocsmainappexample.queue.core.windows.net/code-requests` (siehe [Teil 4](walkthrough-tutorial-authentication-04.md)). Da der Warteschlangenname am Ende der URL enthalten ist, wird der Name nicht im Code angezeigt. |

Bei der lokalen Ausführung erstellen Sie diese Variablen in der von Ihnen verwendeten Befehlsshell. Wenn Sie die App auf einem virtuellen Computer bereitstellen, würden Sie ähnliche serverseitige Variablen erstellen.

Beim Bereitstellen in Azure App Service haben Sie jedoch keinen Zugriff auf den Server selbst. In diesem Fall erstellen Sie *Anwendungseinstellungen* mit denselben Namen, die der App dann als Umgebungsvariablen angezeigt werden. 

Die Bereitstellungsskripts erstellen diese Einstellungen mit dem Azure CLI-Befehl [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set). Alle vier Variablen werden mit einem einzigen Befehl festgelegt.

Informationen zum Erstellen von Einstellungen über das Azure-Portal finden Sie unter [Konfigurieren einer App Service-App im Azure-Portal](/azure/app-service/configure-common).

> [!div class="nextstepaction"]
> [Teil 6. Startcode der Haupt-App >>>](walkthrough-tutorial-authentication-06.md)
