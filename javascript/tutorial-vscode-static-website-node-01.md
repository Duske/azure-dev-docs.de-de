---
title: Bereitstellen einer statischen Node.js-Website in Azure in Visual Studio Code
description: 'Teil 1 des Tutorials: Einführung und Voraussetzungen.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 45dfb1f32d98385b2cb944340b4601de804e133f
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685905"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Bereitstellen einer statischen Website in Azure in Visual Studio Code

In diesem Tutorial erstellen Sie mit [Azure Storage](https://docs.microsoft.com/azure/storage) eine statische Website und stellen sie in Azure bereit. Eine statische Website besteht aus HTML-, CSS-, JavaScript- und anderen statischen Dateien wie beispielsweise Bildern oder Schriftarten. In der Regel ist eine statische Website eine in Angular oder React geschriebene Single-Page-Webanwendung (Single-Page Application, [SPA](https://en.wikipedia.org/wiki/Single-page_application)). Sie entwerfen Ihre App jedoch so, dass diese Dateien direkt im *Speicher* und nicht mit einem Webserver gehostet und verarbeitet werden. Das Hosting im Speicher ist einfacher und kostengünstiger als die Wartung eines Webservers.

> [!NOTE]
> Wenn Sie eigenen Servercode haben (z. B. eine Node.js-/Express-App), führen Sie stattdessen das [App Service-Tutorial](tutorial-vscode-azure-app-service-node-01.md) durch.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- Die [Azure Storage-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager. (Diese werden nur zum Generieren eines Beispielprojekts verwendet. Sie müssen Node.js nicht installieren, wenn Sie bereits App-Code haben.)

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurestorage">Installieren der Azure Storage-Erweiterung</a>

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/en-us/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-static-website-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
