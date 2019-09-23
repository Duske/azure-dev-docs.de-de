---
title: Hinzufügen von Speicherbindungen für Azure Functions in Python mit Visual Studio Code
description: 'Tutorialschritt 7: Hinzufügen einer Bindung in Python zum Schreiben von Meldungen in Azure Storage'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 8fdb78024cc6ac1df0ee961fc424878f5f634cf2
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019938"
---
# <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Hinzufügen einer Bindung zum Schreiben von Meldungen in den Azure-Speicher

[Vorheriger Schritt: Bereitstellen einer zweiten Funktion](tutorial-vs-code-serverless-python-06.md)

Mit einer _Bindung_ können Sie eine Verbindung Ihres Funktionscodes mit Ressourcen wie z.B. Azure-Speicher herstellen, ohne Datenzugriffscode schreiben zu müssen. Eine Bindung wird in der Datei *function.json* definiert und kann sowohl die Eingabe als auch die Ausgabe darstellen. Eine Funktion kann mehrere Ein- und Ausgabebindungen, aber nur einen Trigger haben. Weitere Informationen finden Sie unter [Konzepte der Trigger und Bindungen in Azure Functions](/azure/azure-functions/functions-triggers-bindings.md).

In diesem Abschnitt fügen Sie der HttpExample-Funktion, die Sie zuvor in diesem Tutorial erstellt haben, eine Speicherbindung hinzu. Die Funktion verwendet diese Bindung, um mit jeder Anforderung Meldungen in den Speicher zu schreiben. Der fragliche Speicher verwendet das gleiche Standardspeicherkonto, das von der Funktions-App verwendet wird. Wenn Sie jedoch den Speicher stark nutzen möchten, sollten Sie das Erstellen eines separaten Kontos in Erwägung ziehen.

1. Synchronisieren Sie die Remoteeinstellungen für das Azure Functions-Projekt in Ihrer *local.settings.json*-Datei, indem Sie die Befehlspalette öffnen und **Azure Functions: Remoteeinstellungen herunterladen** auswählen. Öffnen Sie *local.settings.json*, und überprüfen Sie, ob sie einen Wert für `AzureWebJobsStorage` enthält. Der Wert ist die Verbindungszeichenfolge für das Speicherkonto.

1. Klicken Sie im Ordner `HttpExample` mit der rechten Maustaste auf *function.json*, und wählen Sie **Bindung hinzufügen** aus:

    ![Befehl „Bindung hinzufügen“ im Visual Studio Code-Explorer](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Wählen Sie in den folgenden Eingabeaufforderungen in Visual Studio Code die Werte aus, oder geben Sie sie ein:

    | Prompt | Bereitzustellender Wert |
    | --- | --- |
    | Bindungsrichtung festlegen | out |
    | Bindung mit ausgehender Richtung auswählen | Azure Queue Storage |
    | Der Name, der zum Identifizieren dieser Bindung in Ihrem Code verwendet wird | msg |
    | Die Warteschlange, an die die Nachricht gesendet wird | outqueue |
    | Einstellung aus *local.settings.json* wählen (Fragen nach der Speicherverbindung) | AzureWebJobsStorage |

1. Nachdem Sie diese Auswahl getroffen haben, vergewissern Sie sich, dass die folgende Bindung der Datei *function.json* hinzugefügt wurde:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Nachdem Sie die Bindung konfiguriert haben, können Sie sie in Ihrem Funktionscode verwenden. Wiederum wird die neu definierte Bindung in Ihrem Code als Argument der `main`-Funktion in *\_\_init\_\_.py* angezeigt. Beispielsweise können Sie die Datei *\_\_init\_\_.py* in HttpExample so ändern, dass sie der folgenden entspricht, die die Verwendung des `msg`-Arguments zum Schreiben einer Meldung mit Zeitstempel mit dem in der Anforderung verwendeten Namen zeigt. In den Kommentaren werden die spezifischen Änderungen erläutert:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
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
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Um diese Änderungen lokal zu testen, starten Sie den Debugger erneut in Visual Studio Code, indem Sie F5 drücken oder den Menübefehl **Debuggen** > **Debuggen starten** auswählen. Im **Ausgabe**-Fenster sollten nun wie zuvor die Endpunkte im Projekt angezeigt werden.

1. Rufen Sie in einem Browser die URL `http://localhost:7071/api/HttpExample?name=VS%20Code` auf, um eine Anforderung an den HttpExample-Endpunkt zu erstellen, die auch in eine Meldung in der Warteschlange schreiben sollte.

1. Um zu überprüfen, ob die Nachricht in die Warteschlange „outqueue“ (wie in der Bindung benannt) geschrieben wurde, können Sie eine der drei folgenden Methoden verwenden:

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu der Ressourcengruppe, die ihr Functions-Projekt enthält. Navigieren Sie in dieser Ressourcengruppe lokal zum Speicherkonto für das Projekt und dann zu **Warteschlangen**. Navigieren Sie auf dieser Seite zu „outqueue“, wo alle protokollierten Meldungen angezeigt werden sollten.

    1. Navigieren Sie zu der Warteschlange, und untersuchen Sie sie mit dem in Visual Studio integrierten Azure Storage-Explorer wie unter [Verbinden von Funktionen mit Azure Storage mithilfe von Visual Studio Code](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code.md) beschrieben, insbesondere im Abschnitt [Überprüfen der Ausgabewarteschlange](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue).

    1. Verwenden Sie die Azure CLI, um die Speicherwarteschlange abzufragen, wie unter [Abfragen der Speicherwarteschlange](/azure/azure-functions/functions-add-output-binding-storage-queue-python.md#query-the-storage-queue) beschrieben.

1. Stellen Sie den Code zum Testen in der Cloud mithilfe des Befehls **Für Funktions-App bereitstellen** erneut im **Azure: Functions**-Explorer bereit. Wenn Sie dazu aufgefordert werden, wählen Sie die zuvor erstellte Funktions-App aus. Nachdem die Bereitstellung abgeschlossen ist (es dauert einige Minuten!), werden im **Ausgabe**-Fenster erneut die öffentlichen Endpunkte angezeigt, mit denen Sie die Tests wiederholen können.

> [!div class="nextstepaction"]
> [Ich habe eine Speicherbindung hinzugefügt.](tutorial-vs-code-serverless-python-08.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)
