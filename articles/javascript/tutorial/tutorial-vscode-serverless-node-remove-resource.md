---
title: Entfernen kostspieliger Azure-Remoteressourcen nach dem Bereitstellen der Azure Functions-Anwendung
description: Entfernen (bereinigen) Sie Azure-Remoteressourcen, damit keine Kosten anfallen. Klicken Sie zum Bereinigen der Ressourcen mit der rechten Maustaste im Azure Functions-Explorer auf die Funktions-App, und wählen Sie **Funktions-App löschen** aus.
ms.topic: tutorial
ms.date: 08/31/2020
ms.custom: devx-track-js, contperf-fy21q2
ms.openlocfilehash: 428330b34b3c315d01c2209840de62001caa747a
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97522048"
---
# <a name="5-clean-up-azure-resources-for-azure-functions-tutorial"></a>5. Tutorial: Bereinigen von Azure-Ressourcen für Azure Functions

[Vorheriger Schritt: Bereitstellen der Funktions-App](tutorial-vscode-serverless-node-deploy-hosting.md)

## <a name="remove-remote-azure-resources"></a>Entfernen von Azure-Remoteressourcen

Die von Ihnen erstellte Functions-App enthält Ressourcen, für die minimale Kosten anfallen können (siehe [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/)). Um die Ressourcen zu bereinigen, klicken Sie mit der rechten Maustaste auf die Funktions-App im **Azure: Functions**-Explorer, und wählen Sie **Funktions-App löschen** aus.

Sie können auch das [Azure-Portal](https://portal.azure.com) aufrufen, **Ressourcengruppen** im Navigationsbereich auf der linken Seite auswählen, die im Rahmen dieses Tutorials erstellte Ressourcengruppe auswählen und dann den Befehl **Ressourcengruppe löschen** verwenden.

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
