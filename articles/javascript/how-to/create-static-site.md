---
title: Erstellen von statischen Websites mit Node.js, APIs und Markup
description: In diesem Artikel wird erläutert, wie Azure zum Erstellen einer JAMstack-App (JavaScript, APIs und Markup) verwendet wird.
ms.topic: how-to
ms.date: 08/20/2019
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 9b00c9b27767109a16526f70dfcb6bfb592fa4a4
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561106"
---
# <a name="build-jamstack-static-site-web-apps-on-azure-with-nodejs"></a>Erstellen von JAMstack-Web-Apps (statischen Websites) in Azure mit Node.js

Mithilfe einer Kombination aus *JavaScript* (Front-End), *APIs* (von Drittanbietern oder benutzerdefinierte APIs, die als serverloser Code erstellt wurden) und *Markup*-Vorlagen (HTML und CSS) können hervorragende Web-Apps produktiv erstellt und verwaltet werden, die als statische Webseiten bereitgestellt werden. Mit dieser Kombination, die auch als JAMstack bekannt ist, vermeiden Sie es, komplizierten Back-End-Code für Webseiten schreiben zu müssen. Stattdessen stellt das System nur statische Webseiten (HTML, CSS und JavaScript) bereit, wenn diese Seiten Ihre APIs für serverseitige Arbeit aufrufen. Da Sie diese APIs mit serverlosen Technologien mit automatischer Skalierung schreiben können, vermeiden Sie Kosten- und Sicherheitsrisiken von herkömmlichen Always On-Servern oder -Webhosts. (Weitere Informationen finden Sie unter [jamstack.org](https://jamstack.org/).)

Zum Implementieren einer statischen/JAMstack-Website in Azure müssen Sie mehrere Tools und Dienste verwenden:

- Konfigurieren Sie eine Datenbank je nach Bedarf.
- Implementieren Sie serverlosen API.Code in Azure Functions. Diese APIs verwenden in der Regel die Datenbank.
- Wählen Sie Bibliotheken aus, die Sie für die Front-End-Entwicklung verwenden möchten, z. B. Angular. Anschließend laden Sie die statischen HTML-, CSS- und JavaScript-Dateien in Azure Blob Storage hoch. Dieser Dienst stellt einen integrierten Webserver bereit.
- Erstellen Sie einen Reverseproxy, damit Ihr gesamter Datenverkehr über eine URL-Domäne verläuft.

Sie können sich eine Demonstration des Prozesses einer Build 2019-Sitzung ansehen: [Produktive Front-End-Entwicklung mit JavaScript, Visual Studio Code und Azure](https://azure.microsoft.com/resources/videos/build-2019-productive-front-end-development-with-javascript-visual-studio-code-and-azure/).

> [!VIDEO https://medius.studios.ms/Embed/Video-nc/B19-BRK3021?latestplayer=true]

Ein ausführliches Tutorial finden Sie unter [Bereitstellen einer statischen Website in Azure](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-01.md).

Weitere Informationen finden Sie in den folgenden Artikeln:

- **Datenbanken:** Sie können eine beliebige Datenbank verwenden, z. B. die verschiedenen Datenbankdienste in Azure, die unter [Integrieren von Azure-Datenbanken in Node.js-Apps](integrate-database.md).
  
- **Serverlose APIs:**

  - Beginnen Sie mit [Bereitstellen von Azure Functions in Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-install.md). Dort erhalten Sie eine Einführung in Azure Functions im Kontext von Visual Studio Code, die viele Details vereinfacht.
  - Danach verfügen Sie über ein Azure Functions-Projekt (ein Ordner), der einen Unterordner mit dem Namen für die Funktion enthält, der dem Namen des HTTP-Endpunkts entspricht. Dieser Funktionsordner enthält die Datei *index.js*, die den Code enthält.
  - Sie können die Funktion nach Bedarf anpassen und auch weitere Funktionen zum Projekt hinzufügen. Anschließend stellen Sie sie wieder in Azure bereit, um sie öffentlich zur Verfügung zu stellen.
  - Weitere Ressourcen zur serverlosen Entwicklung finden Sie unter [Schreiben von serverlosem Node.js.Code in Azure](develop-serverless-apps.md).

- **Stellen Sie Ihr Front-End in Azure Storage bereit:** Da Sie nun über die APIs verfügen, können Sie Ihren Front-End-Code mit einem beliebigen Framework schreiben, um diese APIs zu verwenden. Befolgen Sie die Anweisungen im Artikel [Tutorial: Hosten einer statischen Website in Blob Storage](/azure/storage/blobs/storage-blob-static-website-host), um die Dateien in Azure hochzuladen und das statische Websitehosting zu aktivieren.

- **Erstellen Sie einen Reverseproxy:** Reverseproxys werden unter [Verwenden von Azure Functions-Proxys](/azure/azure-functions/functions-proxies) beschrieben. Sie ermöglichen Ihnen, bestimmte Anforderungen mühelos an verschiedene URLs weiterzuleiten. In diesem Fall sollten Sie Anforderungen für Ihre Front-End-Dateien an die Azure Storage-URL weiterleiten, an der Sie die Dateien bereitgestellt haben. API-Anforderungen sollten Sie an die Azure Functions-URL weiterleiten.

  - Diese Proxys erstellen Sie, indem Sie die Datei *proxies.json* Ihrem Azure Functions-Projekt so bearbeiten, dass sie wie im Folgenden gezeigt aussieht. Ersetzen Sie `<storage_url>` und `<api_url>` durch Ihre eigenen URLs:
  
    ```json
    {
      "$schema": "http://json.schemastore.org/proxies",
      "proxies": {
        "Static frontend on Azure Storage": {
          "matchCondition": {
            "route": "/{*restOfPath}"
          },
          "backendUri": "<storage_url>/{restOfPath}"
        },
        "Azure Functions API": {
          "matchCondition": {
            "route": "/api/{*restOfPath}"
          },
          "backendUri": "<api_url>/api/{restOfPath}"
        }
      }
    }
    ```