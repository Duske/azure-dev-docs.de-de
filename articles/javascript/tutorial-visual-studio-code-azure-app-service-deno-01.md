---
title: Bereitstellen von Deno-Apps in Azure App Service über die Azure CLI
description: 'Teil 1 des Deno-Tutorials: Einführung und Voraussetzungen'
ms.topic: tutorial
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: e4c521edd2f23576842d90979813f96a1812f0ec
ms.sourcegitcommit: 4dd392ea864be52421d0239e59198bc44b0a5a16
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91364943"
---
# <a name="deploy-deno-to-azure-app-service-using-visual-studio-code"></a>Bereitstellen von Deno in Azure App Service mithilfe von Visual Studio Code

In diesem Tutorial stellen Sie eine Deno-Anwendung mithilfe der Azure CLI für Azure App Service (unter Linux oder Windows) bereit.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. [Kostenloses Konto erstellen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Deno](https://deno.land/#installation)
- Installation der Azure CLI und Anmeldung

## <a name="install-or-run-in-azure-cloud-shell"></a>Installieren oder Ausführen in Azure Cloud Shell

Am einfachsten können Sie die Azure-Befehlszeilenschnittstelle in einer Azure Cloud Shell-Umgebung über Ihren Browser verwenden. Informationen zu Cloud Shell finden Sie unter [Schnellstart für Bash in Azure Cloud Shell](/azure/cloud-shell/quickstart).

Wenn Sie zur Installation der Befehlszeilenschnittstelle bereit sind, lesen Sie die [Installationsanweisungen](/cli/azure/install-azure-cli).

Führen Sie nach der Erstinstallation der CLI `az --version` aus, um zu überprüfen, ob sie installiert wurde und die korrekte Version aufweist.

> [!NOTE]
> [Installieren Sie die klassische Azure CLI](/cli/azure/install-classic-cli), wenn Sie das klassische Azure-Bereitstellungsmodell verwenden.

## <a name="sign-in"></a>Anmelden

Bevor Sie CLI-Befehle mit einer lokalen Installation verwenden, müssen Sie sich mithilfe von [az login](/cli/azure/reference-index#az-login) anmelden.

[!INCLUDE [interactive-login](../azure-cli/includes/interactive-login.md)]

Nach der Anmeldung sehen Sie eine Liste der mit Ihrem Azure-Konto verknüpften Abonnements. Die Abonnementinformationen mit `isDefault: true` ist das momentan aktivierte Abonnement nach der Anmeldung. Zur Auswahl eines anderen Abonnements verwenden Sie den Befehl [az account set](/cli/azure/account#az-account-set) mit der ID des Abonnements, zu dem Sie wechseln möchten. Weitere Informationen zur Abonnementauswahl finden Sie unter [Verwenden mehrerer Azure-Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli).

> [!div class="nextstepaction"]
> [Ich habe die Azure CLI installiert und mich angemeldet](tutorial-visual-studio-code-azure-app-service-deno-02.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=getting-started)

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
