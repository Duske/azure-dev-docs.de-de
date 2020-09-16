---
title: 'Tutorial: Bereitstellen von Docker-Containern in Azure App Service mit Visual Studio Code'
description: 'Tutorial: Schritt 1, Verwenden von Containern, Einführung und Voraussetzungen.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: f0fb983a596ca1828809d1d829af5517e8af66df
ms.sourcegitcommit: 9e282fc2ec967bee181c3034e7e70b28ae308905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473555"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Tutorial: Bereitstellen von Docker-Containern in Azure App Service mit Visual Studio Code

In diesem Artikel erfahren Sie, wie Sie mit Visual Studio Code ein Containerimage aus einer Containerregistrierung in [Azure App Service](/azure/app-service/) bereitstellen.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie den Link **Ich bin auf ein Problem gestoßen** am Ende jedes Artikels, um Feedback zu geben.

Ein entsprechendes Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=t79HDLC5kQA&feature=youtu.be&ocid=AID3006292" target="_blank">Django-Apps in VS Code-Entwicklungsontainern</a> (youtube.com).

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Konto](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- Ein geeigneter Container, der in eine Containerregistrierung hochgeladen wurde. Details zur Erstellung eines Containers mit einer Python-Web-App finden Sie unter [Python in Containern](https://code.visualstudio.com/docs/containers/quickstart-python).
- Die [Azure App Service-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- Die [Docker-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet: Fahren Sie mit Schritt 2 fort. >>>](tutorial-deploy-containers-02.md)

Sie haben Probleme? Übermitteln Sie ein GitHub-Problem, indem Sie unten auf dieser Seite die Feedbackoption „Diese Seite“ verwenden.
