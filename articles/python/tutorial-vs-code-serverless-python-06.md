---
title: 'Schritt 6: Hinzufügen einer zweiten serverlosen Python-Funktion zu Azure Functions mit VS Code'
description: 'Tutorialschritt 6: Erweitern eines Azure Functions-Projekts durch Hinzufügen einer zweiten serverlosen Funktion'
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperf-fy21q2
ms.openlocfilehash: 41c97fecd590417d406b94124319348a03d6db7f
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911360"
---
# <a name="6-add-a-second-python-function-to-azure-functions"></a>6: Hinzufügen einer zweiten Python-Funktion zu Azure Functions

[Vorheriger Schritt: Bereitstellen in Azure](tutorial-vs-code-serverless-python-05.md)

Nach der ersten Bereitstellung können Sie Änderungen am Code vornehmen, z. B. zusätzliche Python-Funktionen hinzufügen und ihn dann erneut für die gleiche Azure Functions-App bereitstellen.

1. Wählen Sie im Bereich **Azure: Functions**-Explorer den Befehl **Funktion erstellen** aus, oder verwenden Sie **Azure Functions: Funktion erstellen** aus der Befehlspalette. Geben Sie die folgenden Details für die Funktion an:

    - Vorlage: HTTP-Trigger
    - Name: „DigitsOfPi“
    - Autorisierungsstufe: Anonym

    Der Abschnitt **Lokales Projekt** im Azure Functions-Explorer zeigt nun die Funktion „*DigitsOfPi*“ an. Im Editor können Sie zwischen den Dateien *\_\_init\_\_.py*, *function.json* und *sample.dat* der Funktion wechseln.

1. Ersetzen Sie den Inhalt von *\_\_init\_\_.py*, sodass er dem folgenden Code entspricht, der eine Zeichenfolge mit dem Wert von PI für eine Anzahl von Ziffern generiert, die in der URL angegeben sind (dieser Code verwendet nur einen URL-Parameter):

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Da der Code nur HTTP-GET unterstützt, ändern Sie *function.json* so, dass die `"methods"`-Sammlung nur `"get"` enthält (d.h. entfernen Sie `"post"`). Die Datei sollte wie folgt aussehen:

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
            "get"
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

1. Starten Sie den lokalen Debugger, indem Sie F5 drücken oder den Menübefehl **Ausführen** > **Debuggen starten** auswählen. Im **Ausgabe**-Fenster sollten nun beide Endpunkte im Projekt angezeigt werden:

    <pre>
    Http Functions:
            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    </pre>

1. Richten Sie in einem Browser oder von curl aus eine Anforderung an `http://localhost:7071/api/DigitsOfPi?digits=125`, und beobachten Sie die Ausgabe. (Möglicherweise bemerken Sie, dass der Codealgorithmus nicht ganz genau ist, aber wir überlassen Ihnen die Verbesserungen!) Stoppen Sie den Debugger, wenn Sie fertig sind.

1. Stellen Sie den Code mithilfe des Befehls **Für Funktions-App bereitstellen** erneut im **Azure: Functions**-Explorer bereit. Wenn Sie dazu aufgefordert werden, wählen Sie die zuvor erstellte Funktions-App aus.

1. Die Bereitstellung ist nach einigen Minuten abgeschlossen, und anschließend werden im **Ausgabefenster** die öffentlichen Endpunkte angezeigt, mit denen Sie die Tests wiederholen können.

> [!div class="nextstepaction"]
> [Ich habe eine zweite Funktion hinzugefügt: Fahren Sie mit Schritt 7 fort. >>>](tutorial-vs-code-serverless-python-07.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-ms-survey)
