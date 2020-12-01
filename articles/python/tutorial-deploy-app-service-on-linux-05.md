---
title: 'Schritt 5: Bereitstellen einer Python-Web-App in Azure App Service für Linux mit VS Code'
description: 'Tutorialschritt 5: Bereitstellen des Web-App-Codes'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 7b3743d417ed3455c59f5b9887ee54728fb7318a
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485704"
---
# <a name="5-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>5: Bereitstellen Ihrer Python-Web-App in Azure App Service für Linux

[Vorheriger Schritt: Konfigurieren einer benutzerdefinierten Startdatei](tutorial-deploy-app-service-on-linux-04.md)

Verwenden Sie dieses Verfahren, um Ihre Python-App in einer Azure App Service-Instanz bereitzustellen.

1. Öffnen Sie in Visual Studio Code den **Azure App Service**-Explorer, und wählen Sie den blauen Pfeil nach oben aus:

   ![Bereitstellen Ihrer Web-App in App Service im App Service-Explorer](media/deploy-azure/deploy-web-app-to-app-service-in-app-service-explorer.png)

    Alternativ können Sie auch mit der rechten Maustaste auf den App Service-Namen klicken und dann den Befehl **In Web-App bereitstellen** auswählen.

1. Geben Sie in den daraufhin folgenden Eingabeaufforderungen die folgenden Daten an:

    - Wählen Sie für „Select the folder to deploy“ (Ordner für Bereitstellung auswählen) den aktuellen App-Ordner aus.
    - Wählen Sie unter „Web-App auswählen“ die App Service-Instanz aus, die Sie im vorherigen Schritt erstellt haben.
    - Wenn Sie aufgefordert werden, Ihre Buildkonfiguration zu aktualisieren, um Buildbefehle auszuführen, antworten Sie mit **Ja**.
    - Wenn Sie gefragt werden, ob die vorhandene Bereitstellung überschrieben werden soll, antworten Sie mit **Bereitstellen**.
    - Wenn Sie gefragt werden, ob der Arbeitsbereich immer bereitgestellt werden soll, antworten Sie mit **Ja**.

1. Während die Bereitstellung läuft, können Sie den Fortschritt im **Ausgabefenster** von VS Code anzeigen.

    ![Fortschritt der Bereitstellung im Ausgabefenster von VS Code](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. Wenn die Bereitstellung nach einigen Minuten abgeschlossen ist (die Dauer ist abhängig von der Anzahl zu installierender Abhängigkeiten), wird die folgende Meldung angezeigt. Wählen Sie die Schaltfläche **Website durchsuchen** aus, um die laufende Website anzuzeigen.

    ![Bereitstellung abgeschlossen mit der Schaltfläche „Website durchsuchen“](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![Erfolgreiche Ausführung der App in App Service](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. Sollte weiterhin die Standard-App angezeigt werden, warten Sie ein bis zwei Minuten, bis der Container nach der Bereitstellung neu gestartet wurde, und versuchen Sie es dann noch mal. Falls Sie einen benutzerdefinierten Startbefehl verwenden und sich vergewissert haben, dass er korrekt ist, können Sie mit der Protokollüberprüfung in Schritt 6 fortfahren.

1. Um zu überprüfen, ob die Dateien bereitgestellt werden, erweitern Sie die App Service-Instanz im **Azure App Service**-Explorer und dann **Dateien**:

    ![Überprüfen der Bereitstellung von Dateien im App Service-Explorer](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    Hinweis: Die Dateien *.deployment*, *antenv.tar.gz* und *oryx-manifest.toml* werden vom App Service-Buildsystem verwendet. *hostingstart.html* ist die Standard-App-Seite.

> [!div class="nextstepaction"]
> [Ich habe meine App bereitgestellt: Fahren Sie mit Schritt 6 fort. >>>](tutorial-deploy-app-service-on-linux-06.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
