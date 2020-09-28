---
title: 'Schritt 2: Erstellen einer Python-Funktion für Azure Functions mit VS Code'
description: 'Tutorialschritt 2: Verwenden der Azure Functions-Erweiterung für VS Code'
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: cc0d8f5241cafceb9e087557104a624f5f7adb92
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2020
ms.locfileid: "90773057"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Erstellen einer Python-Funktion für Azure Functions

[Vorheriger Schritt: Konfigurieren Ihrer Umgebung](tutorial-vs-code-serverless-python-01.md)

In diesem Artikel erstellen Sie eine Python-Funktion für Azure Functions mit Visual Studio Code. Der Code für Azure Functions wird innerhalb eines Functions-_Projekts_ verwaltet, das Sie vor der Codeerstellung erstellen.

1. Im **Azure: Functions**-Explorer (wird mit dem Azure-Symbol auf der linken Seite geöffnet) wählen Sie das Befehlssymbol **Neues Projekt erstellen** aus, oder öffnen Sie die Befehlspalette (F1), und wählen Sie **Azure Functions: Neues Projekt erstellen** aus.

    ![„Neues Projekt erstellen“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. In den folgenden Eingabeaufforderungen:

    | Prompt | Wert | BESCHREIBUNG |
    | --- | --- | --- |
    | Angeben eines Ordners für das Projekt | Aktuell geöffneter Ordner | Der Ordner, in dem das Projekt erstellt wird. Sie sollten das Projekt in einem Unterordner erstellen. |
    | Auswählen einer Sprache für Ihr Funktions-App-Projekt | **Python** | Die Sprache, die für die Funktion verwendet werden soll, die die für den Code verwendete Vorlage bestimmt. |
    | Auswählen des Python-Interpreters zum Erstellen einer virtuellen Umgebung | (Verwenden Sie den angegebenen Standardpfad, oder geben Sie den Pfad zu einem geeigneten Interpreter manuell ein, wenn kein Pfad angegeben ist.) | Der für eine virtuelle Umgebung zu verwendende Python-Interpreter. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | **HTTP-Trigger** | Eine Funktion, die einen HTTP-Trigger verwendet, wenn eine HTTP-Anforderung an den Endpunkt der Funktion gerichtet wird. (Für Azure Functions gibt es eine Reihe von anderen Triggern. Weitere Informationen finden Sie unter [Welche Möglichkeiten bestehen mit Functions?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Angeben eines Funktionsnamens | HttpExample | Der Name wird für einen Unterordner verwendet, der den Code der Funktion zusammen mit Konfigurationsdaten enthält, und definiert auch den Namen des HTTP-Endpunkts. Verwenden Sie „HttpExample“ anstelle der Standardeinstellung „HTTPTrigger1“, um die Funktion selbst vom Trigger zu unterscheiden. |
    | Autorisierungsstufe | **Anonym** | Bei der anonymen Autorisierung ist die Funktion für jeden öffentlich zugänglich. |

1. Nach kurzer Zeit wird eine Meldung angezeigt, dass das neue Projekt erstellt wurde. Im **Explorer** befindet sich der für die Funktion erstellte Unterordner, und Visual Studio Code öffnet die Datei *\_\_init\_\_.py*, die den Standardfunktionscode enthält:

    ![Ergebnis der Erstellung eines neuen Python-Projekts in Azure Functions](media/tutorial-vs-code-serverless-python/display-results-of-new-python-project-in-azure-functions.png)

    Wenn Visual Studio Code Ihnen beim Öffnen von *\_\_init\_\_.py* mitteilt, dass Sie keinen Python-Interpreter ausgewählt haben, öffnen Sie die Befehlspalette (**F1**), und wählen Sie den **Python: Interpreter auswählen**-Befehl und dann die virtuelle Umgebung im lokalen `.env`-Ordner aus (der als Teil des Projekts erstellt wurde).

    ![Auswählen der mit dem Python-Projekt erstellten virtuellen Umgebung](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

> [!TIP]
> Wenn Sie eine weitere Funktion im selben Projekt erstellen möchten, verwenden Sie den Befehl **Funktion erstellen** im **Azure: Functions**-Explorer, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie den Befehl **Azure Functions: Funktion erstellen**, und wählen Sie ihn aus. Beide Befehle fordern Sie zur Eingabe eines Funktionsnamens auf (des Namens des Endpunkts) und erstellen dann einen Unterordner mit den Standarddateien.
>
> ![Erstellen von Funktionen mithilfe von „Neue Funktion“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Ich habe die Funktion erstellt: Fahren Sie mit Schritt 3 fort. >>>](tutorial-vs-code-serverless-python-03.md)

