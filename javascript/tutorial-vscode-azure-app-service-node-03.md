---
title: Bereitstellen von Node.js-Apps in Azure App Service in Visual Studio Code
description: 'Teil 3 des Tutorials: Bereitstellen der Website'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 937eb54e9885e3b5b9fa7be54f551945a54572cd
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467200"
---
# <a name="deploy-the-website"></a>Bereitstellen der Website

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-azure-app-service-node-02.md)

In diesem Schritt stellen Sie Ihre Node.js-Website mithilfe von Visual Studio Code und der Azure App Service-Erweiterung bereit. In diesem Tutorial wird das einfachste Bereitstellungsmodell verwendet, bei dem Ihre App gezippt und in Azure App Service für Linux bereitgestellt wird.

1. Stellen Sie vor dem Bereitstellen der App sicher, dass sie an dem Port lauscht, der in der Umgebungsvariable `PORT` angegeben ist: `process.env.PORT`.

1. Starten Sie VS Code mit dem folgenden Befehl in Ihrem App-Ordner (z. B. `myExpressApp` aus dem vorherigen Schritt):

    ```bash
    code .
    ```

1. Klicken Sie in VS Code auf das Azure-Symbol, um den **Azure App Service**-Explorer zu öffnen, und anschließend auf den blauen Pfeil nach oben, um die App in Azure bereitzustellen:

    ![Bereitstellen in Web-App](media/deploy-azure/deploy.png)

    > [!TIP]
    > Alternativ können Sie die **Befehlspalette** öffnen (**F1**), „In Web-App bereitstellen“ eingeben und dann den Befehl **Azure App Service: Bereitstellen in Web-App** ausführen.

1. Geben Sie an den Eingabeaufforderungen Folgendes ein:

    a. Wählen Sie Ihren aktuellen Ordner für die App aus (`myExpressApp` in diesem Tutorial).

    a. Wählen Sie **Neue Web-App erstellen** aus.

    a. Geben Sie einen global eindeutigen Namen für Ihre App ein, und drücken Sie die **EINGABETASTE**. Gültige Zeichen für den Namen einer App sind die Buchstaben a-z, die Ziffern 0-9 und der Bindestrich (-).

    a. Wählen Sie einen Standort in einer Azure-Region in Ihrer Nähe oder in der Nähe anderer Dienste aus, auf die Sie möglicherweise zugreifen müssen (siehe [Regionen](https://azure.microsoft.com/regions/)).

    a. Wählen Sie Ihre **Node.js-Version** aus. Empfohlen wird LTS.

1. Während die Erweiterung Ihre App erstellt, wird der Fortschritt in VS Code im Fenster **Ausgabe** angezeigt (möglicherweise müssen Sie die Ausgabe **Azure App Service** auswählen).

    ![Visual Studio Code-Ausgabefenster für Azure App Service](media/deploy-azure/output-window.png)

1. Wählen Sie **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Zielserver zu aktualisieren. Ihre App wird anschließend bereitgestellt.

    ![Konfigurierte Bereitstellung](media/deploy-azure/server-build.png)

1. Beim Start der Bereitstellung werden Sie aufgefordert, Ihren Arbeitsbereich zu aktualisieren, sodass alle späteren Bereitstellungen automatisch die gleiche App Service-Web-App als Ziel verwenden. Klicken Sie auf **Ja**, um sicherzustellen, dass Ihre Änderungen für die richtige App bereitgestellt werden.

    ![Konfigurierte Bereitstellung](media/deploy-azure/save-configuration.png)

1. Wählen Sie nach Abschluss der Bereitstellung an der Eingabeaufforderung **Website durchsuchen** aus, um Ihre neu bereitgestellte Website anzuzeigen.

## <a name="troubleshooting"></a>Problembehandlung

Wenn der Fehler „Sie sind nicht berechtigt, dieses Verzeichnis oder diese Seite anzuzeigen“ angezeigt wird, wurde die Anwendung wahrscheinlich nicht korrekt gestartet. Zeigen Sie wie im [nächsten Schritt](tutorial-vscode-azure-app-service-node-04.md) beschrieben die Protokolle an, um das Problem zu diagnostizieren und zu beheben. Wenn sich das Problem nicht beheben lässt, wenden Sie sich an uns, indem Sie unten auf die Schaltfläche **Ich bin auf ein Problem gestoßen** klicken. Wir helfen Ihnen gerne weiter.

## <a name="updating-the-website"></a>Aktualisieren der Website

Sie können Änderungen an dieser App bereitstellen, indem Sie den gleichen Prozess ausführen und die vorhandene App auswählen anstatt eine neue zu erstellen.

----

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt.](tutorial-vscode-azure-app-service-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=deploy-app)
