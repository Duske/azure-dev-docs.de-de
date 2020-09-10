---
title: 'Exemplarische Vorgehensweise, Teil 3: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine Beschreibung der Implementierung von Drittanbieter-APIs mithilfe von Azure Functions und der Art und Weise, wie der Endpunkt durch einen Zugriffsschlüssel geschützt wird.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 84078a455843cb28f80a633bb5344bc5ab645ac7
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379533"
---
# <a name="part-3-example-third-party-api-implementation"></a>Teil 3: Beispiel für die Implementierung einer Drittanbieter-API

[Vorheriger Teil: Herausforderungen der Authentifizierung](walkthrough-tutorial-authentication-02.md)

In unserem Beispielszenario verwendet der öffentliche Endpunkt der Haupt-App eine Drittanbieter-API, die durch einen Zugriffsschlüssel geschützt ist. In diesem Abschnitt wird eine Implementierung der Drittanbieter-API unter Verwendung von Azure Functions gezeigt, aber die API kann auch auf andere Weise implementiert und auf einem anderen Cloudserver oder Webhost bereitgestellt werden. Der einzige wichtige Aspekt ist, dass der Endpunkt durch einen bestimmten Zugriffsschlüssel geschützt ist, der in Clientanforderungen enthalten sein muss. Jede App, die diese API aufruft, muss diesen Schlüssel sicher verwalten.

Zu Demonstrationszwecken wird diese API für den Endpunkt bereitgestellt, `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`. Zum Aufrufen der API müssen Sie jedoch den Zugriffsschlüssel `d0c5atM1cr0s0ft` entweder in einem `?code=`-URL-Parameter oder in einer `'x-functions-key'`-Eigenschaft des HTTP-Headers bereitstellen. Testen Sie z. B. diese URL in einem Browser oder curl: [https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft](https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft).

Wenn der Zugriffsschlüssel gültig ist, gibt der Endpunkt eine JSON-Antwort zurück, die eine einzelne Eigenschaft – „value“ – enthält, deren Wert eine Zahl zwischen 1 und 999 ist, z. B. `{"value": 959}`.

Der Endpunkt wird in Python implementiert und für Azure Functions bereitgestellt. Der Code lautet wie folgt:

```python
import logging
import random
import json

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('RandomNumber invoked via HTTP trigger.')

    random_value = random.randint(1, 1000)
    dict = { "value" : random_value }
    return func.HttpResponse(json.dumps(dict))
```

Im Beispielrepository befindet sich dieser Code unter *third_party_api/RandomNumber/\_\_init\_\_.py*. Der Ordner *RandomNumber* enthält den Namen der Funktion, und *\_\_init\_\_.py* enthält den Code. Eine andere Datei in diesem Ordner, *function.json*, beschreibt, wann die Funktion ausgelöst wird. Weitere Dateien im übergeordneten Ordner *third_party_api* enthalten Details für die Azure Function-„App“, die die Funktion selbst hostet.

Zum Bereitstellen des Codes führt das Bereitstellungsskript des Beispiels die folgenden Schritte aus:

1. Erstellen eines unterstützenden Speicherkontos für Azure Functions mit dem Azure CLI-Befehl [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)

1. Erstellen einer Azure Functions-„App“ mit dem Azure CLI-Befehl [`az function app create`](/cli/azure/functionapp?view=azure-cli-latest#az-functionapp-create)

1. Nachdem 60 Sekunden auf die vollständige Bereitstellung des Codes gewartet wurde, wird der Code mit dem [Azure Functions Core Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash)-Befehl [`func azure functionapp publish`](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash#project-file-deployment) bereitgestellt.

1. Der Zugriffsschlüssel (`d0c5atM1cr0s0ft`) wird der Funktion zugewiesen. (Hintergrundinformationen zu Funktionsschlüsseln finden Sie unter [Absichern von Azure Functions](/azure/azure-functions/security-concepts).)

    Im Bereitstellungsskript wird dieser Schritt über einen Rest-API-Aufruf der [Functions Key Management-API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) ausgeführt, da Azure CLI diese spezielle Funktion derzeit nicht unterstützt. Zum Aufrufen dieser REST-API muss das Bereitstellungsskript zunächst mithilfe eines weiteren REST-API-Aufrufs den Hauptschlüssel der Functions-App abrufen.

    Das Zuweisen von Zugriffsschlüsseln über das [Azure-Portal](https://portal.azure.com) ist ebenfalls möglich. Wählen Sie auf der Seite für die Functions-App die Option **Functions** aus, und wählen Sie dann die bestimmte Funktion aus, die Sie sichern möchten (in diesem Beispiel `RandomNumber`). Wählen Sie auf der Seite der Funktion die Option **Funktionsschlüssel** aus, um die Seite zu öffnen, auf der Sie diese Schlüssel erstellen und verwalten können.

> [!div class="nextstepaction"]
> [Teil 4: Beispiel für die Implementierung einer Haupt-App >>>](walkthrough-tutorial-authentication-04.md)
