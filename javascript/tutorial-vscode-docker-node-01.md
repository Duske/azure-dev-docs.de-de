---
title: Bereitstellen von Docker-Containern in Azure App Service in Visual Studio Code
description: 'Teil 1 des Tutorials: Einführung und Voraussetzungen.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: f37e049ab3c6dd0a01726aa9204746658540110b
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686116"
---
# <a name="deploy-containers-to-azure-app-service"></a>Bereitstellen von Containern in Azure App Service

In diesem Tutorial verwenden Sie Visual Studio Code, um eine Node.js-Containeranwendung mithilfe von Docker zu erstellen, das Containerimage in eine Registrierung zu pushen und das Image dann in Azure App Service bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- [Docker-Erweiterung](vscode:extension/ms-azuretools.vscode-docker)
- [Azure App Service-Erweiterung](vscode:extension/ms-azuretools.vscode-azureappservice)
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager
- [Docker](https://www.docker.com/community-edition)

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-docker">Installieren der Docker-Erweiterung</a>

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azureappservice">Installieren der Azure App Service-Erweiterung</a>

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="verify-docker-install"></a>Überprüfen der Docker-Installation

Vergewissern Sie sich, dass Docker richtig installiert ist, indem Sie den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung ausführen:

```bash
docker --version
```

Die Ausgabe sollte etwa wie folgt aussehen:

```output
Docker Version 17.12.0-ce, build c97c6d6
```

> [!div class="nextstepaction"]
> [Ich habe die Docker-Erweiterung installiert.](tutorial-vscode-docker-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=getting-started)
