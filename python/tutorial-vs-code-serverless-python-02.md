---
title: 'Tutorial: Erstellen einer Python-Funktion für Azure Functions mit Visual Studio Code'
description: 'Tutorialschritt 2: Verwenden der Azure Functions-Erweiterung für VS Code'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 5ccb93abf37011de5075768b73ed330fd91461ea
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466031"
---
# <a name="tutorial-create-a-python-function-for-azure-functions"></a>Tutorial: Erstellen einer Python-Funktion für Azure Functions

[Vorheriger Schritt: Voraussetzungen](tutorial-vs-code-serverless-python-01.md)

In diesem Artikel erstellen Sie eine Python-Funktion für Azure Functions mit Visual Studio Code. Code für Azure Functions wird innerhalb eines Functions-_Projekts_ verwaltet, das Sie erstellen, bevor Sie den Code erstellen.

1. Im **Azure: Functions**-Explorer (wird mit dem Azure-Symbol auf der linken Seite geöffnet) wählen Sie das Befehlssymbol **Neues Projekt** aus, oder öffnen Sie die Befehlspalette (F1), und wählen Sie **Azure Functions: Neues Projekt erstellen** aus.

    ![„Neues Projekt erstellen“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. In den folgenden Eingabeaufforderungen:

    | Prompt | Wert | BESCHREIBUNG |
    | --- | --- | --- |
    | Angeben eines Ordners für das Projekt | Aktuell geöffneter Ordner | Der Ordner, in dem das Projekt erstellt wird. Sie sollten das Projekt in einem Unterordner erstellen. |
    | Auswählen einer Sprache für Ihr Funktions-App-Projekt | **Python** | Die Sprache, die für die Funktion verwendet werden soll, die die für den Code verwendete Vorlage bestimmt. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | **HTTP-Trigger** | Eine Funktion, die einen HTTP-Trigger verwendet, wenn eine HTTP-Anforderung an den Endpunkt der Funktion gerichtet wird. (Für Azure Functions gibt es eine Reihe von anderen Triggern. Weitere Informationen finden Sie unter [Welche Möglichkeiten bestehen mit Functions?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Angeben eines Funktionsnamens | HttpExample | Der Name wird für einen Unterordner verwendet, der den Code der Funktion zusammen mit Konfigurationsdaten enthält, und definiert auch den Namen des HTTP-Endpunkts. Verwenden Sie „HttpExample“ anstelle der Standardeinstellung „HTTPTrigger“, um die Funktion selbst vom Trigger zu unterscheiden. |
    | Autorisierungsstufe | **Anonym** | Bei der anonymen Autorisierung ist die Funktion für jeden öffentlich zugänglich. |
    | Auswählen, wie Sie Ihr Projekt öffnen möchten | **In aktuellem Fenster öffnen** | Öffnet das Projekt im aktuellen Visual Studio Code-Fenster. |

    > [!NOTE]
    > Wenn sowohl Python 3.6 als auch Python 3.7 installiert ist, verwendet Visual Studio Code standardmäßig Python 3.6 für das Azure Functions-Projekt. Wenn Sie Python 3.7 verwenden möchten, müssen Sie derzeit zuerst eine Python 3.7-Umgebung erstellen und aktivieren und dann den Befehl `func init` über ein Terminal ausführen. Starten Sie anschließend von diesem Ordner aus Visual Studio Code mithilfe des Befehls `code .`.

1. Nach kurzer Zeit wird eine Meldung angezeigt, dass das neue Projekt erstellt wurde. Im **Explorer** befindet sich der für die Funktion erstellte Unterordner, und Visual Studio Code öffnet die Datei *\_\_init\_\_.py*, die den Standardfunktionscode enthält:

    ![Ergebnis der Erstellung eines neuen Python-Projekts in Azure Functions](media/tutorial-vs-code-serverless-python/display-results-of-new-python-project-in-azure-functions.png)

    > [!NOTE]
    > Wenn Visual Studio Code Ihnen beim Öffnen von *\_\_init\_\_.py* mitteilt, dass Sie keinen Python-Interpreter ausgewählt haben, öffnen Sie die Befehlspalette (**F1**), und wählen Sie den **Python: Interpreter auswählen**-Befehl und dann die virtuelle Umgebung im lokalen `.env`-Ordner aus (der als Teil des Projekts erstellt wurde). Die Umgebung muss explizit auf Python 3.6x basieren, wie bereits im vorherigen Artikel unter [Voraussetzungen](tutorial-vs-code-serverless-python-01.md#prerequisites) erwähnt.
    >
    > ![Auswählen der mit dem Python-Projekt erstellten virtuellen Umgebung](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

> [!TIP]
> Wenn Sie eine weitere Funktion im selben Projekt erstellen möchten, verwenden Sie den Befehl **Funktion erstellen** im **Azure: Functions**-Explorer, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie den Befehl **Azure Functions: Funktion erstellen**, und wählen Sie ihn aus. Beide Befehle fordern Sie zur Eingabe eines Funktionsnamens auf (des Namens des Endpunkts) und erstellen dann einen Unterordner mit den Standarddateien.
>
> ![Erstellen von Funktionen mithilfe von „Neue Funktion“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Ich habe die Funktion erstellt.](tutorial-vs-code-serverless-python-03.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
