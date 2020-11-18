---
title: include file tutorial-azure-web-app-mongodb-01.md
description: include file tutorial-azure-web-app-mongodb-01.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 61bb61d147e01061dcf58701132feddd09c53b59
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94341075"
---
In diesem Abschnitt des Tutorials benötigen Sie ein Azure-Abonnement und die gesamte Software, um dieses Tutorial verwenden zu können.

## <a name="create-or-use-existing-azure-subscription"></a>Erstellen oder Verwenden eines vorhandenen Azure-Abonnements 

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension)

## <a name="install-software"></a>Installieren von Software

- [Node.js 8.x oder höher und npm](https://nodejs.org/en/download): Der Node.js-Paket-Manager, der auf dem lokalen Computer installiert ist
- [Docker](https://docs.docker.com/get-docker/): Docker wird verwendet, um eine lokale MongoDB-Datenbank bereitzustellen, ohne dass MongoDB installiert werden muss. 
    - Wenn Sie Docker verwenden müssen, um eine lokale MongoDB-Datenbank abzurufen, müssen Sie auch Folgendes verwenden:
        -  [Dev Container](https://code.visualstudio.com/docs/remote/containers) von Visual Studio stellen verschiedene gängige Container für die JavaScript-Entwicklung bereit. 
        - [Remotecontainer](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
    - Wenn Sie bereits über eine lokale MongoDB-Datenbank verfügen und Docker nicht installieren möchten, können Sie diesen Schritt überspringen. Alle Schritte, die den Development Container für den Zugriff auf eine lokal ausgeführte MongoDB-Datenbank verwenden, können für die Verwendung Ihrer eigenen lokalen MongoDB-Datenbank wiederverwendet werden, sofern die folgende MongoDB-URL verfügbar ist: 
        - `mongodb://localhost:27017`
- [Visual Studio Code](https://code.visualstudio.com/) auf dem lokalen Computer installiert. 
- Visual Studio Code-Erweiterungen:
    - [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code (installiert aus Visual Studio Code).
    - [Azure-Datenbanken](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren? 

Optionale Visual Studio Code-Erweiterungen:
* [MongoDB für VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)
* [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)