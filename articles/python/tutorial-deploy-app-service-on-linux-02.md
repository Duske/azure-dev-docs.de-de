---
title: 'Schritt 2: Vorbereiten einer App für die Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 2: Einrichten der Anwendung'
ms.topic: conceptual
ms.date: 11/20/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 7197f8afc28bd62e7247c3955c888199ee69c509
ms.sourcegitcommit: 09b4a2dbe13601fdf16fcc4082a5075b46ad3459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559194"
---
# <a name="2-prepare-your-app-for-deployment-to-azure-app-service"></a>2: Vorbereiten Ihrer App für die Bereitstellung in Azure App Service

[Vorheriger Schritt: Konfigurieren Ihrer Umgebung](tutorial-deploy-app-service-on-linux-01.md)

In diesem Artikel bereiten Sie eine App für die Bereitstellung in der Azure App Service-Instanz für dieses Tutorial vor. Sie können eine vorhandene App verwenden oder eine App erstellen bzw. herunterladen.

## <a name="if-you-already-have-an-app"></a>Wenn Sie bereits über eine App verfügen

Wenn Sie bereits über eine App verfügen, mit der Sie arbeiten möchten, vergewissern Sie sich, dass am Stamm Ihres Projekts die Datei *requirements.txt* mit einer Liste Ihrer Abhängigkeiten vorhanden ist (einschließlich Frameworks wie Flask oder Django). Sie können ein beliebiges Framework Ihrer Wahl verwenden.

> [!div class="nextstepaction"]
> [Ich verfüge über eine eigene, entsprechend vorbereitete App: Fahren Sie mit Schritt 3 fort. >>>](tutorial-deploy-app-service-on-linux-03.md)

## <a name="if-you-dont-already-have-an-app"></a>Wenn Sie noch nicht über eine App verfügen

Wenn Sie noch nicht über eine App verfügen, verwenden Sie *eine* der im Anschluss angegebenen Optionen. Überprüfen Sie, ob die App lokal ausgeführt wird.

Im Rest dieses Tutorials wird der in [Option 3](#option-3-create-a-minimal-flask-app) gezeigte Code verwendet.

### <a name="option-1-use-the-vs-code-flask-tutorial-sample"></a>Option 1: Verwenden des VS Code-Beispiels für das Flask-Tutorial

Laden Sie [https://github.com/Microsoft/python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Flask-Tutorials](https://code.visualstudio.com/docs/python/tutorial-flask). Beachten Sie, dass sich der App-Code im Ordner *hello_app* befindet. Informationen zum lokalen Ausführen der App finden Sie in der Datei *readme.md* des Beispiels.

### <a name="option-2-use-the-vs-code-django-tutorial-sample"></a>Option 2: Verwenden des VS Code-Beispiels für das Django-Tutorial

Laden Sie [https://github.com/Microsoft/python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Django-Tutorials](https://code.visualstudio.com/docs/python/tutorial-django).

Im Idealfall wird von in der Cloud bereitgestellten Django-Apps auch eine cloudbasierte Datenbank wie etwa PostgreSQL für Azure verwendet. Weitere Informationen finden Sie im [Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL mithilfe des Azure-Portals](tutorial-python-postgresql-app-portal.md).

Wenn von Ihrer Django-App wie in diesem Beispiel eine lokale SQLite-Datenbank verwendet wird, ist es für dieses Tutorial am einfachsten, wenn Sie eine vorab initialisierte und aufgefüllte Kopie der Datei *db.sqlite3* in Ihr Repository einschließen. Andernfalls müssen Sie einen Postbuildbefehl konfigurieren, um den Django-Befehl `migrate` in dem Container auszuführen, in dem die App bereitgestellt wird. Weitere Informationen finden Sie unter [App Service-Konfiguration: Anpassen der Buildautomatisierung](/azure/app-service/configure-language-python#customize-build-automation).

### <a name="option-3-create-a-minimal-flask-app"></a>Option 3: Erstellen einer minimalen Flask-App

In diesem Abschnitt wird die in dieser exemplarischen Vorgehensweise verwendete minimale Flask-App beschrieben.

1. Erstellen Sie einen neuen Ordner, öffnen Sie ihn in VS Code, und fügen Sie eine Datei mit dem Namen *hello.py* mit dem nachstehenden Inhalt hinzu. Das App-Objekt wird absichtlich mit `myapp` benannt, um zu veranschaulichen, wie die Namen im Startbefehl für App Service verwendet werden, wie Sie später erfahren.

    ```python
    from flask import Flask
    myapp = Flask(__name__)

    @myapp.route("/")
    def hello():
        return "Hello Flask, on Azure App Service for Linux"
    ```

1. Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

    ```text
    Flask
    ```

1. Öffnen Sie ein Terminal mithilfe des Menübefehls **Terminal** > **Neues Terminal**.

1. Erstellen Sie im Terminal eine virtuelle Umgebung mit dem Namen `.venv`, und aktivieren Sie sie. 

    # <a name="macoslinux"></a>[macOS/Linux](#tab/linux)

    ```bash
    sudo apt-get install python3-venv    # If needed
    python3 -m venv .venv
    source .venv/bin/activate
    ```

    # <a name="windows"></a>[Windows](#tab/windows)

    ```cmd
    py -3 -m venv .venv
    .venv\scripts\activate
    ```

    ---

1. Wenn Sie von VS Code zum Aktivieren der neu erstellten Umgebung aufgefordert werden, antworten Sie mit **Ja**.

1. Installieren Sie die Abhängigkeiten der App:

    ```cmd
    pip install -r requirements.txt
    ```

1. Legen Sie die Umgebungsvariable FLASK_APP fest, die Flask anweist, wo das App-Objekt zu finden ist:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set FLASK_APP=hello:myapp
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```ps
    $env:FLASK_APP = "hello:myapp"
    ```

   # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export FLASK_APP=hello:myapp
    ```

    ---

1. Führen Sie die App aus:

    ```cmd
    flask run
    ```

1. Öffnen Sie die App unter Verwendung der URL `http://127.0.0.1:5000/` in einem Browser. Die Nachricht „Hello Flask, on Azure App Service for Linux.“ sollte angezeigt werden.

1. Beenden Sie den Flask-Server durch Drücken von **STRG**+**C** im Terminal.

> [!div class="nextstepaction"]
> [Meine App ist bereit: Fahren Sie mit Schritt 3 fort. >>>](tutorial-deploy-app-service-on-linux-03.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
