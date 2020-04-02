---
title: 'Schritt 5: Bereitstellen einer Python-Web-App in Azure App Service für Linux mit VS Code'
description: 'Tutorialschritt 5: Bereitstellen des Web-App-Codes'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 3f17fe3190857d1cc4faca84c9319e1c514cea4c
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441205"
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

1. Während die Bereitstellung läuft, können Sie den Fortschritt im **Ausgabefenster** von VS Code anzeigen.

    ![Fortschritt der Bereitstellung im Ausgabefenster von VS Code](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. Wenn die Bereitstellung nach einigen Minuten abgeschlossen ist (die Dauer ist abhängig von der Anzahl zu installierender Abhängigkeiten), wird die folgende Meldung angezeigt. Wählen Sie die Schaltfläche **Website durchsuchen** aus, um die laufende Website anzuzeigen.

    ![Bereitstellung abgeschlossen mit der Schaltfläche „Website durchsuchen“](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![Erfolgreiche Ausführung der App in App Service](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. Um zu überprüfen, ob die Dateien bereitgestellt werden, erweitern Sie die App Service-Instanz im **Azure App Service**-Explorer und dann **Dateien**:

    ![Überprüfen der Bereitstellung von Dateien im App Service-Explorer](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    App Service erstellt im Ordner *antenv* eine virtuelle Umgebung mit Ihren Abhängigkeiten. Wenn Sie diesen Knoten erweitern, können Sie überprüfen, ob die Pakete, die Sie in der Datei *requirements.txt* angegeben haben, auch in *antenv/lib/python3.7/site-packages* installiert wurden.

> [!div class="nextstepaction"]
> [Ich habe meine App bereitgestellt: Fahren Sie mit Schritt 6 fort. >>>](tutorial-deploy-app-service-on-linux-06.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)
