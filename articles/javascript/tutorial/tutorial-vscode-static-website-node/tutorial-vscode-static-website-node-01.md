---
title: Bereitstellen einer statischen Node.js-Website in Azure in Visual Studio Code
description: 'Teil 1 des Tutorials zu statischen Web-Apps: Einführung und Voraussetzungen'
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js
ms.openlocfilehash: a4280e3c28ee99e38eb4430c991450667277b29f
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609241"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Bereitstellen einer statischen Website in Azure in Visual Studio Code

In diesem Tutorial erstellen Sie mit [Azure Storage](/azure/storage) eine statische Website und stellen sie in Azure bereit. Eine statische Website besteht aus HTML-, CSS-, JavaScript- und anderen statischen Dateien wie beispielsweise Bildern oder Schriftarten. In der Regel ist eine statische Website eine in Angular, React oder Vue geschriebene Single-Page-Webanwendung (Single-Page Application, [SPA](https://en.wikipedia.org/wiki/Single-page_application)). Sie entwerfen Ihre App jedoch so, dass diese Dateien direkt im _Speicher_ und nicht mit einem Webserver gehostet und verarbeitet werden. Das Hosting im Speicher ist einfacher und kostengünstiger als die Wartung eines Webservers.

## <a name="walkthrough-video"></a>Video mit exemplarischer Vorgehensweise

Dieses Video enthält eine umfassende exemplarische Vorgehensweise für den Inhalt dieses Artikels.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player]

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/)
- Die [Azure Storage-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)
- [Node.js und npm](https://nodejs.org/en/download), Node.js-Paket-Manager (Diese werden nur zum Generieren eines Beispielprojekts verwendet. Sie müssen Node.js nicht installieren, wenn Sie bereits App-Code haben.)

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](../../includes/azure-sign-in.md)]

> [!div class="nextstepaction"] 
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-static-website-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
