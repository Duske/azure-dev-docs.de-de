---
title: Untersuchen der Python-Codedateien für Azure Functions in Visual Studio Code
description: 'Tutorialschritt 3: Grundlegendes zum Python-Vorlagencode, der von Azure Functions bereitgestellt wird'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: b1ed7f69656797822b17c11bcf0f2ece0caa09e2
ms.sourcegitcommit: 86aec15e2d25b5b706092feb4271fb958c29d5fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71707343"
---
# <a name="examine-the-code-files"></a>Untersuchen der Codedateien

[Vorheriger Schritt: Erstellen der Funktion](tutorial-vs-code-serverless-python-02.md)

Im neu erstellten Funktionsunterordner sind drei Dateien enthalten: *\_\_init\_\_* .py enthält den Funktionscode, *function.json* beschreibt die Funktion für Azure Functions, und *sample.dat* ist eine Beispieldatendatei. Sie können *sample.dat* löschen, wenn Sie möchten, da sie nur dazu dient, Ihnen zu zeigen, dass Sie dem Unterordner weitere Dateien hinzufügen können.

Wir betrachten zuerst *function.json* und dann den Code in *\_\_init\_\_* .py.

## <a name="functionjson"></a>function.json

Die Datei „function.json“ enthält die erforderlichen Konfigurationsinformationen für den Azure Functions-Endpunkt:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Die `scriptFile`-Eigenschaft identifiziert die Startdatei für den Code, und dieser Code muss eine Python-Funktion mit dem Namen `main` enthalten. Sie können Ihren Code in mehrere Dateien einbeziehen, sofern die hier angegebene Datei eine `main`-Funktion enthält.

Das `bindings`-Element enthält zwei Objekte, eines zum Beschreiben eingehender Anforderungen und das andere, um die HTTP-Antwort zu beschreiben. Bei eingehenden Anforderungen (`"direction": "in"`) antwortet die Funktion auf HTTP-GET- oder -POST-Anforderungen und erfordert keine Authentifizierung. Die Antwort (`"direction": "out"`) ist eine HTTP-Antwort, die jeden Wert zurückgibt, der von der `main`-Python-Funktion zurückgegeben wird.

## <a name="__initpy__"></a>\_\_init.py\_\_

Wenn Sie eine neue Funktion erstellen, stellt Azure Functions standardmäßigen Python-Code in *\_\_init\_\_* .py bereit:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

Die wichtigen Teile des Codes lauten wie folgt:

- Sie müssen `func` aus `azure.functions` importieren; das Importieren des Protokollierungsmoduls ist optional, wird jedoch empfohlen.
- Die erforderliche `main`-Python-Funktion empfängt ein `func.HttpRequest`-Objekt mit dem Namen `req` und gibt einen Wert vom Typ `func.HttpResponse` zurück. Weitere Informationen zu den Funktionen dieser Objekte finden Sie in den Referenzen zu [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) und [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python).
- Der Text von `main` verarbeitet dann die Anforderung und generiert eine Antwort. In diesem Fall sucht der Code in der URL nach einem `name`-Parameter. Wenn dies nicht gelingt, wird überprüft, ob der Anforderungstext JSON-Code enthält (mit `func.HttpRequest.get_json`) und ob der JSON-Code einen `name`-Wert enthält (mit der `get`-Methode des von `get_json` zurückgegebenen JSON-Objekts).
- Wenn ein Name gefunden wird, gibt der Code die Zeichenfolge „Hello“ mit angefügtem Namen zurück. Andernfalls wird eine Fehlermeldung zurückgegeben.

> [!div class="nextstepaction"]
> [Ich habe die Codedateien untersucht.](tutorial-vs-code-serverless-python-04.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=03-examine-code-files)
