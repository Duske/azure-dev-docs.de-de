---
title: 'Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 1: Einführung, Voraussetzungen und Anmeldung bei Azure'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: d5eed3d2b1aeea92b3681ada006b3723e67f70c4
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466277"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Python-Anwendung in Azure App Service für Linux mithilfe der [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Ich bin auf ein Problem gestoßen** am Ende jedes Artikels, um Feedback zu geben.

> [!TIP]
> [Azure App Service für Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) führt den Quellcode in einem vordefinierten Docker-Container aus. Dieser Container führt Apps mit Python 3.7 mithilfe des [Gunicorn](https://gunicorn.org)-Webservers aus. Die Eigenschaften dieses Containers werden unter [Konfigurieren von Python-Apps für App Service unter Linux](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python) beschrieben. Die Containerdefinition selbst finden Sie unter [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code mit der Azure App Service-Erweiterung](#visual-studio-code-python-and-the-azure-app-service-extension)
- Eine Python-Umgebung

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python und die Azure App Service-Erweiterung

Installieren Sie folgende Software:

- [Visual Studio Code](https://code.visualstudio.com/).
- Python und die [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-Erweiterung, wie im [VS Code-Tutorial zu Python unter Voraussetzungen](https://code.visualstudio.com/docs/python/python-tutorial) beschrieben
- Die [Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice)-Erweiterung, die die Interaktion mit Azure App Service innerhalb von VS Code ermöglicht. Allgemeine Informationen finden Sie im [Tutorial zur App Service-Erweiterung](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) und im [GitHub-Repository vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet.](tutorial-deploy-app-service-on-linux-02.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=01-verify-prerequisites)
