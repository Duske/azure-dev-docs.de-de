---
title: 'Schritt 4: Konfigurieren einer benutzerdefinierten Startdatei für Python-Apps in Azure App Service für Linux'
description: Schritt 4 des Tutorials, der den App Service anweist, wie die Web-App gestartet wird, einschließlich spezifischer Anweisungen für Django, Flask und andere Frameworks.
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 0c7fd378fa1166113812c4748c10676030941e13
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95485649"
---
# <a name="4-configure-a-custom-startup-file-for-python-apps-on-azure-app-service"></a>4: Konfigurieren einer benutzerdefinierten Startdatei für Python-Apps in Azure App Service

[Vorheriger Schritt: Erstellen der App Service-Instanz](tutorial-deploy-app-service-on-linux-03.md)

In diesem Schritt wird bei Bedarf eine benutzerdefinierte Startdatei für eine Python-App in einer Azure App Service-Instanz konfiguriert.

Eine benutzerdefinierte Startdatei wird in folgenden Fällen benötigt:

- Sie möchten den Gunicorn-Webserver mit zusätzlichen Argumenten starten, die über die Standardwerte (`--bind=0.0.0.0 --timeout 600`) hinausgehen.

- Ihre App wurde mit einem anderen Framework als Flask oder Django erstellt, oder Sie möchten einen anderen Webserver verwenden.

- Sie verfügen über eine Flask-App, deren Hauptcodedatei **nicht** *app.py* oder *application.py* heißt oder deren App-Objekt **nicht** den Namen `app` hat.

    Anders ausgedrückt: Wenn sich im Stammordner Ihres Projekts keine Datei namens *app.py* oder *application.py* befindet *und* das Flask-App-Objekt nicht `app` heißt, benötigen Sie einen benutzerdefinierten Startbefehl.

    Die Hauptdatei im Codebeispiel aus [Schritt 2](tutorial-deploy-app-service-on-linux-02.md) heißt beispielsweise *hello.py*, und das App-Objekt hat den Namen `myapp`. In diesem Fall ist also eine benutzerdefinierte Startdatei erforderlich, wie in diesem Artikel beschrieben.

Weitere Informationen finden Sie unter [Startprozess des Containers](/azure/app-service/configure-language-python#container-startup-process).

## <a name="create-a-startup-file"></a>Erstellen einer Startdatei

Falls Sie eine benutzerdefinierte Startdatei benötigen, führen Sie die folgenden Schritte aus:

1. Erstellen Sie in Ihrem Projekt eine Datei mit dem Namen *startup.txt*, *startup.sh* oder einem anderen Namen Ihrer Wahl, die Ihre/n Startbefehl/e enthält. Einzelheiten zu Django, Flask und anderen Frameworks finden Sie in den nachfolgenden Abschnitten in diesem Artikel.

    Eine Startdatei kann im Bedarfsfall mehrere Befehle enthalten.

1. Committen Sie die Datei in Ihr Coderepository, damit sie mit dem Rest der App bereitgestellt werden kann.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer die App Service-Instanz aus, um sie zu erweitern, klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen**, und wählen Sie **Im Portal öffnen** aus:

    ![Öffnen der Anwendungseinstellungen im Portal im App Service-Explorer](media/deploy-azure/open-application-settings-in-portal-for-app-service.png)

1. Melden Sie sich bei Bedarf beim Azure-Portal an. Wählen Sie dann auf der Seite **Konfiguration** die Option **Allgemeine Einstellungen** aus, geben Sie den Namen Ihrer Startdatei (z. B. *startup.txt* oder *startup.sh*) unter **Stapeleinstellungen** > **Startbefehl** ein, und wählen Sie dann **Speichern** aus.

    ![Festlegen des Namens der Startbefehlsdatei im Azure-Portal](media/deploy-azure/enter-startup-file-for-app-service-in-the-azure-portal.png)

    > [!NOTE]
    > Alternativ können Sie den Startbefehl auch direkt im Azure-Portal in das Feld **Startbefehl** eingeben, anstatt eine Startbefehlsdatei zu verwenden. Die Verwendung einer Startdatei ist jedoch vorzuziehen, da sich dieser Teil der Konfiguration dann in Ihrem Repository befindet und Sie dort Änderungen überwachen und eine erneute Bereitstellung in einer anderen App Service-Instanz durchführen können.

1. Die App Service-Instanz wird neu gestartet, wenn Sie Änderungen speichern.

    Da Sie Ihren App-Code noch nicht bereitgestellt haben, wird jedoch auf der Website zu diesem Zeitpunkt ein Anwendungsfehler angezeigt. Diese Fehlermeldung gibt an, dass der Gunicorn-Server gestartet wurde, die App jedoch nicht finden konnte und daher keine Antworten auf HTTP-Anforderungen erfolgen. Sie stellen Ihren App-Code im nächsten Schritt bereit.

## <a name="django-startup-commands"></a>Django-Startbefehle

Standardmäßig sucht App Service automatisch den Ordner, der die Datei *wsgi.py* enthält, und startet Gunicorn mit dem folgenden Befehl:

```sh
# <module> is the folder that contains wsgi.py. If you need to use a subfolder,
# specify the parent of <module> using --chdir.
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Wenn Sie Gunicorn-Argumente ändern möchten (um z. B. `--timeout 1200` zu verwenden), erstellen Sie eine Befehlsdatei mit diesen Änderungen.

## <a name="flask-startup-commands"></a>Flask-Startbefehle

Standardmäßig geht der App Service für Linux-Container davon aus, dass die aufrufbare WSGI-Datei einer Flask-App den Namen `app` trägt und in einer Datei namens *application.py* oder *app.py* enthalten ist, die sich im Stammordner der App befindet.

Falls Sie eine der folgenden Variationen verwenden, muss Ihr benutzerdefinierter Startbefehl den Speicherort des App-Objekts im Format *<Datei>:<App-Objekt>* enthalten:

- **Anderer Dateiname und/oder App-Objektname:** Wenn beispielsweise die Hauptcodedatei der App den Namen *hello.py* und das App-Objekt den Namen `myapp` hat, lautet der Startbefehl wie folgt:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```

- **Startdatei in einem Unterordner:** Wenn z. B. die Startdatei *myapp/website.py* lautet und das App-Objekt `app` ist, verwenden Sie das Gunicorn-Argument `--chdir`, um den Ordner anzugeben. Benennen Sie die Startdatei und das App-Objekt dann wie üblich:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir myapp website:app
    ```

- **Startdatei in einem Modul:** Im Code [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) befindet sich die Startdatei *webapp.py* im Ordner *hello_app*, bei dem es sich wiederum um ein Modul mit einer Datei *\_\_init\_\_.py* handelt. Das App-Objekt hat den Namen `app` und wird in *\_\_init\_\_.py* definiert. Außerdem verwendet *webapp.py* einen relativen Import.

    Aufgrund dieser Anordnung löst der Verweis von Gunicorn auf `webapp:app` einen Fehler aus, da ein relativer Import versucht wurde, dessen Quelle sich nicht im Paket befindet, und die App kann nicht gestartet werden.

    Erstellen Sie in diesem Fall eine einfache Shimdatei, die das App-Objekt aus dem Modul importiert, und lassen Sie Gunicorn die App mithilfe des Shims starten. Der Code in [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) enthält z. B. die Datei *startup.py* mit folgendem Inhalt:

    ```python
    # startup.py shim
    from hello_app.webapp import app
    ```

    Der Startbefehl lautet dann wie folgt:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 startup:app
    ```


## <a name="startup-commands-for-other-frameworks-and-web-servers"></a>Startbefehle für andere Frameworks und Webserver

Für den App Service-Container, der Python-Apps ausführt, sind standardmäßig Django und Flask zusammen mit dem gunicorn-Webserver installiert.

Um ein anderes Framework als Django oder Flask (z. B. Falcon, FastAPI usw.) oder um einen anderen Webserver zu verwenden, gehen Sie folgendermaßen vor:

- Nehmen Sie das Framework und/oder den Webserver in Ihre Datei *requirements.txt* auf.

- Identifizieren Sie in Ihrem Startbefehl die aufrufbare WSGI-Datei, wie im [vorherigen Abschnitt für Flask](#flask-startup-commands) beschrieben.

- Um einen anderen Webserver als gunicorn zu starten, verwenden Sie einen Befehl `python -m`, anstatt den Server direkt aufzurufen. Der folgende Befehl startet z. B. den uvicorn-Server, wobei vorausgesetzt wird, dass die aufrufbare WSGI-Datei den Namen `app` trägt und sich in *application.py* befindet:

    ```sh
    python -m uvicorn application:app --host 0.0.0.0
    ```

    Sie verwenden `python -m`, weil über *requirements.txt* installierte Webserver nicht zur globalen Python-Umgebung hinzugefügt werden und deshalb nicht direkt aufgerufen werden können. Der Befehl `python -m` ruft den Server innerhalb der aktuellen virtuellen Umgebung auf.

> [!div class="nextstepaction"]
> [Ich habe meine Startdatei konfiguriert: Fahren Sie mit Schritt 5 fort. >>>](tutorial-deploy-app-service-on-linux-05.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
