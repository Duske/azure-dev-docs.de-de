---
title: 'Schritt 2: Erstellen einer serverlosen Python-Funktion für Azure Functions mit VS Code'
description: 'Tutorial, Schritt 2: Hinzufügen einer serverlosen Python-Funktion mit der Azure Functions-Erweiterung für VS Code'
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, seo-python-october2019, contperf-fy21q2
ms.openlocfilehash: 97650e7d0f0676921ca70cf65db56b83bd4c0240
ms.sourcegitcommit: 4f9ce09cbf9663203c56f5b12ecbf70ea68090ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97911480"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Erstellen einer Python-Funktion für Azure Functions

[Vorheriger Schritt: Konfigurieren Ihrer Umgebung](tutorial-vs-code-serverless-python-01.md)

In diesem Artikel erstellen Sie eine Python-Funktion für Azure Functions (serverlos) mit Visual Studio Code. Der Code für Azure Functions wird innerhalb eines Functions-_Projekts_ verwaltet, das Sie vor der Codeerstellung erstellen.

1. Im **Azure: Functions**-Explorer (wird mit dem Azure-Symbol auf der linken Seite geöffnet) wählen Sie das Befehlssymbol **Neues Projekt erstellen** aus, oder öffnen Sie die Befehlspalette (F1), und wählen Sie **Azure Functions: Neues Projekt erstellen** aus.

    ![„Neues Projekt erstellen“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. In den folgenden Eingabeaufforderungen:

    | Prompt | Wert | BESCHREIBUNG |
    | --- | --- | --- |
    | Wählen Sie einen Ordner aus, der Ihr Functions-Projekt enthalten soll. | Aktuell geöffneter Ordner | Der Ordner, in dem das Projekt erstellt wird. Sie sollten das Projekt in einem Unterordner erstellen. |
    | Sprache auswählen | **Python** | Die Sprache, die für die Funktion verwendet werden soll, die die für den Code verwendete Vorlage bestimmt. |
    | Auswählen des Python-Interpreters zum Erstellen einer virtuellen Umgebung | (Verwenden Sie den angegebenen Standardpfad, oder geben Sie den Pfad zu einem geeigneten Interpreter manuell ein, wenn kein Pfad angegeben ist.) | Der für eine virtuelle Umgebung zu verwendende Python-Interpreter. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | **HTTP-Trigger** | Eine Funktion, die einen HTTP-Trigger verwendet, wenn eine HTTP-Anforderung an den Endpunkt der Funktion gerichtet wird. (Für Azure Functions gibt es eine Reihe von anderen Triggern. Weitere Informationen finden Sie unter [Welche Möglichkeiten bestehen mit Functions?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Angeben eines Funktionsnamens | HttpExample | Der Name wird für einen Unterordner verwendet, der den Code der Funktion zusammen mit Konfigurationsdaten enthält, und definiert auch den Namen des HTTP-Endpunkts. Verwenden Sie „HttpExample“ anstelle der Standardeinstellung „HTTPTrigger1“, um die Funktion selbst vom Trigger zu unterscheiden. |
    | Autorisierungsstufe | **Anonym** | Bei der anonymen Autorisierung ist die Funktion für jeden öffentlich zugänglich. |

1. Der Prozess erstellt ein Element vom Typ **Lokales Projekt**, das im Explorer angezeigt wird, unter dem sich eine Auflistung mit dem Namen **Functions** befindet, die die Funktion mit dem angegebenen Namen enthält (hier **HttpExample**):

    ![Explorer-Ansicht eines neuen Python-Projekts in Azure Functions](media/tutorial-vs-code-serverless-python/explorer-view-new-python-project-in-azure-functions.png)

    Wenn Visual Studio Code Ihnen beim Öffnen von *\_\_init\_\_.py* mitteilt, dass Sie keinen Python-Interpreter ausgewählt haben, öffnen Sie die Befehlspalette (**F1**), und wählen Sie den **Python: Interpreter auswählen**-Befehl und dann die virtuelle Umgebung im lokalen `.venv`-Ordner aus (der als Teil des Projekts erstellt wurde).

    ![Auswählen der mit dem Python-Projekt erstellten virtuellen Umgebung](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

1. Der VS Code-Editor wird auch mit der Datei *\_\_init\_\_.py* geöffnet, die den Standardfunktionscode enthält. Beachten Sie im Editor oben das Steuerelement **HttpExample > __init.py__**:

    ![Dateiselektor-Steuerelement für eine Funktion in VS Code](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-01.png)

    Wenn Sie **__init.py__** auswählen, werden in VS Code alle Dateien angezeigt, die die Funktion bilden: der Code in *\_\_init\_\_.py*, die Konfiguration in *function.json* und Beispieldaten in *sample.dat*. Sie können eine beliebige Datei auswählen, um zwischen Ihnen zu wechseln:

    ![Dateiselektor-Steuerelement für Funktionscodedateien](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-02.png)

    Wenn Sie **HttpExample** auswählen, zeigt VS Code entsprechend alle Dateien und Ordner auf *Projektebene* an:

    ![Projektselektor-Steuerelement für eine Funktion in VS Code](media/tutorial-vs-code-serverless-python/file-selector-in-azure-functions-editor-03.png)

> [!TIP]
> Wenn Sie eine weitere Funktion im selben Projekt erstellen möchten, verwenden Sie den Befehl **Funktion erstellen** im **Azure: Functions**-Explorer, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie den Befehl **Azure Functions: Funktion erstellen**, und wählen Sie ihn aus. Beide Befehle fordern Sie zur Eingabe eines Funktionsnamens auf (d. h. den Namen des Endpunkts). Anschließend wird ein weiterer Funktionsunterordner im Projekt mit seinen eigenen *\_\_init\_\_.py*-, *function.json* und *sample.dat*-Dateien erstellt.
>
> ![Erstellen von Funktionen mithilfe von „Neue Funktion“ im Azure Functions-Explorer](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Ich habe die Funktion erstellt: Fahren Sie mit Schritt 3 fort. >>>](tutorial-vs-code-serverless-python-03.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/python-functions-qs-ms-survey)
