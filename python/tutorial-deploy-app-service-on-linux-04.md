---
title: 'Tutorial: Konfigurieren einer benutzerdefinierten Startdatei für Python-Apps in Azure App Service für Linux'
description: 'Tutorialschritt 4: Anweisen von App Service, wie die Web-App gestartet wird'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: fdd3248e50020bb90cdde8a3e9c30c2895dccdae
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172204"
---
# <a name="tutorial-configure-a-custom-startup-file-for-python-apps-on-azure-app-service"></a>Tutorial: Konfigurieren einer benutzerdefinierten Startdatei für Python-Apps in Azure App Service

[Vorheriger Schritt: Erstellen der App Service-Instanz](tutorial-deploy-app-service-on-linux-02.md)

Je nachdem, wie Sie Ihre App strukturiert haben, müssen Sie möglicherweise eine benutzerdefinierte Startbefehlsdatei für Ihre App erstellen, wie in der Azure-Dokumentation unter [Konfigurieren von Python-Apps für App Service für Linux](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python) beschrieben.

Spezifische Anwendungsfälle für einen benutzerdefinierten Startbefehl:

- Sie verfügen über eine **Flask**-App, deren Startdatei und App-Objekt **anders** als *application.py* bzw. `app` heißen. Anders ausgedrückt: Sofern Sie nicht im Stammordner Ihres Projekts über eine Datei *application.py* verfügen *und* das Flask-App-Objekt den Namen `app` hat, benötigen Sie einen benutzerdefinierten Startbefehl.
- Sie möchten den Gunicorn-Webserver mit zusätzlichen Argumenten starten, die über die Standardwerte (`--bind=0.0.0.0 --timeout 600`) hinausgehen.

## <a name="create-a-startup-file"></a>Erstellen einer Startdatei

Wenn Sie eine benutzerdefinierte Startdatei benötigen, führen Sie die folgenden Schritte aus:

1. Erstellen Sie in Ihrem Projekt eine Datei mit dem Namen *startup.txt* (oder einem anderen Namen Ihrer Wahl), die Ihren Startbefehl enthält. Informationen zu Flask finden Sie unter [Flask-Startbefehle](#flask-startup-commands) im nächsten Abschnitt. Django-Apps benötigen in der Regel keine Anpassung.

1. Committen Sie die Datei in Ihr Coderepository, damit sie mit dem Rest der App bereitgestellt werden kann.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer die App Service-Instanz aus, um sie zu erweitern, klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen**, und wählen Sie **Im Portal öffnen** aus:

    ![Befehl „Im Portal öffnen“ im App Service-Explorer](media/deploy-azure/open-settings-in-portal-command.png)

1. Melden Sie sich bei Bedarf beim Azure-Portal an. Wählen Sie dann auf der Seite **Konfiguration** die Option **Allgemeine Einstellungen** aus, geben Sie den Namen der Startdatei (z. B. *startup.txt*) unter **Stapeleinstellungen** > **Startbefehl** ein, und wählen Sie dann **Speichern** aus.

    ![Festlegen des Namens der Startdatei im Azure-Portal](media/deploy-azure/azure-portal-startup-file.png)

    > [!NOTE]
    > Anstatt eine Startbefehlsdatei zu verwenden, können Sie den Startbefehl auch direkt im Azure-Portal im Feld **Startbefehl** einfügen. Die Verwendung einer Datei ist jedoch im Allgemeinen vorzuziehen, da damit dieser Teil der Konfiguration in Ihrem Repository verbleibt und Sie dort Änderungen überwachen und die erneute Bereitstellung in einer anderen App Service-Instanz durchführen können.

1. Die App Service-Instanz wird neu gestartet, wenn Sie Änderungen speichern. Da Sie Ihren App-Code noch nicht bereitgestellt haben, wird jedoch auf der Website zu diesem Zeitpunkt ein Anwendungsfehler angezeigt. Diese Fehlermeldung gibt an, dass der Gunicorn-Server gestartet wurde, die App jedoch nicht finden konnte und daher keine Antworten auf HTTP-Anforderungen erfolgen. Sie stellen Ihren App-Code im nächsten Schritt bereit.

## <a name="django-startup-commands"></a>Django-Startbefehle

Standardmäßig sucht App Service automatisch den Ordner, der die Datei *wsgi.py* enthält, und startet Gunicorn mit dem folgenden Befehl:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Wenn Sie Gunicorn-Argumente ändern möchten (um z. B. `--timeout 1200` zu verwenden), erstellen Sie eine Befehlsdatei mit diesen Änderungen.

## <a name="flask-startup-commands"></a>Flask-Startbefehle

Standardmäßig geht der App Service für Linux-Container davon aus, dass die Startdatei einer Flask-App den Namen *application.py* hat und sich im Stammordner der App befindet. Ferner wird davon ausgegangen, dass das in dieser Datei definierte Flask-App-Objekt den Namen `app` hat. Wenn Ihre App nicht genau so strukturiert ist, muss Ihr benutzerdefinierter Startbefehl den Speicherort des App-Objekts angeben:

1. **Anderer Dateiname und/oder App-Objektname:** Wenn z. B. die Startdatei der App den Namen *hello.py* und das App-Objekt den Namen `myapp` hat, lautet der Startbefehl wie folgt:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```

1. **Startdatei in einem Unterordner:** Wenn z. B. die Startdatei *myapp/website.py* lautet und das App-Objekt `app` ist, verwenden Sie das Gunicorn-Argument `--chdir`, um den Ordner anzugeben. Benennen Sie die Startdatei und das App-Objekt dann wie üblich:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir myapp website:app
    ```

1. **Startdatei in einem Modul:** Im Code [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) befindet sich die Startdatei *webapp.py* im Ordner *hello_app*, bei dem es sich wiederum um ein Modul mit einer Datei *\_\_init\_\_.py* handelt. Das App-Objekt hat den Namen `app` und wird in *\_\_init\_\_.py* definiert. Außerdem verwendet *webapp.py* einen relativen Import. Aufgrund dieser Anordnung löst der Verweis von Gunicorn auf `webapp:app` einen Fehler aus, da ein relativer Import versucht wurde, dessen Quelle sich nicht im Paket befindet, und die App kann nicht gestartet werden.

    Erstellen Sie in diesem Fall eine einfache Shimdatei, die das App-Objekt aus dem Modul importiert, und lassen Sie Gunicorn die App mithilfe des Shims starten. Der Code in [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) enthält z. B. die Datei *startup.py* mit folgendem Inhalt:

    ```python
    # startup.py shim
    from hello_app.webapp import app
    ```

    Der Startbefehl lautet dann wie folgt:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 startup:app
    ```

> [!div class="nextstepaction"]
> [Ich habe meine Startdatei konfiguriert.](tutorial-deploy-app-service-on-linux-05.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=04-startup-command)
