---
title: Erstellen der Azure Functions-Anwendung in Visual Studio Code
description: Erstellen Sie eine lokale (serverlose) Azure Functions-Anwendung, die eine Funktion mit einem HTTP-Trigger enthält. Eine Azure Functions-App kann viele Funktionen mit unterschiedlichen Triggern enthalten. Der HTTP-Trigger behandelt insbesondere eingehenden HTTP-Datenverkehr.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: devx-track-js, contperfq2
ms.openlocfilehash: d7d39018ab9645755c6c9bfe414f7507e9d091c7
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515191"
---
# <a name="2-create-the-local-functions-app-with-the-visual-studio-code-_functions_-extension"></a>2. Erstellen der lokalen Functions-App mit der _Functions_-Erweiterung von Visual Studio Code

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-serverless-node-install.md)

In diesem Schritt erstellen Sie eine lokale (serverlose) Azure Functions-Anwendung, die eine Funktion mit einem [HTTP-Trigger](/azure/azure-functions/functions-reference-node#http-triggers-and-bindings) enthält. Eine Azure Functions-App kann viele Funktionen mit [unterschiedlichen Triggern](/azure/azure-functions/functions-triggers-bindings) enthalten. Der HTTP-Trigger behandelt insbesondere eingehenden HTTP-Datenverkehr.

1. Führen Sie an einem Terminal oder einer Eingabeaufforderung Visual Studio Code in einem geeigneten Ordner für das Projekt aus:

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. Wählen Sie in Visual Studio Code das Azure-Logo aus, um den **Azure Functions**-Explorer zu öffnen, und wählen Sie dann den Befehl **Projekt erstellen** aus:

    ![Erstellen einer lokalen Funktions-App in VS Code](../media/functions-extension/create-function-app-project.png)

1. Wählen Sie an den ersten beiden Eingabeaufforderungen den aktuellen Ordner und dann **JavaScript** als Programmiersprache aus.

1. Wählen Sie an der Eingabeaufforderung **Select a template for your project's first function** (Wählen Sie eine Vorlage für die erste Funktion Ihres Projekts aus) die Option **HTTP-Trigger** aus:

    ![Auswählen des Triggers für die Funktion](../media/functions-extension/create-function-choose-template.png)

1. Geben Sie an der Eingabeaufforderung **Provide a function name** (Geben Sie einen Funktionsnamen an) den Namen **HttpExample** ein. (Verwenden Sie nicht den Standardnamen „HttpTrigger“, da dieser mit dem Namen des Triggers identisch ist und zu Verwechslungen führen kann.)

    ![Eingeben eines Funktionsnamens](../media/functions-extension/create-function-name.png)

1. Wählen Sie an der Eingabeaufforderung **Autorisierungsstufe** die Option **Anonym** aus:

    ![ Auswählen von „Anonym“ an der Eingabeaufforderung „Autorisierungsstufe“](../media/functions-extension/create-function-anonymous-auth.png)

1. Nach einigen Augenblicken ist VS Code mit der Erstellung des Projekts fertig. Sie haben nun einen für die Funktion benannten Ordner *HttpExample*, der drei Dateien enthält:

    | Dateiname | BESCHREIBUNG |
    | --- | --- |
    | *index.js* |  Der Quellcode, der auf die HTTP-Anforderung antwortet. |
    | *function.json* | Die [Bindungskonfiguration](/azure/azure-functions/functions-triggers-bindings) für den HTTP-Trigger. |
    | *sample.dat* | Eine Platzhalterdatendatei, die zeigt, dass der Ordner weitere Dateien enthalten kann. Sie können diese Datei ggf. löschen, da sie in diesem Tutorial nicht verwendet wird. |

    ![Ergebnis der Erstellung einer Funktions-App](../media/functions-extension/create-function-app-results.png)

## <a name="http-function-javascript-template-code"></a>JavaScript-Vorlagencode für HTTP-Funktionen

Der grundlegende Code zum Antworten auf die HTTP-Anforderung wird für Sie bereitgestellt. Wenn Ihnen die HTTP-Anforderung (Parameter _req_) und die Antwortobjekte bekannt sind, sollte die Funktion vertraut erscheinen. Sie geben Antwortinformationen mit dem **context**-Objekt für die Eigenschaft `res` zurück.  

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

Jeder [Triggertyp](/azure/azure-functions/functions-triggers-bindings?tabs=csharp) einer Funktion stellt eine Vorlagenfunktion für Sie bereit, damit Sie sich sofort auf den Code für Ihre Anwendung konzentrieren können. Sehen Sie sich bei der Umstellung von Express.js auf Azure Functions die [notwendigen Änderungen](/azure/azure-functions/shift-expressjs?tabs=javascript) für Ihre Anwendung an. 

> [!div class="nextstepaction"]
> [Ich habe die Funktions-App erstellt.](tutorial-vscode-serverless-node-test-local.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
