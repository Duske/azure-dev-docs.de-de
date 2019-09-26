---
title: Erstellen einer Python-Funktion für Azure Functions mit Visual Studio Code
description: 'Tutorialschritt 2: Verwenden der Azure Functions-Erweiterung für VS Code'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 9af09298c479f7584ffaece422c135e75138f0fc
ms.sourcegitcommit: d6575ac86449380b5a9c6c66aa722cb33ed53438
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186164"
---
# <a name="create-the-python-function"></a>Erstellen der Python-Funktion

[Vorheriger Schritt: Voraussetzungen](tutorial-vs-code-serverless-python-01.md)

1. Code für Azure Functions wird innerhalb eines Functions-_Projekts_ verwaltet, das Sie erstellen, bevor Sie den Code erstellen. Im **Azure: Functions**-Explorer (wird mit dem Azure-Symbol auf der linken Seite geöffnet) wählen Sie das Befehlssymbol **Neues Projekt** aus, oder öffnen Sie die Befehlspalette (F1), und wählen Sie **Azure Functions: Neues Projekt erstellen** aus.

    ![Schaltfläche „Neues Projekt erstellen“ im Functions-Explorer](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. In den folgenden Eingabeaufforderungen:

    | Prompt | Wert | BESCHREIBUNG |
    | --- | --- | --- |
    | Angeben eines Ordners für das Projekt | Aktuell geöffneter Ordner | Der Ordner, in dem das Projekt erstellt wird. Sie sollten das Projekt in einem Unterordner erstellen. |
    | Auswählen einer Sprache für Ihr Funktions-App-Projekt | **Python** | Die Sprache, die für die Funktion verwendet werden soll, die die für den Code verwendete Vorlage bestimmt. |
    | Auswählen einer Vorlage für die erste Funktion Ihres Projekts | **HTTP-Trigger** | Eine Funktion, die einen HTTP-Trigger verwendet, wenn eine HTTP-Anforderung an den Endpunkt der Funktion gerichtet wird. (Für Azure Functions gibt es eine Reihe von anderen Triggern. Weitere Informationen finden Sie unter [Welche Möglichkeiten bestehen mit Functions?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Angeben eines Funktionsnamens | HttpExample | Der Name wird für einen Unterordner verwendet, der den Code der Funktion zusammen mit Konfigurationsdaten enthält, und definiert auch den Namen des HTTP-Endpunkts. Verwenden Sie „HttpExample“ anstelle der Standardeinstellung „HTTPTrigger“, um die Funktion selbst vom Trigger zu unterscheiden. |
    | Autorisierungsstufe | **Anonym** | Bei der anonymen Autorisierung ist die Funktion für jeden öffentlich zugänglich. |
    | Auswählen, wie Sie Ihr Projekt öffnen möchten | **In aktuellem Fenster öffnen** | Öffnet das Projekt im aktuellen Visual Studio Code-Fenster. |

1. Nach kurzer Zeit wird eine Meldung angezeigt, dass das neue Projekt erstellt wurde. Im **Explorer** befindet sich der für die Funktion erstellte Unterordner, und Visual Studio Code öffnet die Datei *\_\_init\_\_.py*, die den Standardfunktionscode enthält:

    [![Ergebnis der Erstellung eines neuen Python-Functions-Projekts](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Wenn Visual Studio Code Ihnen beim Öffnen von *\_\_init\_\_.py* mitteilt, dass Sie keinen Python-Interpreter ausgewählt haben, öffnen Sie die Befehlspalette (**F1**), und wählen Sie den **Python: Interpreter auswählen**-Befehl und dann die virtuelle Umgebung im lokalen `.env`-Ordner aus (der als Teil des Projekts erstellt wurde). Die Umgebung muss explizit auf Python 3.6x basieren, wie bereits im vorherigen Artikel unter [Voraussetzungen](tutorial-vs-code-serverless-python-01.md#prerequisites) erwähnt.
    >
    > ![Auswählen der mit dem Projekt erstellten virtuellen Umgebung](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Wenn Sie eine weitere Funktion im selben Projekt erstellen möchten, verwenden Sie den Befehl **Funktion erstellen** im **Azure: Functions**-Explorer, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie den Befehl **Azure Functions: Funktion erstellen**, und wählen Sie ihn aus. Beide Befehle fordern Sie zur Eingabe eines Funktionsnamens auf (des Namens des Endpunkts) und erstellen dann einen Unterordner mit den Standarddateien.
>
> ![Befehl „Neue Funktion“ im Azure: Functions-Explorer](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Ich habe die Funktion erstellt.](tutorial-vs-code-serverless-python-03.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
