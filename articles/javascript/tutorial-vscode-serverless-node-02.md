---
title: Erstellen der Azure Functions-Anwendung in Visual Studio Code
description: 'Teil 2 des serverlosen Tutorials: Erstellen der Azure Functions-App'
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: 39481fc2b5472bd2f9e54946d3ab1dd6051f00d1
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365113"
---
# <a name="create-the-local-functions-app"></a>Erstellen der lokalen Funktions-App

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-serverless-node-01.md)

In diesem Schritt erstellen Sie eine lokale Azure Functions-Anwendung, die eine Funktion mit einem [HTTP-Trigger](/azure/azure-functions/functions-reference-node#http-triggers-and-bindings) enthält. Eine Azure Functions-App kann viele Funktionen mit [unterschiedlichen Triggern](/azure/azure-functions/functions-triggers-bindings) enthalten. Der HTTP-Trigger behandelt insbesondere eingehenden HTTP-Datenverkehr.

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung Visual Studio Code in einem geeigneten Ordner für das Projekt aus:

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. Klicken Sie in VS Code auf das Azure-Logo, um den **Azure Functions**-Explorer zu öffnen, und wählen Sie dann den Befehl **Projekt erstellen** aus:

    ![Erstellen einer lokalen Funktions-App in VS Code](media/functions-extension/create-function-app-project.png)

1. Wählen Sie an den ersten beiden Eingabeaufforderungen den aktuellen Ordner und dann **JavaScript** als Programmiersprache aus.

1. Wählen Sie an der Eingabeaufforderung **Select a template for your project's first function** (Wählen Sie eine Vorlage für die erste Funktion Ihres Projekts aus) die Option **HTTP-Trigger** aus:

    ![Auswählen des Triggers für die Funktion](media/functions-extension/create-function-choose-template.png)

1. Geben Sie an der Eingabeaufforderung **Provide a function name** (Geben Sie einen Funktionsnamen an) den Namen **HttpExample** ein. (Verwenden Sie nicht den Standardnamen „HttpTrigger“, da dieser mit dem Namen des Triggers identisch ist und zu Verwechslungen führen kann.)

    ![Eingeben eines Funktionsnamens](media/functions-extension/create-function-name.png)

1. Wählen Sie an der Eingabeaufforderung **Autorisierungsstufe** die Option **Anonym** aus:

    ![ Auswählen von „Anonym“ an der Eingabeaufforderung „Autorisierungsstufe“](media/functions-extension/create-function-anonymous-auth.png)

1. Nach einigen Augenblicken ist VS Code mit der Erstellung des Projekts fertig. Sie haben nun einen für die Funktion benannten Ordner *HttpExample*, der drei Dateien enthält:

    | Dateiname | BESCHREIBUNG |
    | --- | --- |
    | *index.js* |  Der Quellcode, der auf die HTTP-Anforderung antwortet. |
    | *function.json* | Die [Bindungskonfiguration](/azure/azure-functions/functions-triggers-bindings) für den HTTP-Trigger. |
    | *sample.dat* | Eine Platzhalterdatendatei, die zeigt, dass der Ordner weitere Dateien enthalten kann. Sie können diese Datei ggf. löschen, da sie in diesem Tutorial nicht verwendet wird. |

    ![Ergebnis der Erstellung einer Funktions-App](media/functions-extension/create-function-app-results.png)

> [!div class="nextstepaction"]
> [Ich habe die Funktions-App erstellt.](tutorial-vscode-serverless-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)