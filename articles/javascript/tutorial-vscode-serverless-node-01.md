---
title: Bereitstellen von Azure Functions in Node.js in Visual Studio Code
description: 'Teil 1 des Tutorials: Einführung und Voraussetzungen.'
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 00039e7320e603b025b00887ec548c534a1f1612
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "91110513"
---
# <a name="deploy-azure-functions-from-visual-studio-code"></a>Bereitstellen von Azure Functions in Visual Studio Code

In diesem Tutorial verwenden Sie Visual Studio Code und die Azure Functions-Erweiterung, um eine in JavaScript geschriebene Azure Functions-Anwendung zu erstellen und bereitzustellen.

## <a name="walkthrough-video"></a>Video mit exemplarischer Vorgehensweise

Dieses Video enthält eine umfassende exemplarische Vorgehensweise für den Inhalt dieses Artikels.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-Azure-Functions-Visual-Studio-Code/player]

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/)
- Die [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions">Installieren der Azure Functions-Erweiterung</a>

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

Um das lokale Debuggen zu aktivieren, müssen Sie die [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) installieren. Diesen Schritt können Sie direkt in Visual Studio Code ausführen.

1. Starten Sie Visual Studio Code.

1. Öffnen Sie die **Befehlspalette** (**F1**), geben Sie **Azure Functions: Azure Functions Core Tools installieren oder aktualisieren** ein, und drücken Sie die **EINGABETASTE**, um den Befehl auszuführen.

1. Wählen Sie zum Überprüfen der Installation in VS Code den Menübefehl **Terminal** > **Neues Terminal** aus, und führen Sie dann den Befehl `func` aus. Die Ausgabe des Befehls sollte in etwa wie folgt aussehen (mit Nutzungsinformationen).

    <pre>
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %

    Azure Functions Core Tools (2.4.419 Commit hash: c9c1724d002bd90b2e6b41393915ea3a26bcf0ce)
    Function Runtime Version: 2.0.12332.0
    </pre>

> [!div class="nextstepaction"]
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-serverless-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=getting-started)
