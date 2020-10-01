---
title: 'Schritt 7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 7: Bereinigen von Azure-Ressourcen'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 523a1bb305fbaf566181f87c4affa0dd251e93a5
ms.sourcegitcommit: 4af22924a0eaf01e6902631c0714045c02557de4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91209370"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Bereinigen von Ressourcen nach der Bereitstellung in Azure App Service für Linux aus Visual Studio Code

[Vorheriger Schritt: Streamen von Protokollen](tutorial-deploy-app-service-on-linux-06.md)

Die von Ihnen erstellte Azure App Service-Instanz beinhaltet einen App Service-Plan, der Kosten verursachen kann. Löschen Sie die Ressourcengruppe, in der alle Ressourcen enthalten sind, um diese Kosten zu vermeiden.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Herzlichen Glückwunsch zum Absolvieren dieser exemplarische Vorgehensweise zum Bereitstellen von Python-Code in App Service für Linux!

Wie bereits erwähnt, können Sie mehr über die App Service-Erweiterung erfahren, indem Sie das zugehörige GitHub-Repository [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice) besuchen. Beschreibungen von Problemen und sonstige Beiträge sind ebenfalls willkommen.

Weitere Informationen zu Azure-Diensten, die Sie von Python aus verwenden können, einschließlich Datenspeicherung sowie KI und Machine Learning-Diensten, finden Sie unter [Azure für Python-Cloudentwickler](/python/azure/?view=azure-python).

Es gibt auch andere Azure-Erweiterungen für VS Code, die möglicherweise für Sie hilfreich sind. Suchen Sie einfach im Erweiterungen-Explorer nach „Azure“:

![Azure-Erweiterungen für Visual Studio Code](media/deploy-azure/azure-extensions-for-visual-studio-code.png)

Einige beliebte Erweiterungen sind:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure-Funktionen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [ARM-Tools (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Ich bin fertig!](/python/azure/?view=azure-python) 

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)