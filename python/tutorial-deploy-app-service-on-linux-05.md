---
title: Bereitstellen einer Python-Web-App in Azure App Service für Linux mit VS Code
description: 'Tutorialschritt 5: Bereitstellen des Web-App-Codes'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: e9b85928bd2b1308ab57747d7b0c32c085274cde
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019678"
---
# <a name="deploy-your-app"></a>Bereitstellen Ihrer App

[Vorheriger Schritt: Konfigurieren einer benutzerdefinierten Startdatei](tutorial-deploy-app-service-on-linux-04.md)

1. Öffnen Sie in Visual Studio Code den **Azure App Service**-Explorer, und wählen Sie den blauen Pfeil nach oben aus:

   ![Befehl „In Web-App bereitstellen“](media/deploy-azure/deploy-to-web-app-command.png)

    Alternativ können Sie auch mit der rechten Maustaste auf den App Service-Namen klicken und dann den Befehl **In Web-App bereitstellen** auswählen.

1. Geben Sie in den daraufhin folgenden Eingabeaufforderungen die folgenden Daten an:

    - Wählen Sie für „Select the folder to deploy“ (Ordner für Bereitstellung auswählen) den aktuellen App-Ordner aus.
    - Wählen Sie unter „Web-App auswählen“ die App Service-Instanz aus, die Sie im vorherigen Schritt erstellt haben.

1. Während die Bereitstellung läuft, können Sie den Fortschritt im **Ausgabefenster** von VS Code anzeigen.

    ![Fortschritt der Bereitstellung im Ausgabefenster von VS Code](media/deploy-azure/deployment-progress.png)

1. Wenn die Bereitstellung nach einigen Minuten abgeschlossen ist (die Dauer ist abhängig von der Anzahl zu installierender Abhängigkeiten), wird die folgende Meldung angezeigt. Wählen Sie die Schaltfläche **Website durchsuchen** aus, um die laufende Website anzuzeigen.

    ![Meldung zum Bereitstellungsabschluss](media/deploy-azure/deployment-complete.png)

    ![Erfolgreiche Ausführung der App in App Service](media/deploy-azure/running-app.png)

1. Um zu überprüfen, ob die Dateien bereitgestellt werden, erweitern Sie die App Service-Instanz im **Azure App Service**-Explorer und dann **Dateien**:

    ![Überprüfen der Bereitstellung von Dateien im App Service-Explorer](media/deploy-azure/expand-files-node.png)

    App Service erstellt im Ordner *antenv* eine virtuelle Umgebung mit Ihren Abhängigkeiten. Wenn Sie diesen Knoten erweitern, können Sie überprüfen, ob die Pakete, die Sie in der Datei *requirements.txt* angegeben haben, auch in *antenv/lib/python3.7/site-packages* installiert wurden.

> [!div class="nextstepaction"]
> [Ich habe meine App bereitgestellt.](tutorial-deploy-app-service-on-linux-06.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)
