---
title: 'Tutorial: Bereitstellen von Docker-Containern in Azure App Service mit Visual Studio Code'
description: Schritt 1 im Tutorial, Konfigurieren Ihrer Umgebung für Container
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 447643662ef3c839823d81d29cfb55cc2ee442b2
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772607"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Tutorial: Bereitstellen von Docker-Containern in Azure App Service mit Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code ein Containerimage aus einer Containerregistrierung in [Azure App Service](/azure/app-service/) bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie die Feedbackschaltfläche **Diese Seite** am Ende jedes Artikels.

Ein entsprechendes Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=t79HDLC5kQA&feature=youtu.be&ocid=AID3006292" target="_blank">Django-Apps in VS Code-Entwicklungsontainern</a> (youtube.com).

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

- Ein [Azure-Konto](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- Ein geeigneter Container, der in eine Containerregistrierung hochgeladen wurde. Details zur Erstellung eines Containers mit einer Python-Web-App finden Sie unter [Python in Containern](https://code.visualstudio.com/docs/containers/quickstart-python).
- Die [Azure App Service-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- Die [Docker-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet: Fahren Sie mit Schritt 2 fort. >>>](tutorial-deploy-containers-02.md)
