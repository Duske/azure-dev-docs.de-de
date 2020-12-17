---
title: Serverloser Node.js-Code mit Azure Functions
description: Azure Functions bietet eine serverlose Codeinfrastruktur für die Erstellung dynamischer bedarfsgesteuerter HTTP-Endpunkte.
ms.topic: how-to
ms.date: 10/27/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, contperf-fy21q2
ms.openlocfilehash: b90837cdb144e433a7d776e571291ff09610a845
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522306"
---
# <a name="use-azure-functions-to-develop-nodejs-serverless-code"></a>Entwickeln von serverlosem Node.js-Code mithilfe von Azure Functions

Azure Functions bietet eine serverlose Codeinfrastruktur für die Erstellung dynamischer bedarfsgesteuerter HTTP-Endpunkte. Bei serverlosem Code handelt es sich um JavaScript- oder TypeScript-Code, der ausgeführt wird, um auf verschiedene Ereignisse zu reagieren. 

Funktionen werden zusätzlich zu einem Webdienst als Code oder Docker-Container ausgeführt und so abstrahiert, dass Sie sich auf den Code für Ihren Endpunkt konzentrieren können. Da mithilfe von Funktionen auch eine andere Funktion ausgelöst werden kann, können Sie vorhandene gehostete Back-End-Serverfunktionen durch einen Funktionsarbeitsstream ersetzen, wodurch die Verwaltung des entsprechenden Servers entfällt. 

## <a name="what-is-a-function-resource"></a>Was ist eine Funktionsressource?

Eine Azure-Funktionsressource ist eine logische Einheit für alle zugehörigen Funktionen an einem einzelnen geografischen Azure-Standort. Die Ressource kann eine einzelne Funktion oder zahlreiche Funktionen enthalten, die voneinander unabhängig oder über Eingabe- oder Ausgabetrigger miteinander verknüpft sein können. Sie können aus vielen gängigen Funktionen auswählen oder eigene Funktionen erstellen.

:::image type="content" source="../media/howto-serverless/portal-screenshot-new-azure-function-type.png" alt-text="Sie können aus vielen gängigen Funktionen auswählen oder eigene Funktionen erstellen.":::

Die Einstellungen für Funktionsressourcen umfassen typische serverlose Konfigurationen wie Umgebungsvariablen, Authentifizierung, Protokollierung und CORS.  

## <a name="durable-stateful-functions"></a>Langlebige, zustandsbehaftete Funktionen 

[Langlebige Funktionen](/azure/azure-functions/durable/durable-functions-overview) behalten den *Zustand* bei oder dienen zur Verwaltung zeitintensiver Funktionen in Azure. [Erstellen Sie Ihre erste langlebige Funktion in JavaScript.](/azure/azure-functions/durable/quickstart-js-vscode)

## <a name="static-web-apps-include-functions"></a>Statische Web-Apps mit Funktionen 

Verwenden Sie bei der Entwicklung einer statischen Front-End-Clientanwendung (beispielsweise Angular, React oder Vue), die auch serverlose APIs benötigt, [statische Web-Apps](/azure/static-web-apps/getting-started?tabs=react) mit Funktionen, um beides zusammenzufassen. 

## <a name="a-simple-javascript-function-for-http-requests"></a>Einfache JavaScript-Funktion für HTTP-Anforderungen

Bei einer Funktion handelt es sich um eine exportierte asynchrone Funktion mit Anforderung und Kontextinformationen. Der folgende partielle Screenshot des Azure-Portals zeigt den Funktionscode: 

:::image type="content" source="../media/howto-serverless/portal-screenshot-azure-function-http.png" alt-text="Partieller Screenshot: Azure-Funktion im Azure-Portal":::

## <a name="develop-functions-locally-with-visual-studio-code-and-extensions"></a>Lokales Entwickeln von Funktionen mit Visual Studio Code und Erweiterungen

Erstellen Sie Ihre [erste Funktion](/azure/azure-functions/functions-create-first-function-vs-code) mit Visual Studio Code. Visual Studio Code vereinfacht viele Details mit der [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

Diese Erweiterung unterstützt Sie mit allgemeinen Vorlagen bei der Erstellung von JavaScript- und TypeScript-Funktionen. 

JavaScript-Beispiel einer HTTP-Funktion für Azure: 

```nodejs
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

TypeScript-Beispiel einer HTTP-Funktion für Azure: 

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };

};

export default httpTrigger;
```

## <a name="configuring-the-function"></a>Konfigurieren der Funktion

Funktionen werden mithilfe der Datei **function.json** konfiguriert. Damit können Sie konfigurieren, wie die Funktion ausgelöst wird ("direction": in) und was sie zurückgibt ("direction": out). Außerdem können Sie Umgebungsvariablen und andere Informationen festlegen, die erforderlich sind, damit die Funktion funktioniert. Weitere Informationen zu Triggern und Bindungen finden Sie [hier](/azure/azure-functions/functions-triggers-bindings?tabs=javascript.md). 

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

## <a name="develop-functions-remotely-using-the-azure-portal"></a>Verwenden der Remoteentwicklung von Funktionen über das Azure-Portal

Wenn Sie [eine Azure-Funktion über das Azure-Portal erstellen](https://ms.portal.azure.com/#create/Microsoft.FunctionApp), können Sie die Funktion konfigurieren, den Code in einer vorab aufgefüllten Vorlage schreiben und die Funktion testen. 

Im Portal werden keine TypeScript-Funktionen erstellt, sondern nur JavaScript-Funktionen. Wenn Sie TypeScript verwenden möchten, müssen Sie die Funktion entweder herunterladen oder sie lokal in Visual Studio Code mit der Funktionserweiterung erstellen. 

## <a name="next-steps"></a>Nächste Schritte

Das [JavaScript-Entwicklerhandbuch für Azure Functions](/azure/azure-functions/functions-reference-node) ist ein guter Ausgangspunkt. 

Verwenden Sie das Microsoft Learn-Modul, um sich mit dem [Aktivieren automatischer Updates in einer Web-App mithilfe von Azure Functions und SignalR Service](/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr/) vertraut zu machen.

* [Ausführen von Code mit einem Timer](/azure/azure-functions/functions-create-scheduled-function)
* [Tutorial: Hochladen von Bilddaten in die Cloud mit Azure Storage](/azure/storage/blobs/storage-upload-process-images?tabs=nodejsv10)
* [Ausführen von Code, wenn eine Nachricht in Azure Queue Storage geschrieben wird](/azure/azure-functions/functions-create-storage-queue-triggered-function)
* [Speichern von unstrukturierten Daten mit Azure Functions und Azure Cosmos DB](/azure/azure-functions/functions-integrate-store-unstructured-data-cosmosdb?tabs=javascript)
* [Beispiele für Node.js und Azure Functions](/samples/browse/?languages=javascript%2Cnodejs&products=azure-functions)
