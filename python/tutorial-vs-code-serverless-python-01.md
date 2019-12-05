---
title: 'Tutorial: Erstellen und Bereitstellen von Azure Functions serverlos in Python mit Visual Studio Code'
description: 'Tutorialschritt 1: Einführung und Voraussetzungen'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: e6076bf9760a2f5b5e38622693b9a23561d3c964
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467014"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Tutorial: Erstellen und Bereitstellen von Azure Functions serverlos in Python mit Visual Studio Code

In diesem Artikel verwenden Sie Visual Studio Code und die Azure Functions-Erweiterung, um einen serverlosen HTTP-Endpunkt mit Python zu erstellen und auch eine Verbindung (oder „Bindung“) mit dem (bzw. an den) Speicher hinzuzufügen.

Azure Functions führt Ihren Code in einer serverlosen Umgebung aus, ohne dass ein virtueller Computer bereitgestellt oder eine Web-App veröffentlicht werden muss. Die Azure Functions-Erweiterung für Visual Studio Code vereinfacht den Prozess der Verwendung von Funktionen erheblich, indem sie automatisch viele Konfigurationsprobleme löst.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie die Schaltfläche **Ich bin auf ein Problem gestoßen** am Ende jedes Artikels, um Feedback zu geben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein [Azure-Abonnement](#azure-subscription).
- [Visual Studio Code mit der Azure Functions-Erweiterung](#visual-studio-code-python-and-the-azure-functions-extension)
- Die [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) für ein kostenloses 30-tägiges Konto mit 200 US-Dollar in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python und die Azure Functions-Erweiterung

Installieren Sie folgende Software:

- Python 3.7 oder Python 3.6 gemäß den Anforderungen von Azure Functions. Die neuesten kompatiblen Versionen sind [Python 3.7.5](https://www.python.org/downloads/release/python-375/) und [Python 3.6.8](https://www.python.org/downloads/release/python-368/).
- [Visual Studio Code](https://code.visualstudio.com/).
- Die [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python) wie in [Erste Schritte in Python mit Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) beschrieben.
- Die [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Allgemeine Informationen finden Sie im [vscode-azurefunctions-GitHub-Repository](https://github.com/Microsoft/vscode-azurefunctions).

> [!NOTE]
> Die Azure Functions-Erweiterung ist im [Azure-Tools-Erweiterungspaket](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) enthalten.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Befolgen Sie die Anweisungen für Ihr Betriebssystem unter [Arbeiten mit Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2). Die Tools selbst sind in .NET Core geschrieben, und das Core Tools-Paket wird am besten mit dem Node.js-Paketmanager npm installiert, weshalb Sie derzeit auch für Python-Code .NET Core und Node.js installieren müssen. Sie können jedoch die .NET Core-Anforderung mithilfe von „Erweiterungsbundles“ umgehen, wie in der vorgenannten Dokumentation beschrieben. Wie auch immer, Sie müssen diese Komponenten nur einmal installieren, danach fordert Visual Studio Code Sie automatisch auf, alle Updates zu installieren.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen

Um sicherzustellen, dass alle Azure Functions-Tools installiert sind, öffnen Sie die Visual Studio Code-Befehlspalette (**F1**), wählen Sie den Befehl **Terminal: Neues integriertes Terminal erstellen** aus, und führen Sie nach dem Öffnen des Terminals den Befehl `func` aus:

![Überprüfen der Voraussetzungen für Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

Die Ausgabe, die mit dem Azure Functions-Logo beginnt (Sie müssen die Ausgabe nach oben scrollen), gibt an, dass die Azure Functions Core Tools vorhanden sind.

Wenn der `func`-Befehl nicht erkannt wird, vergewissern Sie sich, dass der Ordner, in dem Sie die Azure Functions Core Tools installiert haben, in der PATH-Umgebungsvariablen enthalten ist.

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet.](tutorial-vs-code-serverless-python-02.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
