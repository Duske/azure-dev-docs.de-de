---
title: Einführung und Voraussetzungen
description: Hier erfahren Sie, wie Sie eine React-/TypeScript-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: dd35ca0925b014d2fddeb064c8cbdbdea703697b
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522348"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Erstellen und Bereitstellen einer statischen Web-App in Azure

In diesem Tutorial erfahren Sie, wie Sie eine React-/TypeScript-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen. 

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

- [Node.js und npm](https://nodejs.org/en/download) (installiert auf Ihrem lokalen Computer)
- [Visual Studio Code](https://code.visualstudio.com/) (installiert auf Ihrem lokalen Computer) 
    - [Azure Static Web Apps (Vorschauversion)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) zum Bereitstellen der React-App für die statische Azure-Web-App
- [Git](https://git-scm.com/downloads) zum Pushen an GitHub, wodurch wiederum die GitHub-Aktion aktiviert wird
- [GitHub-Konto](https://github.com/join) zum Forken und zum Pushen an ein Repository
- Verwenden Sie [Azure Cloud Shell](/azure/cloud-shell/quickstart) unter Nutzung der Bash-Umgebung.

   [![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)   
- Wenn Sie möchten, können Sie auch die Azure CLI [installieren](/cli/azure/install-azure-cli), um CLI-Verweisbefehle auszuführen.
   - Wenn Sie eine lokale Installation verwenden, melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei der Azure CLI an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.  Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).
  - Installieren Sie die Azure CLI-Erweiterungen bei der ersten Verwendung, wenn Sie dazu aufgefordert werden.  Weitere Informationen zu Erweiterungen finden Sie unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Führen Sie [az version](/cli/azure/reference-index?#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Herunterladen und Ausführen der React-App für die Bildanalyse mit Cognitive Services](run-the-react-cognitive-services-image-analyzer-app-locally.md) 