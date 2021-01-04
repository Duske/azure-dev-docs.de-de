---
title: Installieren von Node.js für die Anwendungsentwicklung mit dem Azure SDK
description: In diesem Artikel erfahren Sie, wie Sie Node.js für allgemeine Entwicklungsszenarios mit Azure installieren.
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5fdaca001f82afca942a22ec9ad971f4af18c8b8
ms.sourcegitcommit: 525c4b41d85aae9c3026a070b07e00c2241ea716
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97394035"
---
# <a name="install-and-manage-nodejs-for-common-azure-development-scenarios"></a>Installieren und Verwalten von Node.js für allgemeine Azure-Entwicklungsszenarios

Ihre Installation von Node.js für die Azure-Entwicklung sollte sowohl Ihre lokale Entwicklungsumgebung als auch die Hostingumgebung berücksichtigen, in der Sie die Bereitstellung durchführen möchten. Azure bietet Hosting für Node.js sowohl unter Windows als auch unter Linux in der LTS-Version (Long Term Support, langfristige Unterstützung). 

## <a name="minimum-version-of-nodejs-8"></a>Mindestversion: Node.js 8 und höher

Das Azure SDK unterstützt Node.js 8 als Mindestversion. 

## <a name="download-and-install-nodejs-based-on-your-intended-use"></a>Herunterladen und Installieren von Node.js basierend auf dem Verwendungszweck

Sie können Node.js Ihren Anforderungen entsprechend herunterladen und installieren.
 
* [Node.js-Downloadseite](https://nodejs.org/en/download/) 
* [Offizielles Docker-Image](https://hub.docker.com/_/node/)

## <a name="managing-versions-of-nodejs"></a>Verwalten von Versionen von Node.js

Verwenden Sie nvm (Node-Versions-Manager), wenn Sie mehrere Versionen von Node.js für Ihre Azure-Entwicklung verwalten müssen.

* OSX, *nix: [nvm](https://github.com/creationix/nvm)
* Windows: [nvm für Windows](https://github.com/marcelklehr/nodist)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren Ihrer lokalen Entwicklungsumgebung zur Verwendung des Azure SDK](configure-local-development-environment.md)
