---
title: Bereinigen der Ressourcen nach dem Bereitstellen einer Node.js-Container-App in Visual Studio Code
description: 'Teil 8 des Docker-Tutorials: Bereinigen von Ressourcen'
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: 4a7ff0e9a84e10223bbe8b55f5309506593f6e07
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117894"
---
# <a name="part-8-clean-up-resources"></a>Teil 8: Bereinigen der Ressourcen

[Vorheriger Schritt: Streamen von Protokollen](tutorial-vscode-docker-node-07.md)

## <a name="delete-resource-group"></a>Ressourcengruppe löschen

Die von Ihnen erstellte App Service-Instanz für den Container beinhaltet einen App Service-Plan, der Kosten verursachen kann. Verwenden Sie die Visual Studio Code-Erweiterung „Azure-Ressourcengruppen“, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen.

1. Suchen Sie in der Liste nach dem Namen der Ressourcengruppe.
1. Klicken Sie mit der rechten Maustaste auf den Namen der Ressourcengruppe, und wählen Sie die Option **Löschen** aus.

    :::image type="content" source="../../media/visual-studio-code-azure-resources-extension-remove-resource-group.png" alt-text="Verwenden Sie die Visual Studio Code-Erweiterung „Azure-Ressourcengruppen“, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen.":::

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [tutorial-next-steps](../../includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Ich bin fertig.](../../how-to/deploy-containers.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=clean-up-resources)