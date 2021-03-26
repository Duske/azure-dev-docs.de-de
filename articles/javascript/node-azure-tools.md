---
title: Toolauswahl – JavaScript – Azure
description: Installieren der einzelnen Node.js- und JavaScript-Entwicklungstools in Azure
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 8d34bae3e1ac789d24db59a41ab22673e15cc819
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117834"
---
# <a name="tools-for-javascript-developers-on-azure"></a>Tools für JavaScript-Entwickler in Azure 

JavaScript ist ein Ökosystem mit zahlreichen Tools. In diesem Artikel wird eine Auswahl von Tools vorgestellt, die von Microsoft für JavaScript-Entwickler erstellt und verwaltet werden. Tools wurden für neue Azure-Dienste und Bereitstellungs-/Hostingszenarien verbessert. 

Sie benötigen diese Tools nicht für die Verwendung von Azure. Sie ermöglichen lediglich eine bessere Nutzung in Bezug auf Funktionen und Unterstützung. 

## <a name="azure-portal"></a>Azure-Portal

Über das [Azure-Portal](https://portal.azure.com/) haben Sie Zugriff auf alle Abonnements und Ressourcen für Ihr Konto. 

## <a name="visual-studio-code"></a>Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com) ist die bevorzugte IDE für die JavaScript-Entwicklung für Azure. Die Schnittstelle, Features und Erweiterungen wirken zusammen, um die Entwicklungszeit zu verkürzen und Frustration bei der Entwicklung zu verringern. 

Erstellen Sie im Stammverzeichnis des lokalen Entwicklungsprojekts einen Projektarbeitsbereich, und fügen Sie dann alle relevanten Konfigurationen, Einstellungen und Erweiterungen hinzu. Checken Sie die Arbeitsbereichsdatei mit dem Projekt ein, damit jedes Teammitglied Zugriff auf die Einstellungen und Tools hat, die für das Projekt erforderlich sind.

Mit Visual Studio Code haben Sie mehrere Vorteile:

* In Visual Studio Code wird die Azure-Referenzdokumentation inline angezeigt.
* Visual Studio Code bietet Anweisungsvervollständigung.
* Wenige mehrdeutige Typen oder Objekte

Visual Studio Code bietet umfassende Dokumentation für die [JavaScript-Projektnutzung](https://code.visualstudio.com/docs/nodejs/working-with-javascript). 

## <a name="visual-studio-code-extensions"></a>Erweiterungen für Visual Studio Code

Nutzen Sie die folgenden kostenlosen Erweiterungen, um Azure-Dienste direkt in Visual Studio Code zu verwenden.

| Tool | BESCHREIBUNG  |
|:---------:|---------|
|[Azure-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)|Mit dieser Erweiterung von Microsoft können Sie Folgendes nutzen: Websitehosting, SQL- und MongoDB-Daten, Docker-Container, serverlose Funktionen und mehr – alles in Azure und über VS Code.|

Falls Sie die Installation einzelner Erweiterungen bevorzugen, hilft Ihnen diese Liste mit den beliebtesten Azure-Diensten weiter:

| Tool | BESCHREIBUNG  |
|:---------:|---------|
| [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)| Anmeldungs- und Abonnementverwaltung|
| [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions "Link zur Azure Functions-Erweiterung") <br> [![Azure Functions-Tools](media/node-azure-tools/icon-azure-functions.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) | Erstellen, Verwalten, Anzeigen, Debuggen und Bereitstellen von Funktionen|
| [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice "Link zur Azure App Service-Erweiterung") <br> [![App Service-Tools](media/node-azure-tools/icon-azure-app-service.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) | Durchsuchen von Websites und des Azure-Portals, Erstellen neuer Websites und Bereitstellen für Slots |
| [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb "Link zur Cosmos DB-Erweiterung" )  <br> [![Cosmos DB-Tools](media/node-azure-tools/icon-cosmos-db.png)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)| Erstellen, Durchsuchen und Aktualisieren global verteilter Datenbanken mit mehreren Datenbanken in Azure |
| [Docker](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)   <br> [![Docker](media/node-azure-tools/icon-docker.png)](https://marketplace.visualstudio.com/items?itemName=formulahendry.docker-explorer)| Verwalten von Docker-Containern und -Images, Docker Hub und Azure Container Registry |
|[Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)|Azure Storage, z. B. Blobcontainer, Dateifreigaben, Tabellen und Warteschlangen|

> [!div class="nextstepaction"]
> [Weitere Azure-Erweiterungen über den Visual Studio Code Marketplace abrufen](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance)


## <a name="typescript"></a>TypeScript

[TypeScript](https://www.typescriptlang.org/download) verfügt über alle Features von JavaScript, aber zusätzlich noch über eine weitere Ebene: das Typsystem von TypeScript. Ihr vorhandener funktionierender JavaScript-Code ist gleichzeitig TypeScript-Code. Der Hauptvorteil von TypeScript besteht darin, dass auf unerwartetes Verhalten in Ihrem Code hingewiesen werden kann, um die Fehlerwahrscheinlichkeit zu verringern.

## <a name="typescript-and-the-azure-sdk-client-libraries"></a>TypeScript und die Azure SDK-Clientbibliotheken

Die Referenzdokumentation zu den Azure SDK-Clientbibliothek wird für TypeScript geschrieben, da die Clientbibliotheken mit TypeScript geschrieben werden. Sie müssen nicht TypeScript verwenden, um die Azure SDK-Clientbibliotheken verwenden zu können. 

Erfahren Sie mehr über die [TypeScript-Richtlinien für das Azure SDK](https://azure.github.io/azure-sdk/typescript_introduction.html).


## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Die Azure CLI ist für die Verwaltung von Azure-Ressourcen über die Befehlszeile optimiert. 

Die Azure CLI ermöglicht die folgenden Verwendungsszenarien:

* [Lokale Installation der Azure CLI](/cli/azure/install-az-cli2)
* [Azure Cloud Shell](https://shell.azure.com/)
* [Docker-Container](/cli/azure/run-azure-cli-docker)

Bei Verwendung des Azure-Portals steht die Azure CLI im Portal über die obere Navigationsleiste zur Verfügung.

:::image type="content" source="media/azure-tools/azure-portal-select-azure-cloud-shell.png" alt-text="Bei Verwendung des Azure-Portals steht die Azure CLI im Portal über die obere Navigationsleiste zur Verfügung.":::

