---
title: Bereitstellen von Azure Functions in Node.js in Visual Studio Code
description: 'Teil 1 des Tutorials: Einführung und Voraussetzungen.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 5dd41d710df629565699cff3bfd8e4bca7677087
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686052"
---
# <a name="deploy-azure-functions-from-visual-studio-code"></a>Bereitstellen von Azure Functions in Visual Studio Code

In diesem Tutorial verwenden Sie Visual Studio Code und die Azure Functions-Erweiterung, um eine in JavaScript geschriebene Azure Functions-Anwendung zu erstellen und bereitzustellen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- Die [Azure Functions-Erweiterung](vscode:extension/ms-azuretools.vscode-azurefunctions).
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager.

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurefunctions">Installieren der Azure Functions-Erweiterung</a>

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

Um das lokale Debuggen zu aktivieren, müssen Sie die [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) installieren. Diesen Schritt können Sie direkt in Visual Studio Code ausführen.

1. Starten Sie Visual Studio Code.

1. Öffnen Sie die **Befehlspalette** (**F1**), geben Sie **Azure Functions: Azure Functions Core Tools installieren oder aktualisieren** ein, und drücken Sie die **EINGABETASTE**, um den Befehl auszuführen.

1. Wählen Sie zum Überprüfen der Installation in VS Code den Menübefehl **Terminal** > **Neues Terminal** aus, und führen Sie dann den Befehl `func` aus. Die Ausgabe des Befehls sollte in etwa wie folgt aussehen (mit Nutzungsinformationen).

    ```output
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
    ```

> [!div class="nextstepaction"]
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-serverless-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=getting-started)
