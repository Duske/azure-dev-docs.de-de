---
title: Bereitstellen von Python-Apps in Azure App Service für Linux aus Visual Studio Code
description: 'Tutorialschritt 1: Einführung, Voraussetzungen und Anmeldung bei Azure'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 1b1e3d7fa1daa408584e4caf22c553d7f47bccea
ms.sourcegitcommit: d6575ac86449380b5a9c6c66aa722cb33ed53438
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186192"
---
# <a name="deploy-to-azure-app-service-on-linux"></a>Bereitstellen in Azure App Service für Linux

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Python-Anwendung in Azure App Service für Linux mithilfe der [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterung bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Ich bin auf ein Problem gestoßen** am Ende jedes Artikels, um Feedback zu geben.

> [!TIP]
> [Azure App Service für Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) steht derzeit als Vorschauversion für Python zur Verfügung. Bei diesem Dienst wird der Quellcode in einem vordefinierten Docker-Container ausgeführt. Dieser Container führt Apps mit Python 3.7 mithilfe des [Gunicorn](https://gunicorn.org)-Webservers aus. Die Eigenschaften dieses Containers werden unter [Konfigurieren von Python-Apps für App Service unter Linux](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python) beschrieben. Die Containerdefinition selbst finden Sie unter [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

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
