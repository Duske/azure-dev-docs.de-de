---
title: Installieren von Node.js für die Anwendungsentwicklung mit dem Azure SDK
description: In diesem Artikel erfahren Sie, wie Sie Node.js für allgemeine Entwicklungsszenarios mit Azure installieren.
ms.topic: how-to
ms.date: 03/11/2021
ms.custom: devx-track-js
ms.openlocfilehash: 1e346c42b34eb0e38ec7869196dce4c171c897a0
ms.sourcegitcommit: 3536f174735cd3bb7da7e4b266fbf43349a22b67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103193425"
---
# <a name="install-and-manage-nodejs-for-azure-development"></a>Installieren und Verwalten von Node.js für die Azure-Entwicklung

Ihre Installation von Node.js für die Azure-Entwicklung sollte sowohl Ihre lokale Entwicklungsumgebung als auch die Hostingumgebung berücksichtigen, in der Sie die Bereitstellung durchführen möchten. Azure bietet Hosting für Node.js sowohl unter Windows als auch unter Linux in der LTS-Version (Long Term Support, langfristige Unterstützung). 

## <a name="minimum-version-of-nodejs-for-azure-sdk"></a>Mindestversion von Node.js für das Azure SDK

Das Azure SDK unterstützt folgende Mindestversion:

* Node.js 8. 

## <a name="minimum-version-of-nodejs-for-azure-services"></a>Mindestversion von Node.js für Azure-Dienste

Wenn Sie Ihre Anwendung in Azure hosten möchten, ohne sie in einem Container zu hosten, müssen Sie die Mindestversion von Node.js überprüfen, die für den zum Hosten verwendeten Dienst unterstützt wird:

* [Statische Azure-Web-Apps](/azure/static-web-apps/): Client und API
* [Azure Functions](/azure/azure-functions/): nur API
* [Azure-Apps](/azure/app-service/): Server

## <a name="manage-versions-of-nodejs"></a>Verwalten der Node.js-Versionen

Wenn Sie mehrere Node.js-Versionen in Ihren lokalen Umgebungen und Remoteumgebungen verwalten müssen, verwenden Sie eine der folgenden Optionen:

* [NVM](#manage-nodejs-version-with-nvm): Node-Versions-Manager
* [Container](#manage-nodejs-version-with-containers): Verwenden Sie einen Container mit einer bestimmten Mindestversion von Node.js.

## <a name="manage-nodejs-version-with-nvm"></a>Verwalten der Node.js-Version mit NVM

Verwenden Sie nvm (Node-Versions-Manager), wenn Sie mehrere Versionen von Node.js für Ihre Azure-Entwicklung verwalten müssen.

* OSX, *nix: [nvm](https://github.com/creationix/nvm)
* Windows: [nvm für Windows](https://github.com/marcelklehr/nodist)

## <a name="manage-nodejs-version-with-containers"></a>Verwalten der Node.js-Version mit Containern

Sie können die Node.js-Version umgebungsübergreifend mithilfe von Containern verwalten. Die Visual Studio Code-Erweiterung [Remotecontainer](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) vereinfacht die Verwendung von Containern erheblich. 

Nachdem Sie [Docker](https://www.docker.com/) installiert und Ihr Projekt geöffnet haben, verwenden Sie die Erweiterung, um das Projekt in einen Container zu laden und zum Debuggen an den Container anzufügen.  

## <a name="download-and-install-nodejs-based-on-your-intended-use"></a>Herunterladen und Installieren von Node.js basierend auf dem Verwendungszweck

Sie können Node.js Ihren Anforderungen entsprechend herunterladen und installieren.
 
* [Node.js-Downloadseite](https://nodejs.org/en/download/) 
* [Offizielles Docker-Image](https://hub.docker.com/_/node/)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren Ihrer lokalen Entwicklungsumgebung zur Verwendung des Azure SDK](configure-local-development-environment.md)
