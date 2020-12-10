---
title: 'Schritt 4: Lokales Debuggen des serverlosen Azure Functions-Python-Codes mit VS Code'
description: 'Tutorialschritt 4: lokales Ausführen des VS Code-Debuggers, um Ihren serverlosen Python-Code zu überprüfen'
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperfq2
ms.openlocfilehash: a96fb6e621f1c0b1c6e6f067e28b920a8c53fb44
ms.sourcegitcommit: 0cda024089784b92c1db3a4506c1dccd6bfe6339
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96759427"
---
# <a name="4-debug-the-azure-functions-python-code-locally"></a>4: Lokales Debuggen des Azure Functions-Python-Codes

[Vorheriger Schritt: Untersuchen der Codedateien](tutorial-vs-code-serverless-python-03.md)

1. Wenn Sie das Functions-Projekt erstellen, erstellt die Azure Functions-Erweiterung außerdem eine Startkonfiguration in `.vscode/launch.json`, die eine einzelne Konfiguration mit dem Namen **Attach to Python Functions** (Anfügen an Python-Functions) enthält. Diese Konfiguration bedeutet, dass Sie einfach F5 drücken oder den Debug-Explorer verwenden können, um das Projekt zu starten:

    ![Konfiguration für den Debug-Explorer zum Starten eines Python-Projekts](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

1. Wenn Sie den Debugger starten, wird ein Terminal geöffnet, das die Ausgabe von Azure Functions anzeigt, einschließlich einer Zusammenfassung der verfügbaren Endpunkte. Die URL ist möglicherweise anders, wenn Sie einen anderen Namen als „HttpExample“ verwendet haben:

    <pre>
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    </pre>

1. Klicken Sie mit **STRG+Klicken** oder **BEFEHLSTASTE+Klicken** auf die URL im Visual Studio Code-Fenster **Ausgabe**, um einen Browser mit dieser Adresse zu öffnen, oder starten Sie einen Browser, und fügen Sie die gleiche URL ein.

    Der Endpunkt ist in jedem Fall `api/<function_name>`, in diesem Fall `api/HttpExample`. Da diese URL jedoch keinen Namensparameter enthält, sollte im Browserfenster nur „Geben Sie einen Namen in der Abfragezeichenfolge oder im Anforderungstext ein“ angezeigt werden, wie für diesen Pfad im Code angebracht ist.

    > [!TIP]
    > Wenn Sie nicht auf die URL zugreifen können und die Ausführung hinter einem Unternehmensproxy erfolgt (und daher wahrscheinlich die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` festgelegt sind), legen Sie die Umgebungsvariable `NO_PROXY` auf `localhost,127.0.0.1` fest, und wiederholen Sie den Vorgang.

1. Versuchen Sie nun, zur Verwendung einen Namensparameter hinzuzufügen, z. B. `http://localhost:7071/api/HttpExample?name=Visual%20Studio%20Code`, und im Browserfenster sollte die Meldung „Hello Visual Studio Code. Diese über HTTP ausgelöste Funktion wurde erfolgreich ausgeführt.“ angezeigt werden, die zeigt, dass Sie diesen Codepfad ausgeführt haben.

1. Wenn Sie den Namenswert einem JSON-Anforderungstext übergeben möchten, können Sie ein Tool wie curl mit dem JSON-Code inline verwenden:

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    In PowerShell können Sie auch das Cmdlet [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) verwenden.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
    ```

    ---

    Erstellen Sie alternativ eine Datei wie *data.json*, die `{"name":"Visual Studio Code"}`enthält, und verwenden Sie den Befehl `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Um das Debuggen der Funktion zu testen, legen Sie einen Haltepunkt in der Zeile fest, die `name = req.params.get('name')` liest, und richten Sie erneut eine Anforderung an die URL.

    Der Visual Studio Code-Debugger sollte in dieser Zeile stoppen, sodass Sie Variablen untersuchen und den Code schrittweise durchlaufen können.

    (Eine kurze exemplarische Vorgehensweise zum grundlegenden Debuggen finden Sie unter [Visual Studio Code Tutorial – Configure and run the debugger](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger) (Visual Studio Code-Tutorial: Konfigurieren und Ausführen des Debuggers).)

1. Wenn Sie die Funktion gründlich lokal getestet haben, stoppen Sie den Debugger (mit dem Menübefehl **Ausführen** > **Debuggen stoppen** oder dem Befehl **Trennen** in der Debugsymbolleiste).

> [!NOTE]
> Wenn der Fehler „Die in ‚local.settings.json‘ angegebene ‚AzureWebJobsStorage‘-Verbindung konnte nicht überprüft werden.“ auftritt, enthält die Datei *local.settings.json* in Ihrem Projekt die Zeile `"AzureWebJobsStorage": "UseDevelopmentStorage=true"`. Diese Zeile zeigt an, dass der Debugger erwartet, den Azure Storage-Emulator lokal zu verwenden, dieser ist aber nicht installiert. In diesem Fall können Sie [den Azure Storage-Emulator installieren](/azure/storage/common/storage-use-emulator#get-the-storage-emulator), [den Emulator starten und initialisieren](/azure/storage/common/storage-use-emulator#start-and-initialize-the-storage-emulator) und den Debugger neu starten.
>
> Ändern Sie alternativ die Zeile in der JSON-Datei in `"AzureWebJobsStorage": ""`, und starten Sie den Debugger erneut.

> [!div class="nextstepaction"]
> [Ich habe den Debugger lokal ausgeführt: Fahren Sie mit Schritt 5 fort. >>>](tutorial-vs-code-serverless-python-05.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-ms-survey)
