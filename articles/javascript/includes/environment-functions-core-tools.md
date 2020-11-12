---
title: include file completed.md
description: include file completed.md
ms.topic: include
ms.date: 10/13/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 09f69293062f52dfc2190cfeb040ad45ea428c67
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338476"
---
## <a name="install-the-azure-functions-core-tools"></a>Installieren von Azure Functions Core Tools

Um das lokale Debuggen zu aktivieren, müssen Sie die [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) installieren. Diesen Schritt können Sie direkt in Visual Studio Code ausführen.

1. Starten Sie Visual Studio Code.

1. Öffnen Sie die **Befehlspalette** ( **F1** ), geben Sie **Azure Functions: Azure Functions Core Tools installieren oder aktualisieren** ein, und drücken Sie die **EINGABETASTE** , um den Befehl auszuführen.

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