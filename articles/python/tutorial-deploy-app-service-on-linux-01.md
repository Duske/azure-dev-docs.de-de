---
title: 'Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 1: Einführung, Voraussetzungen und Anmeldung bei Azure'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 88335c081375e0a3830de7676e137f5b1038a98c
ms.sourcegitcommit: 5051b25ad32be891800b23fc7ae12a4ca85cbb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88147401"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Python-Anwendung in Azure App Service für Linux mithilfe der [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Ich bin auf ein Problem gestoßen** am Ende jedes Artikels, um Feedback zu geben.

Ein Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Erstellen von WebApps mit VS Code und Azure App Service</a> (youtube.com).

> [!TIP]
> [Azure App Service für Linux](/azure/app-service/overview#app-service-on-linux) führt den Quellcode in einem vordefinierten Docker-Container aus. Dieser Container führt Apps mit Python 3.7 mithilfe des [Gunicorn](https://gunicorn.org)-Webservers aus. Die Eigenschaften dieses Containers werden unter [Konfigurieren von Python-Apps für App Service unter Linux](/azure/app-service/configure-language-python) beschrieben. Die Containerdefinition selbst finden Sie unter [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code mit der Azure App Service-Erweiterung](#visual-studio-code-python-and-the-azure-app-service-extension)
- Eine Python-Umgebung

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python und die Azure App Service-Erweiterung

Installieren Sie folgende Software:

- [Visual Studio Code](https://code.visualstudio.com/)
- Python und die [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)-Erweiterung, wie im [VS Code-Tutorial zu Python unter Voraussetzungen](https://code.visualstudio.com/docs/python/python-tutorial) beschrieben
- Die [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung, die die Interaktion mit Azure App Service innerhalb von VS Code ermöglicht. Allgemeine Informationen finden Sie im [Tutorial zur App Service-Erweiterung](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) und im [GitHub-Repository vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet: Fahren Sie mit Schritt 2 fort. >>>](tutorial-deploy-app-service-on-linux-02.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=01-verify-prerequisites)
