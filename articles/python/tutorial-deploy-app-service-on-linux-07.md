---
title: 'Schritt 7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 7: Bereinigen von Azure-Ressourcen'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: e9fe39d97487cb58523e2fd33a8acb74c51aa00b
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96035486"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code

[Vorheriger Schritt: Streamen von Protokollen](tutorial-deploy-app-service-on-linux-06.md)

Die von Ihnen erstellte Azure App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Löschen Sie die Ressourcengruppe, in der alle Ressourcen enthalten sind, um diese Kosten zu vermeiden.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch zum Absolvieren dieser exemplarische Vorgehensweise zum Bereitstellen von Python-Code in App Service für Linux!

Wie bereits erwähnt, können Sie mehr über die App Service-Erweiterung erfahren, indem Sie das zugehörige GitHub-Repository [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) besuchen. Beschreibungen von Problemen und sonstige Beiträge sind ebenfalls willkommen.

Weitere Informationen zu Azure-Diensten, die Sie von Python aus verwenden können, einschließlich Datenspeicherung sowie KI und Machine Learning-Diensten, finden Sie unter [Azure für Python-Cloudentwickler](/python/azure/).

Es gibt auch andere Azure-Erweiterungen für VS Code, die möglicherweise für Sie hilfreich sind. Suchen Sie einfach im Erweiterungen-Explorer nach „Azure“:

![Azure-Erweiterungen für Visual Studio Code](media/deploy-azure/azure-extensions-for-visual-studio-code.png)

Einige beliebte Erweiterungen sind:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [ARM-Tools (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Ich bin fertig!](/python/azure/) 

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
