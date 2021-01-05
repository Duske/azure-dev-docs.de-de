---
title: Einführung und Voraussetzungen
description: Hier erfahren Sie, wie Sie eine React-/TypeScript-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: dfd5803fe79a0a000173d2d4e3fe52b3c4f029c5
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687450"
---
# <a name="1-build-and-deploy-a-static-web-app-to-azure"></a>1. Erstellen und Bereitstellen einer statischen Web-App in Azure

In diesem Tutorial erfahren Sie, wie Sie eine React-/TypeScript-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen. Die React-App ermöglicht das Analysieren eines Bilds mit maschinellem Sehen von Cognitive Services.

* [**Beispielcode**](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

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
> [Grundlegendes zur Architektur und zur Bereitstellung von Client-Apps mit GitHub-Aktion](./application-architecture.md) 
