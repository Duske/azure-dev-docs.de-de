---
title: Bereitstellen der Functions-App in der Azure-Cloud
description: Verwenden Sie die Visual Studio Code-Erweiterung für Azure Functions, um die Functions-App in der Azure-Cloud bereitzustellen. Überprüfen Sie mit einem Browser, ob die Functions-App öffentlich verfügbar ist.
ms.topic: tutorial
ms.date: 09/23/2019
ms.custom: devx-track-js, contperfq2
ms.openlocfilehash: 4d000d1f8f8b8cf05f6387fb698d4bf2e2e9cdc5
ms.sourcegitcommit: 801682d3fc9651bf95d44e58574d5a4564be6feb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338553"
---
# <a name="4-deploy-the-functions-app-to-azure-cloud"></a>4. Bereitstellen der Functions-App in der Azure-Cloud

[Vorheriger Schritt: Lokales Testen der Funktion](tutorial-vscode-serverless-node-test-local.md)

In diesem Schritt verwenden Sie die Visual Studio Code-Erweiterung für Azure Functions, um die Functions-App in der Azure-Cloud bereitzustellen. Überprüfen Sie mit einem Browser, ob die Functions-App öffentlich verfügbar ist. 

## <a name="use-visual-studio-code-extension-to-deploy-to-hosting-environment"></a>Verwenden der Visual Studio Code-Erweiterung zur Bereitstellung in der Hostingumgebung

1. Klicken Sie in VS Code auf das Azure-Logo, um den **Azure Explorer** zu öffnen, und anschließend unter **Funktionen** auf den blauen Pfeil nach oben, um Ihre App bereitzustellen:

    ![Befehl zum Bereitstellen in Azure Functions](../media/functions-extension/deploy-app.png)

    Alternativ können Sie Ihre App bereitstellen, indem Sie die **Befehlspalette** öffnen (**F1**) und „in Funktions-App bereitstellen" eingeben. Wählen Sie dann den Befehl **Azure Functions: Für Funktions-App** bereitstellen aus.

1. Wählen Sie an der Eingabeaufforderung **Funktions-App in Azure auswählen** die Option **Neue Funktions-App in Azure erstellen** aus.

1. Geben Sie an der nächsten Eingabeaufforderung einen global eindeutigen Namen für Ihre Funktions-App ein, und drücken Sie die **EINGABETASTE**. Gültige Zeichen für die Namen von Funktions-Apps sind die Buchstaben a – z, die Ziffern 0 – 9 und der Bindestrich (-).

1. Wählen Sie die Node.js-Version/-Runtime aus.

    ![VS Code-Ausgabepanel mit Node.js-Version/-Runtime](../media/functions-extension/nodejs-runtime-version.png)

1. Wählen Sie an der nächsten Eingabeaufforderung eine Azure-[Region](https://azure.microsoft.com/regions/) in Ihrer Nähe aus.

1. Im Bereich **Ausgabe** von VS Code für **Azure Functions** wird der Fortschritt angezeigt:

    ![VS Code-Ausgabebereich mit dem Fortschritt der Bereitstellung](../media/functions-extension/deploy-progress.png)

## <a name="verify-functions-app-is-publicly-available-with-browser"></a>Überprüfen mit einem Browser, ob die Functions-App öffentlich verfügbar ist

1. Wechseln Sie nach Abschluss der Bereitstellung zum **Azure Functions**-Explorer, erweitern Sie den Knoten für Ihr Azure-Abonnement, dann den Knoten für Ihre Funktions-App und anschließend **Funktionen (schreibgeschützt)** . Klicken Sie mit der rechten Maustaste auf den Funktionsnamen, und wählen Sie **Funktions-URL kopieren** aus:

    ![Befehl „Funktions-URL kopieren“](../media/functions-extension/copy-function-url-command.png)

1. Fügen Sie die URL in einen Browser ein, und fügen Sie ein Argument `?name=<yourname>` an. Im Browser sollte dann die in der Cloud ausgeführte Funktion angezeigt werden:

    ![In der Cloud ausgeführte Funktion](../media/functions-extension/remote-test-browser.png)

1. Wenn Sie möchten, können Sie in *index.js* Änderungen am Funktionscode vornehmen oder weitere Funktionen mit anderen Triggern hinzufügen. Nachdem Sie den Code lokal getestet haben, stellen Sie ihn wie in den vorherigen Schritten beschrieben erneut bereit, um die Änderungen in der Cloud zu testen.

    > [!TIP]
    > Bei der Bereitstellung wird die gesamte Funktions-App bereitgestellt, sodass die Änderungen aller einzelnen Funktionen gleichzeitig bereitgestellt werden.

> [!div class="nextstepaction"]
> [Ich habe die Funktions-App bereitgestellt.](tutorial-vscode-serverless-node-remove-resource.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=deploy-app)
