---
title: Bereitstellen von Node.js-Apps in Azure App Service in Visual Studio Code
description: 'Teil 3 des Tutorials: Bereitstellen der Website'
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: 1a8b4a37fa823b631e6b4849cf7cff6ac2ba26f3
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894276"
---
# <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

[Vorheriger Schritt: Erstellen der App](tutorial-vscode-azure-app-service-node-02.md)

In diesem Schritt stellen Sie Ihre Node.js-App in Azure bereit, indem Sie die Git-Bereitstellung per VS Code und die Azure App Service-Erweiterung verwenden. Hierfür müssen Sie zuerst ein lokales Git-Repository initialisieren und dann eine Web-App in Azure erstellen und abschließend VS Code für die Verwendung der Git-Bereitstellung konfigurieren.

1. Stellen Sie im Terminal sicher, dass Sie sich im Ordner *expressApp1* befinden, und starten Sie Visual Studio Code dann mit dem folgenden Befehl:

    ```bash
    code .
    ```

1. Wählen Sie in VS Code das Symbol für die Quellcodeverwaltung aus, um den Explorer für die **Quellcodeverwaltung** zu öffnen, und anschließend **+** , um ein lokales Git-Repository zu initialisieren:

    ![Initialisieren eines Git-Repositorys](media/deploy-azure/git-init.png)

1. Wählen Sie bei entsprechender Aufforderung *expressApp1* als Arbeitsbereichsordner aus.

1. Geben Sie nach der Initialisierung des Repositorys die Meldung „Initial commit“ (Erster Commit) ein, und wählen Sie das Häkchen aus, um den ersten Commit Ihrer Quelldateien zu erstellen.

    ![Durchführen eines ersten Commits für das Repository](media/deploy-azure/initial-commit.png)

1. Geben Sie in der Befehlspalette (**STRG**+**UMSCHALT**+**P**) den Befehl „create web“ein, und wählen Sie **Azure App Service: Create New Web App...Advanced** (Azure App Service: Neue Web-App erstellen...Erweitert) aus. Sie verwenden den erweiterten Befehl anstelle der Linux-Standardeinstellungen, um die vollständige Kontrolle über die Bereitstellung zu haben, einschließlich Ressourcengruppe, App Service-Plan und Betriebssystem.

1. Gehen Sie bei den Aufforderungen wie folgt vor:

    - Geben Sie für **Geben Sie einen global eindeutigen Namen ein** einen Namen ein, der in Azure eindeutig ist. Verwenden Sie nur alphanumerische Zeichen („A - Z“, „a - z“ und „0 - 9“) und Bindestriche („-“).
    - Wählen Sie **Neue Ressourcengruppe erstellen** aus, und geben Sie einen Namen ein, z. B. `AppServiceTutorial-rg`.
    - Wählen Sie ein Betriebssystem (Windows oder Linux) aus.
    - Nur für Linux: Wählen Sie eine Node.js-Version aus. (Für Windows legen Sie die Version später über eine App-Einstellung fest.)
    - Wählen Sie **Einen neuen App Services-Plan erstellen** aus, geben Sie einen Namen wie `AppServiceTutorial-plan` an, und wählen Sie den Tarif **F1 Free** aus.
    - Wählen Sie für die Application Insights-Ressource die Option **Vorerst überspringen** aus.
    - Wählen Sie einen Ort in Ihrer Nähe aus.

1. Sie werden von VS Code nach kurzer Zeit darüber benachrichtigt, dass die Erstellung abgeschlossen ist. Schließen Sie die Benachrichtigung mit der Schaltfläche **X**:

    ![Benachrichtigung nach Abschluss der Web-App-Erstellung](media/deploy-azure/creation-complete.png)

1. Nachdem Sie die Web-App eingerichtet haben, weisen Sie VS Code als Nächstes an, Ihren Code aus dem lokalen Git-Repository bereitzustellen. Wählen Sie das Azure-Symbol aus, um den **Azure App Service**-Explorer zu öffnen, erweitern Sie Ihren Abonnementknoten, klicken Sie mit der rechten Maustaste auf den Namen der gerade erstellten Web-App, und wählen Sie **Bereitstellungsquelle konfigurieren** aus.

    ![Befehl zum Konfigurieren der Bereitstellungsquelle in einer Web-App](media/deploy-azure/configure-deployment-source.png)

1. Wählen Sie bei entsprechender Aufforderung **LocalGit** aus.

1. Bei einer Bereitstellung für App Service unter Windows müssen Sie vorher zwei Einstellungen erstellen:

    1. Erweitern Sie in VS Code den Knoten für den neuen App-Dienst, klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen**, und wählen Sie anschließend **Neue Einstellung hinzufügen** aus:

        ![Hinzufügen eines Befehls für eine App-Einstellung](media/deploy-azure/add-setting.png)

    1. Geben Sie `WEBSITE_NODE_DEFAULT_VERSION` als Einstellungsschlüssel und `10.15.2` als Einstellungswert ein. Mit dieser Einstellung wird die Node.js-Version festgelegt.
    1. Wiederholen Sie den Prozess, um einen Schlüssel für `SCM_DO_BUILD_DURING_DEPLOYMENT` mit dem Wert `1` zu erstellen. Mit dieser Einstellung wird erzwungen, dass der Server bei der Bereitstellung `npm install` ausführt.
    1. Erweitern Sie den Knoten **Anwendungseinstellungen**, um sich zu vergewissern, dass die Einstellungen vorhanden sind.

1. Wählen Sie das Symbol mit dem blauen Pfeil nach oben aus, um Ihren Code in Azure bereitzustellen:

    ![Symbol „In Web-App bereitstellen“](media/deploy-azure/deploy.png)

1. Wählen Sie für die Aufforderungen den Ordner *expressApp1* und dann den Namen der Web-App aus, die Sie weiter oben erstellt haben.

1. Wählen Sie bei der Bereitstellung unter Linux **Ja** aus, wenn Sie aufgefordert werden, Ihre Konfiguration für die Ausführung von `npm install` auf dem Zielserver zu aktualisieren.

    ![Aufforderung zum Aktualisieren der Konfiguration auf dem Linux-Zielserver](media/deploy-azure/server-build.png)

1. Wählen Sie unter Linux und Windows **Ja** aus, wenn **Always deploy the workspace „nodejs-docs-hello-world“ to (app name)"** (Arbeitsbereich „nodejs-docs-hello-world“ immer in (App-Name) bereitstellen) angezeigt wird. Mit der Auswahl von **Ja** wird VS Code angewiesen, für nachfolgende Bereitstellungen automatisch dieselbe App Service-Web-App zu verwenden.

1. Wählen Sie nach Abschluss der Bereitstellung in der Aufforderung die Option **Website durchsuchen** aus, um Ihre neu bereitgestellte Web-App anzuzeigen. Im Browser sollte „Hello World!“ angezeigt werden.

1. (Optional): Sie können Ihre Codedateien ändern und dann die Schaltfläche für die Bereitstellung erneut verwenden, um die Web-App zu aktualisieren.

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt](tutorial-vscode-azure-app-service-node-04.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=deploy-app)
