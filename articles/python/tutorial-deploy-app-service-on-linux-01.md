---
title: 'Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code'
description: Schritt 1 im Tutorial, Konfigurieren Ihrer Umgebung für App Service
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: bdbfd1ceaab01141bd32ec0c7d97309e2ce5e641
ms.sourcegitcommit: 1dfcc022a3098b1a1505e9458eada35f527ef070
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97636495"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Python-Anwendung in Azure App Service für Linux mithilfe der [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Treten Probleme auf? Informieren Sie uns darüber.** am Ende jedes Artikels, um Feedback zu geben.

Ein Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Erstellen von WebApps mit VS Code und Azure App Service</a> (youtube.com).

> [!NOTE]
> Falls Sie Apps lieber über die CLI bereitstellen möchten, helfen Ihnen die Informationen unter **[Schnellstart: Erstellen einer Python-App in Azure App Service für Linux](/azure/app-service/quickstart-python)** weiter.

> [!TIP]
> [Azure App Service für Linux](/azure/app-service/overview#app-service-on-linux) führt den Quellcode in einem vordefinierten Docker-Container aus. In diesem Container werden Apps mit Python 3.6 oder einer höheren Version unter Verwendung des [Gunicorn](https://gunicorn.org)-Webservers ausgeführt. Die Eigenschaften dieses Containers werden unter [Konfigurieren von Python-Apps für App Service unter Linux](/azure/app-service/configure-language-python) beschrieben. Die Containerdefinitionen finden Sie unter [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/).

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

- Falls Sie über kein Azure-Konto mit einem aktiven Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension).

- Vergewissern Sie sich, dass Sie über eine [lokale Installation von Python 3.7 oder 3.8](https://python.org/downloads) verfügen. Führen Sie den folgenden Befehl aus, um Ihre Version zu überprüfen:

    ```bash
    python --version
    ```

- Installieren Sie folgende Software:
  - [Visual Studio Code](https://code.visualstudio.com/)
  - Python und die [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-Erweiterung, wie im [VS Code-Tutorial zu Python unter Voraussetzungen](https://code.visualstudio.com/docs/python/python-tutorial) beschrieben
  - Die [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung, die die Interaktion mit Azure App Service innerhalb von VS Code ermöglicht. Allgemeine Informationen finden Sie im [Tutorial zur App Service-Erweiterung](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) und im [GitHub-Repository vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet: Fahren Sie mit Schritt 2 fort. >>>](tutorial-deploy-app-service-on-linux-02.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
