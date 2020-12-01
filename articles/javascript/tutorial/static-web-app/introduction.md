---
title: Einführung und Voraussetzungen
description: Hier erfahren Sie, wie Sie eine React-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5d70e14a9a5247f99c8b6e033af0a225b27da30b
ms.sourcegitcommit: 291768a67862336267c67819e913c16710e3875e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95820675"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Erstellen und Bereitstellen einer statischen Web-App in Azure

In diesem Tutorial erfahren Sie, wie Sie eine React-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen. 

Die React-App (create-react-app) umfasst folgende Funktionen: 
* Anzeige einer Meldung, wenn Azure-Schlüssel und -Endpunkt für maschinelles Sehen von Cognitive Services nicht gefunden werden
* Möglichkeit zum Analysieren eines Bilds mit maschinellem Sehen von Cognitive Services
    * Eingabe einer öffentlichen Bild-URL oder Analyse eines Bilds aus einer Sammlung
    * Nach Abschluss der Analyse:
        * Anzeigebild
        * Anzeigen der JSON-Ergebnisse des maschinellen Sehens 

Die GitHub-Aktion wird gestartet, wenn ein Pushvorgang an einen bestimmten Branch durchgeführt wird, und umfasst Folgendes:
* Einfügen von GitHub-Geheimnissen für den Schlüssel und den Endpunkt für maschinelles Sehen in den Build
* Erstellen des React-Clients (create-react-app)
* Verschieben der resultierenden Dateien in Ihre statische Azure-Web-App-Ressource

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="what-is-an-azure-static-web-app"></a>Was ist eine statische Azure-Web-App?

Bei der Erstellung statischer Web-Apps stehen in Azure mehrere Optionen zur Verfügung, um den gewünschten Grad an Funktionalität und Kontrolle zu erhalten. In diesem Tutorial wird der einfachste Dienst erstellt. Dabei werden Ihnen viele der Entscheidungen abgenommen, sodass Sie sich ganz auf Ihren Front-End-Code konzentrieren können und sich nicht mit der Hostingumgebung beschäftigen müssen.

## <a name="prerequisites"></a>Voraussetzungen

- [Installieren Sie die Azure CLI](/cli/azure/install-azure-cli), oder verwenden Sie [Azure Cloud Shell](https://shell.azure.com.).
- [Node.js und npm](https://nodejs.org/en/download) (installiert auf Ihrem lokalen Computer)
- [Visual Studio Code](https://code.visualstudio.com/) (installiert auf Ihrem lokalen Computer) 
    - [Azure Static Web Apps (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) zum Bereitstellen der React-App für die statische Azure-Web-App
- [Git](https://git-scm.com/downloads) zum Pushen an GitHub, wodurch wiederum die GitHub-Aktion aktiviert wird
- [GitHub-Konto](https://github.com/join) zum Forken und zum Pushen an ein Repository

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Herunterladen und Ausführen der React-App für die Bildanalyse mit Cognitive Services](run-the-react-cognitive-services-image-analyzer-app-locally.md) 