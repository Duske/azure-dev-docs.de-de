---
title: Bereitstellen von Node.js-Apps in Azure App Service mit der Azure-Befehlszeilenschnittstelle
description: 'Teil 1 des Azure CLI-Tutorials: Einführung und Voraussetzungen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 87dfa69c31eabffe12cd579127bfdf8b74a333e7
ms.sourcegitcommit: 3536f174735cd3bb7da7e4b266fbf43349a22b67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103193479"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Bereitstellen in Azure App Service mit der Azure-Befehlszeilenschnittstelle

In diesem Tutorial stellen Sie eine Node.js-Anwendung mithilfe der von allen Betriebssystemen unterstützten [Azure-Befehlszeilenschnittstelle (Azure CLI) ](/cli/azure/overview) in Azure App Service bereit. Mit der CLI können Sie Azure-Ressourcen erstellen, eine Bereitstellungspipeline zwischen einem Git-Repository und Azure einrichten und die `console.log`-Ausgabe der App anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Node.js](https://nodejs.org/en/download), der Node.js-Paket-Manager
- [Git](https://git-scm.com/downloads) (danach sollte der Befehl `git --version` eine Versionsnummer anzeigen)
[!INCLUDE [Azure CLI](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="sign-in-to-azure-with-azure-cli"></a>Anmelden bei Azure mit der Azure CLI

[!INCLUDE [Sign in ](../../../azure-cli/includes/interactive-login.md)]

## <a name="check-npm-version"></a>Überprüfen der npm-Version

Wenn Sie Node.js bereits installiert haben, führen Sie den Befehl `node -v` aus, und vergewissern Sie sich, dass die Version 10.x oder höher ist. Wenn Sie eine ältere Version haben, [aktualisieren](https://nodejs.org/en/download/) Sie auf die aktuelle LTS-Version (Long Term Support = langfristige Unterstützung).

> [!div class="nextstepaction"]
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-azure-cli-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)
