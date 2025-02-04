---
title: Bereitstellen einer Node.js-/Express.js-App in App Service aus Visual Studio Code
description: Stellen Sie mit der App Service-Erweiterung für Visual Studio Code eine Express.js-/Node.js-Webanwendung in Azure App Service bereit.
ms.topic: tutorial
ms.date: 01/20/2021
ms.custom: devx-track-js
ms.openlocfilehash: 10b3462abcb466cf4e4ec57909f36e08dd50d77c
ms.sourcegitcommit: 3536f174735cd3bb7da7e4b266fbf43349a22b67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103193549"
---
# <a name="deploy-nodejs-expressjs-to-azure-app-service-using-visual-studio-code"></a>Bereitstellen einer Node.js-/Express.js-App in Azure App Service mit Visual Studio Code

Stellen Sie eine Node.js-/Express.js-Anwendung mit der [App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code in Azure App Service (unter Linux oder Windows) bereit.

Stellen Sie Ihre Node.js-App mithilfe von Git und der Azure App Service-Erweiterung in Azure bereit. So erreichen Sie dieses Ziel:

* Erstellen Sie die Express.js-App.
* Initialisieren Sie ein lokales Git-Repository.
* Erstellen Sie eine Web-App-Ressource zum Hosten der App.
* Stellen Sie die App in der Ressource bereit.
* Zeigen Sie die Remoteprotokolle lokal an.

## <a name="1-set-up-your-development-environment"></a>1. Einrichten der Entwicklungsumgebung

- Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für VS Code (installiert aus VS Code)
- [Node.js 12 und höher sowie npm](https://nodejs.org/en/download)

## <a name="2-sign-in-to-azure"></a>2. Anmelden bei Azure

[!INCLUDE [azure-sign-in](../includes/azure-sign-in.md)]

## <a name="3-create-a-local-expressjs-app"></a>3. Erstellen einer lokalen Express.js-App

[!INCLUDE [Create a local Express.js app](../includes/create-node-app.md)]

## <a name="4-run-your-local-expressjs-app"></a>4. Ausführen Ihrer lokalen Express.js-App

[!INCLUDE [Run your local Express.js app](../includes/run-node-app.md)]

## <a name="5-initialize-git-in-visual-studio-code-for-current-app"></a>5. Initialisieren von Git in Visual Studio Code für die aktuelle App

1. Stellen Sie im Terminal sicher, dass Sie sich im Ordner *expressApp1* befinden, und starten Sie Visual Studio Code dann mit dem folgenden Befehl:

    ```bash
    code .
    ```

1. Klicken Sie in Visual Studio Code auf das Symbol für die Quellcodeverwaltung, um den Explorer für die **Quellcodeverwaltung** zu öffnen. Klicken Sie anschließend auf **Repository initialisieren**, um ein lokales Git-Repository zu initialisieren:

    ![Initialisieren eines Git-Repositorys](../media/deploy-azure/git-init.png)

1. Wählen Sie bei entsprechender Aufforderung *expressApp1* als Arbeitsbereichsordner aus.

1. Geben Sie nach der Initialisierung des Repositorys die Meldung „Initial commit“ (Erster Commit) ein, und wählen Sie das Häkchen aus, um den ersten Commit Ihrer Quelldateien zu erstellen.

    ![Durchführen eines ersten Commits für das Repository](../media/deploy-azure/initial-commit.png)

## <a name="6-create-app-service-resource-in-visual-studio-code"></a>6. Erstellen der App Service-Ressource in Visual Studio Code

1. Geben Sie in der Befehlspalette (**STRG**+**UMSCHALT**+**P**) den Befehl „create web“ein, und wählen Sie **Azure App Service: Create New Web App...Advanced** (Azure App Service: Neue Web-App erstellen...Erweitert) aus. Sie verwenden den erweiterten Befehl anstelle der Linux-Standardeinstellungen, um die vollständige Kontrolle über die Bereitstellung zu haben, einschließlich Ressourcengruppe, App Service-Plan und Betriebssystem.

1. Gehen Sie bei den Aufforderungen wie folgt vor:

    - Wählen Sie Ihr **Abonnementkonto** aus.
    - Geben Sie für **Geben Sie einen global eindeutigen Namen ein** einen Namen ein, der in Azure eindeutig ist. Verwenden Sie nur alphanumerische Zeichen („A - Z“, „a - z“ und „0 - 9“) und Bindestriche („-“).
    - Wählen Sie **Neue Ressourcengruppe erstellen** aus, und geben Sie einen Namen ein, z. B. `AppServiceTutorial-rg`.
    - Wählen Sie ein Betriebssystem (Windows oder Linux) aus.
    - Nur für Linux: Wählen Sie eine Node.js-Version aus. (Für Windows legen Sie die Version später über eine App-Einstellung fest.)
    - Klicken Sie auf **Einen neuen App Services-Plan erstellen**, geben Sie einen Namen wie `AppServiceTutorial-plan` an, und wählen Sie dann den [Tarif](../core/what-is-azure-for-javascript-development.md#free-tier-resources) **F1 Free** aus.
    - Wählen Sie für die Application Insights-Ressource die Option **Vorerst überspringen** aus.
    - Wählen Sie einen Ort in Ihrer Nähe aus.

1. Sie werden von VS Code nach kurzer Zeit darüber benachrichtigt, dass die Erstellung abgeschlossen ist. Schließen Sie die Benachrichtigung mit der Schaltfläche **X**:

    ![Benachrichtigung nach Abschluss der Web-App-Erstellung](../media/deploy-azure/creation-complete.png)

1. Nachdem Sie die Web-App eingerichtet haben, weisen Sie VS Code als Nächstes an, Ihren Code aus dem lokalen Git-Repository bereitzustellen. Wählen Sie das Azure-Symbol aus, um den **Azure App Service**-Explorer zu öffnen, erweitern Sie Ihren Abonnementknoten, klicken Sie mit der rechten Maustaste auf den Namen der gerade erstellten Web-App, und wählen Sie **Bereitstellungsquelle konfigurieren** aus.

    ![Befehl zum Konfigurieren der Bereitstellungsquelle in einer Web-App](../media/deploy-azure/configure-deployment-source.png)

1. Wählen Sie bei entsprechender Aufforderung **LocalGit** aus.

1. Bei einer Bereitstellung für App Service unter Windows müssen Sie vorher zwei Einstellungen erstellen:

    1. Erweitern Sie in VS Code den Knoten für den neuen App-Dienst, klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen**, und wählen Sie anschließend **Neue Einstellung hinzufügen** aus:

        ![Hinzufügen eines Befehls für eine App-Einstellung](../media/deploy-azure/add-setting.png)

    1. Geben Sie `WEBSITE_NODE_DEFAULT_VERSION` als Einstellungsschlüssel und `10.15.2` als Einstellungswert ein. Mit dieser Einstellung wird die Node.js-Version festgelegt.
    1. Wiederholen Sie den Prozess, um einen Schlüssel für `SCM_DO_BUILD_DURING_DEPLOYMENT` mit dem Wert `1` zu erstellen. Mit dieser Einstellung wird erzwungen, dass der Server bei der Bereitstellung `npm install` ausführt.
    1. Erweitern Sie den Knoten **Anwendungseinstellungen**, um sich zu vergewissern, dass die Einstellungen vorhanden sind.

1. Wählen Sie das Symbol mit dem blauen Pfeil nach oben aus, um Ihren Code in Azure bereitzustellen:

    ![Symbol „In Web-App bereitstellen“](../media/deploy-azure/deploy.png)

1. Wählen Sie für die Aufforderungen den Ordner *expressApp1*, dann erneut das **Abonnementkonto** und anschließend den Namen der Web-App aus, die Sie weiter oben erstellt haben.

1. Wählen Sie bei der Bereitstellung unter Linux **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Zielserver zu aktualisieren.

    ![Aufforderung zum Aktualisieren der Konfiguration auf dem Linux-Zielserver](../media/deploy-azure/server-build.png)

1. Wählen Sie unter Linux und Windows **Ja** aus, wenn **Always deploy the workspace „nodejs-docs-hello-world“ to (app name)"** (Arbeitsbereich „nodejs-docs-hello-world“ immer in (App-Name) bereitstellen) angezeigt wird. Mit der Auswahl von **Ja** wird VS Code angewiesen, für nachfolgende Bereitstellungen automatisch dieselbe App Service-Web-App zu verwenden.

1. Wählen Sie nach Abschluss der Bereitstellung in der Aufforderung die Option **Website durchsuchen** aus, um Ihre neu bereitgestellte Web-App anzuzeigen. Im Browser sollte „Hello World!“ angezeigt werden.

1. (Optional): Sie können Ihre Codedateien ändern und dann die Schaltfläche für die Bereitstellung erneut verwenden, um die Web-App zu aktualisieren.

## <a name="7-stream-remote-service-logs-in-visual-studio-code"></a>7. Streamen von Remotedienstprotokollen in Visual Studio Code

Zeigen Sie mithilfe von Aufrufen von `console.log` eine beliebige Ausgabe an, die die ausgeführte App generiert. Diese Ausgabe wird im Fenster **Ausgabe** in Visual Studio Code angezeigt.

1. Klicken Sie im **Azure App Service**-Explorer mit der rechten Maustaste auf den Knoten der App, und wählen Sie **Start streaming logs** (Streamen der Protokolle starten) aus.

    ![Anzeigen von Streamingprotokollen](../media/deploy-azure/start-streaming-logs.png)

1. Wenn Sie dazu aufgefordert werden, aktivieren Sie die Protokollierung, und starten Sie die Anwendung neu.

    ![Eingabeaufforderung zum Aktivieren der Protokollierung und Neustarten](../media/deploy-azure/enable-restart.png)

1. Nachdem die App neu gestartet wurde, wird das Fenster **Ausgabe** in VS Code mit einer Verbindung mit dem Protokollstream geöffnet, der die Ausgabe zeigt.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Aktualisieren Sie die Webseite im Browser einige Male, um die zusätzliche Protokollausgabe anzuzeigen.

## <a name="8-make-changes-and-redeploy"></a>8. Vornehmen von Änderungen und erneutes Bereitstellen

1. Nehmen Sie eine geringfügige Änderung an der App vor. Ändern Sie `Welcome to Express` in `Welcome to Express with Visual Studio Code`. 

1. Klicken Sie in der App Service-Erweiterung in der Liste mit den App-Diensten mit der rechten Maustaste auf Ihren App-Dienst, und wählen Sie die Option **In Web-App bereitstellen...** aus. 

    :::image type="content" source="../media/deploy-azure/deploy-or-redeploy-app-service.png" alt-text="Bereitstellen oder erneutes Bereitstellen in App Service mit Visual Studio Code":::


## <a name="9-clean-up-resources"></a>9. Bereinigen von Ressourcen

Wenn Sie die Ressourcen bereinigen möchten, klicken Sie mit der rechten Maustaste auf den App-Dienst in der App Service-Erweiterung für Visual Studio Code, und klicken Sie dann auf **Löschen**.

:::image type="content" source="../media/deploy-azure/delete-azure-app-service-with-visual-studio-code-extension.png" alt-text="Wenn Sie die Ressourcen bereinigen möchten, klicken Sie mit der rechten Maustaste auf den App-Dienst in der App Service-Erweiterung von Visual Studio Code, und klicken Sie dann auf **Löschen**.":::

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren von App-Einstellungen](../how-to/configure-web-app-settings.md)

[!INCLUDE [tutorial-next-steps](../includes/tutorial-next-steps.md)]
