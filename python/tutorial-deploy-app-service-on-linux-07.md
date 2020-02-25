---
title: 'Schritt 7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 7: Bereinigen von Azure-Ressourcen'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 001e5d58311267221c5fafb3de89aff3859f4197
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422435"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code

[Vorheriger Schritt: Streamen von Protokollen](tutorial-deploy-app-service-on-linux-06.md)

Die von Ihnen erstellte Azure App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Löschen Sie die Ressourcengruppe, in der alle Ressourcen enthalten sind, um diese Kosten zu vermeiden.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch zum Absolvieren dieser exemplarische Vorgehensweise zum Bereitstellen von Python-Code in App Service für Linux!

Wie bereits erwähnt, können Sie mehr über die App Service-Erweiterung erfahren, indem Sie das zugehörige GitHub-Repository [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) besuchen. Beschreibungen von Problemen und sonstige Beiträge sind ebenfalls willkommen.

Weitere Informationen zu Azure-Diensten, die Sie von Python aus verwenden können, einschließlich Datenspeicherung sowie KI und Machine Learning-Diensten, finden Sie unter [Azure für Python-Cloudentwickler](https://docs.microsoft.com/python/azure/?view=azure-python).

Es gibt auch andere Azure-Erweiterungen für VS Code, die möglicherweise für Sie hilfreich sind. Suchen Sie einfach im Erweiterungen-Explorer nach „Azure“:

![Azure-Erweiterungen für Visual Studio Code](media/deploy-containers/azure-extensions-for-visual-studio-code.png)

Einige beliebte Erweiterungen sind:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [ARM-Tools (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Ich bin fertig!](https://docs.microsoft.com/python/azure/?view=azure-python) 

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=07-clean-up-resources)
