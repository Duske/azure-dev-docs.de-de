---
title: Bereitstellen von Node.js-Apps in Azure App Service mit der Azure-Befehlszeilenschnittstelle
description: 'Teil 1 des Azure CLI-Tutorials: Einführung und Voraussetzungen'
ms.topic: tutorial
ms.date: 09/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 4ca11ffff1889d69ef8a1ee7fbca991b714f2163
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91365203"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Bereitstellen in Azure App Service mit der Azure-Befehlszeilenschnittstelle

In diesem Tutorial stellen Sie eine Node.js-Anwendung mithilfe der von allen Betriebssystemen unterstützten [Azure-Befehlszeilenschnittstelle (Azure CLI) ](/cli/azure/overview?view=azure-cli-latest) in Azure App Service bereit. Mit der CLI können Sie Azure-Ressourcen erstellen, eine Bereitstellungspipeline zwischen einem Git-Repository und Azure einrichten und die `console.log`-Ausgabe der App anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Node.js und npm 6.x oder höher](https://nodejs.org/en/download), Node.js-Paket-Manager
- [Git](https://git-scm.com/downloads) (danach sollte der Befehl `git --version` eine Versionsnummer anzeigen)
- Die [Azure CLI](/cli/azure/install-azure-cli)

Alternativ können Sie die [Azure CLI-Erweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli) verwenden, die beim Schreiben von Azure CLI-Skripts Syntax farbig hervorhebt und IntelliSense (Vervollständigungen) sowie Codeausschnitte bietet.

Eine zweite Alternative ist [Azure Cloud Shell](/azure/cloud-shell/overview), ein Dienst, den Sie in Visual Studio Code mithilfe der [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) verwenden können.

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git) für ein kostenloses Konto mit 200 USD in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="sign-in-to-the-azure-cli"></a>Melden Sie sich bei der Azure-Befehlszeilenschnittstelle an.

Führen Sie nach der Installation der Azure CLI den folgenden Befehl an einem Terminal oder einer Eingabeaufforderung aus:

```azurecli
az login
```

Der Befehl öffnet ein Browserfenster, in dem Sie zur Anmeldung bei Azure aufgefordert werden. Nachdem Sie sich angemeldet haben, wird im Terminalfenster eine JSON-Ausgabe mit Details zu Ihrem Abonnement angezeigt.

## <a name="check-npm-version"></a>Überprüfen der npm-Version

Wenn Sie Node.js bereits installiert haben, führen Sie den Befehl `node -v` aus, und vergewissern Sie sich, dass die Version 10.x oder höher ist. Wenn Sie eine ältere Version haben, [aktualisieren](https://nodejs.org/en/download/) Sie auf die aktuelle LTS-Version (Long Term Support = langfristige Unterstützung).

> [!div class="nextstepaction"]
> [Ich habe die Voraussetzungen installiert.](tutorial-vscode-azure-cli-node-02.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)