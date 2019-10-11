---
title: 'Tutorial: Bereinigen von Azure-Ressourcen – Azure Functions in Python'
description: 'Tutorialschritt 8: Bereinigen von Azure-Ressourcen, um laufende Gebühren zu vermeiden'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: aac220a57c83b274c46886f4548e41b41ec8327e
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172421"
---
# <a name="tutorial-clean-up-azure-resources-for-azure-functions"></a>Tutorial: Bereinigen von Azure-Ressourcen für Azure Functions

[Vorheriger Schritt: Hinzufügen einer Speicherbindung](tutorial-vs-code-serverless-python-07.md)

Der Funktions-APP, die Sie erstellt haben, umfasst Ressourcen, die minimale Kosten verursachen können (siehe [Functions – Preise](https://azure.microsoft.com/pricing/details/functions/)). Um die Ressourcen zu bereinigen, klicken Sie mit der rechten Maustaste auf die Funktions-App im **Azure: Functions**-Explorer, und wählen Sie **Funktions-App löschen** aus.

Sie können auch das [Azure-Portal](https://portal.azure.com) aufrufen, **Ressourcengruppen** im Navigationsbereich auf der linken Seite auswählen, die im Rahmen dieses Tutorials erstellte Ressourcengruppe auswählen und dann den Befehl **Ressourcengruppe löschen** verwenden.

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch zum Absolvieren dieser exemplarische Vorgehensweise zum Bereitstellen von Python-Code für Azure Functions! Sie sind jetzt bereit, viele weitere serverlose Funktionen zu erstellen.

Wie bereits erwähnt, können Sie mehr über die Functions-Erweiterung erfahren, indem Sie das zugehörige GitHub-Repository [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions) besuchen. Beschreibungen von Problemen und sonstige Beiträge sind ebenfalls willkommen.

Lesen Sie die [Einführung in Azure Functions](/azure/azure-functions/functions-overview), um die verschiedenen Trigger zu untersuchen, die Sie verwenden können.

Weitere Informationen zu Azure-Diensten, die Sie von Python aus verwenden können, einschließlich Datenspeicherung sowie KI und Machine Learning-Diensten, finden Sie unter [Azure für Python-Cloudentwickler](/azure/python/?view=azure-python).

Es gibt auch andere Azure-Erweiterungen für Visual Studio Code, die möglicherweise für Sie hilfreich sind. Suchen Sie einfach im Erweiterungen-Explorer nach „Azure“:

![Azure-Erweiterungen für Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Einige beliebte Erweiterungen sind:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Weitere Informationen finden Sie im [Tutorial: Bereitstellen für App Service](tutorial-deploy-app-service-on-linux-01.md).
- [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Ich bin fertig.](https://docs.microsoft.com/python/azure/?view=azure-python)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=08-clean-up-resources)
