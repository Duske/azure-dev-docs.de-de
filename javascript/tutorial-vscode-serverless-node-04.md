---
title: Bereitstellen der Azure Functions-Anwendung in Visual Studio Code
description: 'Teil 4 des Tutorials: Bereitstellen der Funktions-App in der Cloud.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: kraigb
ms.openlocfilehash: 53d0dd11567084d42de71a0f737cf8b9f5fc5249
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685924"
---
# <a name="deploy-the-functions-app"></a>Bereitstellen der Funktions-App

[Vorheriger Schritt: Lokales Testen der Funktion](tutorial-vscode-serverless-node-03.md)

1. Klicken Sie in VS Code auf das Azure-Logo, um den **Azure Explorer** zu öffnen, und anschließend unter **Funktionen** auf den blauen Pfeil nach oben, um Ihre App bereitzustellen:

    ![Befehl zum Bereitstellen in Azure Functions](media/functions-extension/deploy-app.png)

    Alternativ können Sie Ihre App bereitstellen, indem Sie die **Befehlspalette** öffnen (**F1**) und „in Funktions-App bereitstellen" eingeben. Wählen Sie dann den Befehl **Azure Functions: Für Funktions-App** bereitstellen aus.

1. Wählen Sie an der Eingabeaufforderung**Funktions-App in Azure auswählen** die Option**Neue Funktions-App in Azure erstellen** aus.

1. Geben Sie an der nächsten Eingabeaufforderung einen global eindeutigen Namen für Ihre Funktions-App ein, und drücken Sie die **EINGABETASTE**. Gültige Zeichen für die Namen von Funktions-Apps sind die Buchstaben a – z, die Ziffern 0 – 9 und der Bindestrich (-).

1. Wählen Sie an der nächsten Eingabeaufforderung eine Azure-[Region](https://azure.microsoft.com/en-us/regions/) in Ihrer Nähe aus.

1. Im Bereich **Ausgabe** von VS Code für **Azure Functions** wird der Fortschritt angezeigt:

    ![VS Code-Ausgabebereich mit dem Fortschritt der Bereitstellung](media/functions-extension/deploy-progress.png)

1. Wechseln Sie nach Abschluss der Bereitstellung zum **Azure Functions**-Explorer, erweitern Sie den Knoten für Ihr Azure-Abonnement, dann den Knoten für Ihre Funktions-App und anschließend **Funktionen (schreibgeschützt)** . Klicken Sie mit der rechten Maustaste auf den Funktionsnamen, und wählen Sie **Funktions-URL kopieren** aus:

    ![Befehl „Funktions-URL kopieren“](media/functions-extension/copy-function-url-command.png)

1. Fügen Sie die URL in einen Browser ein, und fügen Sie ein Argument `?name=<yourname>` an. Im Browser sollte dann die in der Cloud ausgeführte Funktion angezeigt werden:

    ![In der Cloud ausgeführte Funktion](media/functions-extension/remote-test-browser.png)

1. Wenn Sie möchten, können Sie in *index.js* Änderungen am Funktionscode vornehmen oder weitere Funktionen mit anderen Triggern hinzufügen. Nachdem Sie den Code lokal getestet haben, stellen Sie ihn wie in den vorherigen Schritten beschrieben erneut bereit, um die Änderungen in der Cloud zu testen.

    > [!TIP]
    > Bei der Bereitstellung wird die gesamte Funktions-App bereitgestellt, sodass die Änderungen aller einzelnen Funktionen gleichzeitig bereitgestellt werden.

> [!div class="nextstepaction"]
> [Ich habe die Funktions-App bereitgestellt.](tutorial-vscode-serverless-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=deploy-app)
