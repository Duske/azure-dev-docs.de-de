---
title: 'Schritt 3: Untersuchen der Python-Codedateien für serverlose Azure Functions-Instanzen in VS Code'
description: 'Tutorialschritt 3: Grundlegendes zum serverlosen Python-Vorlagencode, der von Azure Functions bereitgestellt wird'
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperf-fy21q2
ms.openlocfilehash: 8d202aacb8848f17e15641340d126c65999d1c09
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911310"
---
# <a name="3-examine-the-python-code-files-in-visual-studio-code"></a>3: Untersuchen der Python-Codedateien in Visual Studio Code

[Vorheriger Schritt: Erstellen der Funktion](tutorial-vs-code-serverless-python-02.md)

In der neu erstellten Funktion **HttpExample** sind drei Dateien enthalten: *\_\_init\_\_.py* enthält den Funktionscode, *function.json* beschreibt die Funktion für Azure Functions, und *sample.dat* ist eine Beispieldatendatei. Sie können *sample.dat* löschen, wenn Sie möchten, da sie nur dazu dient, Ihnen zu zeigen, dass Sie dem Unterordner weitere Dateien hinzufügen können.

## <a name="functionjson"></a>function.json

Die Datei *function.json* beschreibt die erforderlichen Konfigurationsinformationen für den Azure Functions-Endpunkt:

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

Ausführliche Informationen zur Datei *functions.json* finden Sie im [Azure Functions-Entwicklerhandbuch](/azure/azure-functions/functions-reference) und in der Referenz [Trigger und Bindungen in Azure Functions](/azure/azure-functions/functions-triggers-bindings?tabs=python).

## <a name="__init__py"></a>\_\_init\_\_.py

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
        return func.HttpResponse(f"Hello, {name}. This HTTP triggered function executed successfully.")
    else:
        return func.HttpResponse(
             "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.",
             status_code=200
        )
```

Die wichtigen Teile des Codes lauten wie folgt:

- Sie müssen das Modul `azure.functions` importieren. Das Importieren des Protokollierungsmoduls ist optional, wird jedoch empfohlen.
- Die erforderliche `main`-Python-Funktion empfängt ein `func.HttpRequest`-Objekt mit dem Namen `req` und gibt einen Wert vom Typ `func.HttpResponse` zurück. Weitere Informationen zu den Funktionen dieser Objekte finden Sie in den Referenzen zu [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest) und [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse).
- Der Text von `main` verarbeitet dann die Anforderung und generiert eine Antwort. In diesem Fall sucht der Code in der URL nach einem `name`-Parameter. Wenn dies nicht gelingt, wird überprüft, ob der Anforderungstext JSON-Code enthält (mit `func.HttpRequest.get_json`) und ob der JSON-Code einen `name`-Wert enthält (mit der `get`-Methode des von `get_json` zurückgegebenen JSON-Objekts).
- Wenn ein Name gefunden wird, gibt der Code die Zeichenfolge „Hello“ mit angefügtem Namen und einem Hinweis auf eine erfolgreiche Funktion zurück. Andernfalls gibt der Code eine generische Nachricht zurück.

> [!div class="nextstepaction"]
> [Ich habe die Codedateien untersucht: Fahren Sie mit Schritt 4 fort. >>>](tutorial-vs-code-serverless-python-04.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-ms-survey)
