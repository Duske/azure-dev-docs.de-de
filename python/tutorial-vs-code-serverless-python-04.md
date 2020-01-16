---
title: 'Tutorial: Lokales Debuggen des Azure Functions-Python-Codes mit VS Code'
description: 'Tutorialschritt 4: lokales Ausführen des VS Code-Debuggers, um Ihren Python-Code zu überprüfen'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: ffd5d433166c44edd8c021fd29cb7e43395df7ff
ms.sourcegitcommit: ac68fb174d606c7af2bfa79fe32b8ca7b73c86a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946683"
---
# <a name="tutorial-debug-the-azure-functions-python-code-locally"></a>Tutorial: Lokales Debuggen des Azure Functions-Python-Codes

[Vorheriger Schritt: Untersuchen der Codedateien](tutorial-vs-code-serverless-python-03.md)

Sie können Ihren Azure Functions-Python-Code lokal in Visual Studio Code debuggen.

1. Wenn Sie das Functions-Projekt erstellen, erstellt die Visual Studio Code-Erweiterung außerdem eine Startkonfiguration in `.vscode/launch.json`, die eine einzelne Konfiguration mit dem Namen **Attach to Python Functions** (Anfügen an Python-Functions) enthält. Diese Konfiguration bedeutet, dass Sie einfach F5 drücken oder den Debug-Explorer verwenden können, um das Projekt zu starten:

    ![Konfiguration für den Debug-Explorer zum Starten eines Python-Projekts](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

1. Wenn Sie den Debugger starten, wird ein Terminal geöffnet, das die Ausgabe von Azure Functions anzeigt, einschließlich einer Zusammenfassung der verfügbaren Endpunkte. Die URL ist möglicherweise anders, wenn Sie einen anderen Namen als „HttpExample“ verwendet haben:

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Klicken Sie mit **STRG+Klicken** oder **BEFEHLSTASTE+Klicken** auf die URL im Visual Studio Code-Fenster **Ausgabe**, um einen Browser mit dieser Adresse zu öffnen, oder starten Sie einen Browser, und fügen Sie die gleiche URL ein. Der Endpunkt ist in jedem Fall `api/<function_name>`, in diesem Fall `api/HttpExample`. Da diese URL jedoch keinen Namensparameter enthält, sollte im Browserfenster nur „Geben Sie einen Namen in der Abfragezeichenfolge oder im Anforderungstext ein“ angezeigt werden, wie für diesen Pfad im Code angebracht ist.

1. Versuchen Sie nun, zur Verwendung einen Namensparameter hinzuzufügen, z.B. `http://localhost:7071/api/HttpExample?name=VS%20Code`, und im Browserfenster sollte die Meldung „Hello Visual Studio Code!“ angezeigt werden, um zu zeigen, dass Sie diesen Codepfad ausgeführt haben.

1. Wenn Sie den Namenswert einem JSON-Anforderungstext übergeben möchten, können Sie ein Tool wie curl mit dem JSON-Code inline verwenden:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    In PowerShell können Sie auch das Cmdlet [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6) verwenden.

    ---

    Erstellen Sie alternativ eine Datei wie *data.json*, die `{"name":"Visual Studio Code"}`enthält, und verwenden Sie den Befehl `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Um das Debuggen der Funktion zu testen, legen Sie einen Haltepunkt in der Zeile fest, die `name = req.params.get('name')` liest, und richten Sie erneut eine Anforderung an die URL. Der Visual Studio Code-Debugger sollte in dieser Zeile stoppen, sodass Sie Variablen untersuchen und den Code schrittweise durchlaufen können. (Eine kurze exemplarische Vorgehensweise zum grundlegenden Debuggen finden Sie unter [Visual Studio Code Tutorial – Configure and run the debugger](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger) (Visual Studio Code-Tutorial: Konfigurieren und Ausführen des Debuggers).)

1. Wenn Sie die Funktion gründlich lokal getestet haben, stoppen Sie den Debugger (mit dem Menübefehl **Debuggen** > **Debuggen stoppen** oder dem Befehl **Trennen** in der Debugsymbolleiste).

> [!div class="nextstepaction"]
> [Ich habe den Debugger lokal ausgeführt.](tutorial-vs-code-serverless-python-05.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=04-test-debug)
