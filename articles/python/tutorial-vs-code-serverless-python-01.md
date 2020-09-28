---
title: 'Tutorial: Erstellen und Bereitstellen einer serverlosen Azure Functions-Instanz in Python mit Visual Studio Code'
description: Schritt 1 im Tutorial, Konfigurieren Ihrer lokalen Umgebung für Azure Functions
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 8d7b3d29b1bd8860d87505fd6f3b09a20702f904
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772745"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Tutorial: Erstellen und Bereitstellen von Azure Functions serverlos in Python mit Visual Studio Code

In diesem Artikel verwenden Sie Visual Studio Code und die Azure Functions-Erweiterung, um einen serverlosen HTTP-Endpunkt mit Python zu erstellen und auch eine Verbindung (oder „Bindung“) mit dem (bzw. an den) Speicher hinzuzufügen.

Azure Functions führt Ihren Code in einer serverlosen Umgebung aus, ohne dass ein virtueller Computer bereitgestellt oder eine Web-App veröffentlicht werden muss. Die Azure Functions-Erweiterung für Visual Studio Code vereinfacht den Prozess der Verwendung von Funktionen erheblich, indem sie automatisch viele Konfigurationsprobleme löst.

Wenn Sie Probleme mit einem der Schritte in diesem Tutorial haben, würden wir uns über nähere Informationen freuen. Verwenden Sie die Feedbackschaltfläche **Diese Seite** am Ende jedes Artikels.

Ein Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=9bMsdBYy-D0&feature=youtu.be&ocid=AID3006292" target="_blank">Erstellen von Azure Functions mit VS Code</a> (youtube.com). Möglicherweise interessieren Sie sich auch für die längere Sitzung mit dem Titel <a href="https://www.youtube.com/watch?v=PV7iy6FPjAY&feature=youtu.be&t=13&ocid=AID3006292" target="_blank">Einfache Datenverarbeitung mit Azure Functions</a> (youtube.com). 

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

- Ein [Azure-Abonnement](#azure-subscription).
- Die [Azure Functions Core Tools](#azure-functions-core-tools).
- [Visual Studio Code mit der Azure Functions-Erweiterung](#visual-studio-code-python-and-the-azure-functions-extension)

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement haben, [registrieren Sie sich jetzt](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) für ein kostenloses 30-tägiges Konto mit 200 US-Dollar in Form einer Azure-Gutschrift, um eine beliebige Kombination von Diensten auszuprobieren.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Befolgen Sie zum Installieren von Azure Functions Core Tools die Anweisungen für Ihr Betriebssystem unter [Arbeiten mit Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2). Ignorieren Sie die Kommentare im Artikel zum Chocolatey-Paket-Manager, die für dieses Tutorial nicht erforderlich sind.

Verwenden Sie bei der Installation von Node.js die Standardoptionen, und wählen Sie *nicht* die Option zur automatischen Installation der erforderlichen Tools aus.  Verwenden Sie darüber hinaus unbedingt die Option `-g` mit den `npm install`-Befehlen, damit die Core Tools für nachfolgende Befehle verfügbar sind.

> [!TIP]
> Core Tools sind in .NET Core geschrieben, und das Core Tools-Paket wird am besten mit dem Node.js-Paket-Manager npm installiert, weshalb Sie derzeit auch für Azure Functions in Python .NET Core und Node.js installieren müssen. Sie können jedoch die .NET Core-Anforderung mithilfe von „Erweiterungsbundles“ umgehen, wie in der vorgenannten Dokumentation beschrieben. Wie auch immer, Sie müssen diese Komponenten nur einmal installieren, danach fordert Visual Studio Code Sie automatisch auf, alle Updates zu installieren.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python und die Azure Functions-Erweiterung

Installieren Sie folgende Software:

- Eine 64-Bit-Version von Python 3.6, 3.7 oder 3.8, wie für Azure Functions erforderlich. Installieren Sie Python von [python.org](https://www.python.org/downloads). Wählen Sie bei der Installation **Add Python 3.x to PATH** (Python 3.x zu PATH hinzufügen) aus, und verwenden Sie die Standardoptionen, indem Sie die Option **Jetzt installieren** auswählen. Wählen Sie unter Windows außerdem am Ende des Prozesses **Disable Path length limit** (Pfadlängenbeschränkung deaktivieren) aus.
- [Visual Studio Code](https://code.visualstudio.com/)
- Die [Python-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-python.python) wie in [Erste Schritte in Python mit Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) beschrieben.
- Die [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Allgemeine Informationen finden Sie im [vscode-azurefunctions-GitHub-Repository](https://github.com/Microsoft/vscode-azurefunctions).

    > [!NOTE]
    > Die Azure Functions-Erweiterung ist im [Azure-Tools-Erweiterungspaket](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) enthalten.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Nachdem Sie die Azure-Erweiterung installiert haben, melden Sie sich bei Ihrem Azure-Konto an, indem Sie zum **Azure**-Explorer navigieren, unter **Functions** die Option **Bei Azure anmelden** auswählen und dann den Anweisungen im Browser folgen.

![Anmelden bei Azure über VS Code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Vergewissern Sie sich nach der Anmeldung, dass auf der Statusleiste **Azure: Angemeldet** angezeigt wird und Sie Ihre Abonnements im **Azure-Explorer** sehen:

![Visual Studio Code-Statusleiste mit Azure-Konto](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

![Azure App Service-Explorer in Visual Studio Code mit Abonnements](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

[!INCLUDE [proxy-config](includes/proxy-config.md)]

### <a name="verify-your-environment"></a>Überprüfen der Umgebung

Um sicherzustellen, dass alle Azure Functions-Tools installiert sind, öffnen Sie die Visual Studio Code-Befehlspalette (**F1**), wählen Sie den Befehl **Terminal: Neues integriertes Terminal erstellen** aus, und führen Sie nach dem Öffnen des Terminals den Befehl `func` aus:

![Überprüfen der Voraussetzungen für Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

Die Ausgabe, die mit dem Azure Functions-Logo beginnt (Sie müssen die Ausgabe nach oben scrollen), gibt an, dass die Azure Functions Core Tools vorhanden sind.

Wird der Befehl `func` nicht erkannt, führen Sie `npm install -g azure-functions-core-tools` erneut aus, und vergewissern Sie sich, dass die Installation erfolgreich war. Verwenden Sie außerdem unbedingt den Switch `-g` mit dem Installationsbefehl. Andernfalls wird das Paket von npm nur im aktuellen Ordner installiert.

Der Befehl `func` durchläuft die Datei *func.cmd*, die im globalen Node.js-Ordner installiert ist. Wenn Sie den Speicherort dieses Ordners anzeigen möchten, führen Sie `npm -l` aus, und überprüfen Sie den Speicherort am Ende der Ausgabe.

> [!div class="nextstepaction"]
> [Ich habe mich bei Azure angemeldet: Fahren Sie mit Schritt 2 fort. >>>](tutorial-vs-code-serverless-python-02.md)
