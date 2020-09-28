---
title: 'Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code'
description: Schritt 1 im Tutorial, Konfigurieren Ihrer Umgebung für App Service
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: b35fc41707b31bec8e889d2b60becdad56f4e7d9
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2020
ms.locfileid: "90773073"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Python-Anwendung in Azure App Service für Linux mithilfe der [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Treten Probleme auf? Informieren Sie uns darüber.** am Ende jedes Artikels, um Feedback zu geben.

Ein Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Erstellen von WebApps mit VS Code und Azure App Service</a> (youtube.com).

> [!NOTE]
> Falls Sie Apps lieber über die CLI bereitstellen möchten, helfen Ihnen die Informationen unter **[Schnellstart: Erstellen einer Python-App in Azure App Service für Linux](/azure/app-service/quickstart-python)** weiter.

> [!TIP]
> [Azure App Service für Linux](/azure/app-service/overview#app-service-on-linux) führt den Quellcode in einem vordefinierten Docker-Container aus. Dieser Container führt Apps mit Python 3.7 mithilfe des [Gunicorn](https://gunicorn.org)-Webservers aus. Die Eigenschaften dieses Containers werden unter [Konfigurieren von Python-Apps für App Service unter Linux](/azure/app-service/configure-language-python) beschrieben. Die Containerdefinition selbst finden Sie unter [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

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

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
