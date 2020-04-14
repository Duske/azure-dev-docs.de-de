---
title: Bereitstellen von Docker-Containern in Azure App Service in Visual Studio Code
description: 'Teil 1 des Tutorials: Einführung und Voraussetzungen.'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 1a14010d362ed3858d319a141fd24e5ea1b0e714
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740568"
---
# <a name="deploy-containers-to-azure-app-service"></a>Bereitstellen von Containern in Azure App Service

In diesem Tutorial verwenden Sie Visual Studio Code, um eine Node.js-Containeranwendung mithilfe von Docker zu erstellen, das Containerimage in eine Registrierung zu pushen und das Image dann in Azure App Service bereitzustellen.

## <a name="walkthrough-video"></a>Video mit exemplarischer Vorgehensweise

Dieses Video enthält eine umfassende exemplarische Vorgehensweise für den Inhalt dieses Artikels.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-containers-to-Azure-App-Service/player]

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/)
- Die [Docker-Erweiterung](vscode:extension/ms-azuretools.vscode-docker).
- Die [Azure App Service-Erweiterung](vscode:extension/ms-azuretools.vscode-azureappservice).
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager
- [Docker](https://www.docker.com/community-edition)

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-docker">Installieren der Docker-Erweiterung</a>

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azureappservice">Installieren der Azure App Service-Erweiterung</a>

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="verify-docker-install"></a>Überprüfen der Docker-Installation

Vergewissern Sie sich, dass Docker richtig installiert ist, indem Sie den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung ausführen:

```bash
docker --version
```

Die Ausgabe sollte etwa wie folgt aussehen:

<pre>
Docker Version 17.12.0-ce, build c97c6d6
</pre>

> [!div class="nextstepaction"]
> [Ich habe die Docker-Erweiterung installiert.](tutorial-vscode-docker-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=getting-started)
