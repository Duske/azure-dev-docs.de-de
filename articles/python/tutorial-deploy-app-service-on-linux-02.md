---
title: 'Schritt 2: Vorbereiten einer App für die Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 2: Einrichten der Anwendung'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 464807b8a4e637db7a0911d23770246dfc7fb0ae
ms.sourcegitcommit: 723441eda0eb4ff893123201a9e029b7becf5ecc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91846691"
---
# <a name="2-prepare-your-app-for-deployment-to-azure-app-service"></a>2: Vorbereiten Ihrer App für die Bereitstellung in Azure App Service

[Vorheriger Schritt: Konfigurieren Ihrer Umgebung](tutorial-deploy-app-service-on-linux-01.md)

In diesem Artikel bereiten Sie eine App für die Bereitstellung in der Azure App Service-Instanz für dieses Tutorial vor. Sie können eine vorhandene App verwenden oder eine App erstellen bzw. herunterladen.

Wenn Sie bereits über eine App verfügen, mit der Sie arbeiten möchten, stellen Sie sicher, dass Sie über die Datei *Requirements. txt* verfügen, in der Ihre Abhängigkeiten beschrieben werden, einschließlich Frameworks wie Flask oder Django. Sie können ein beliebiges Framework Ihrer Wahl verwenden.

Wenn Sie noch nicht über eine App verfügen, verwenden Sie eine der folgenden Optionen. Überprüfen Sie, ob die App lokal ausgeführt wird.

## <a name="option-1-vs-code-flask-tutorial-sample"></a>Option 1: VS Code-Beispiel für das Flask-Tutorial

Laden Sie [https://github.com/Microsoft/python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Flask-Tutorials](https://code.visualstudio.com/docs/python/tutorial-flask).

## <a name="option-2-vs-code-django-tutorial-sample"></a>Option 2: VS Code-Beispiel für das Django-Tutorial

Laden Sie [https://github.com/Microsoft/python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Django-Tutorials](https://code.visualstudio.com/docs/python/tutorial-django).

Wenn Ihre Django-App wie in diesem Beispiel eine lokale SQLite-Datenbank verwendet, müssen Sie eine vorinitialisierte und vorab aufgefüllte Kopie der Datei *db.sqlite3* in Ihr Repository einfügen. Der Grund hierfür ist, dass App Service für Linux derzeit nicht über eine Möglichkeit verfügt, den Django-Befehl `migrate` als Teil der Bereitstellung auszuführen, sodass Sie eine vorgefertigte Datenbank bereitstellen müssen. Auch dann ist die Datenbank praktisch schreibgeschützt. Das Schreiben in die Datenbank verursacht darüber hinaus Fehler.

Die beste Option ist in jedem Fall, eine separate Datenbank zu verwenden, die unabhängig vom App-Code bereitgestellt und initialisiert wird.

## <a name="option-3-create-a-minimal-flask-app"></a>Option 3: Erstellen einer minimalen Flask-App

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
    Flask==1.1.2
    ```

1. Öffnen Sie ein Terminal mithilfe des Menübefehls **Terminal** > **Neues Terminal**.

1. Erstellen und aktivieren Sie im Terminal eine virtuelle Umgebung mit dem Namen `env`:

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

1. Anschließend können Sie die App mithilfe der URL `http://127.0.0.1:5000/` in einem Browser öffnen.

> [!div class="nextstepaction"]
> [Meine App ist bereit: Fahren Sie mit Schritt 3 fort. >>>](tutorial-deploy-app-service-on-linux-03.md)

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/FlaskVSCQuickstartHelp)
