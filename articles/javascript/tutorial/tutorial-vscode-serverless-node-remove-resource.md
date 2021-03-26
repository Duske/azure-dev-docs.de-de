---
title: Entfernen kostspieliger Azure-Remoteressourcen nach dem Bereitstellen der Azure Functions-Anwendung
description: Entfernen (bereinigen) Sie Azure-Remoteressourcen, damit keine Kosten anfallen. Klicken Sie zum Bereinigen der Ressourcen mit der rechten Maustaste im Azure Functions-Explorer auf die Funktions-App, und wählen Sie **Funktions-App löschen** aus.
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: devx-track-js, contperf-fy21q2
ms.openlocfilehash: cfb5941e6152a2919e3f2b7570d94df66e0c74f9
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117784"
---
# <a name="5-clean-up-azure-resources-for-azure-functions-tutorial"></a>5. Tutorial: Bereinigen von Azure-Ressourcen für Azure Functions

[Vorheriger Schritt: Bereitstellen der Funktions-App](tutorial-vscode-serverless-node-deploy-hosting.md)

## <a name="remove-remote-azure-resources"></a>Entfernen von Azure-Remoteressourcen

Die von Ihnen erstellte Functions-App enthält Ressourcen, für die minimale Kosten anfallen können (siehe [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/)). Verwenden Sie die Visual Studio Code-Erweiterung „Azure-Ressourcengruppen“, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen. 

1. Suchen Sie in der Liste nach dem Namen der Ressourcengruppe.
1. Klicken Sie mit der rechten Maustaste auf den Namen der Ressourcengruppe, und wählen Sie die Option **Löschen** aus.

    :::image type="content" source="../media/visual-studio-code-azure-resources-extension-remove-resource-group.png" alt-text="Verwenden Sie die Visual Studio Code-Erweiterung „Azure-Ressourcengruppen“, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen.":::

[!INCLUDE [Next steps for using VSCode extensions](../includes/tutorial-next-steps-vscode-extensions.md)]

[!INCLUDE [Next steps for using JavaScript on Azure](../includes/tutorial-next-steps-js-azure.md)]

## <a name="learn-more-about-azure-functions"></a>Weitere Informationen zu Azure Functions

* [Azure Functions: Entwicklerhandbuch](/azure/azure-functions/functions-reference)
* [JavaScript-Entwicklerhandbuch für Azure Functions](/azure/azure-functions/functions-reference-node)
* [Absichern von Azure Functions](/azure/azure-functions/security-concepts)
* Überlegungen zum [Speicher](/azure/azure-functions/storage-considerations) und zur [Leistung](/azure/azure-functions/functions-best-practices)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ich bin fertig.](../how-to/develop-serverless-apps.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=clean-up-resources)
