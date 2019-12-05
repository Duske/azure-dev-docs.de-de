---
title: 'Tutorial: Vorbereiten einer App für die Bereitstellung in Azure App Service für Linux aus Visual Studio Code'
description: 'Tutorialschritt 2: Einrichten der Anwendung'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: e504e78ae660719c60827db46d4801f5f5c4b3ce
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466211"
---
# <a name="tutorial-prepare-your-app-for-deployment-to-azure-app-service"></a>Tutorial: Vorbereiten Ihrer App für die Bereitstellung in Azure App Service

[Vorheriger Schritt: Voraussetzungen](tutorial-deploy-app-service-on-linux-01.md)

In diesem Artikel bereiten Sie eine App für die Bereitstellung in der Azure App Service-Instanz für dieses Tutorial vor. Sie können eine vorhandene App verwenden oder eine App erstellen bzw. herunterladen.

Wenn Sie bereits über eine App verfügen, mit der Sie arbeiten möchten, stellen Sie sicher, dass Sie über die Datei *Requirements. txt* verfügen, in der Ihre Abhängigkeiten beschrieben werden, einschließlich Frameworks wie Flask oder Django.

Wenn Sie noch nicht über eine App verfügen, verwenden Sie eine der folgenden Optionen. Überprüfen Sie, ob die App lokal ausgeführt wird.

## <a name="minimal-flask-app"></a>Minimale Flask-App

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
    Flask==1.1.1
    ```

1. Befolgen Sie die Anweisungen im [Flask-Tutorial: Erstellen einer Projektumgebung für Flask](https://code.visualstudio.com/docs/python/tutorial-flask#create-a-project-environment-for-flask), um eine virtuelle Umgebung mit installiertem Flask zu erstellen, in der die App lokal ausgeführt werden kann.

1. Verwenden Sie zum Ausführen dieser App die folgenden Befehle (abhängig vom Betriebssystem). Die Umgebungsvariable FLASK_APP weist Flask an, wo das App-Objekt zu finden ist.

    ```ps
    set FLASK_APP=hello:myapp
    flask run
    ```

    ```bash
    export FLASK_APP=hello:myapp
    flask run
    ```

    Anschließend können Sie die App mithilfe der URL `http://127.0.0.1:5000/` in einem Browser öffnen.

## <a name="vs-code-flask-tutorial-sample"></a>VS Code-Beispiel für das Flask-Tutorial

Laden Sie [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Flask-Tutorials](https://code.visualstudio.com/docs/python/tutorial-flask).

## <a name="vs-code-django-tutorial-sample"></a>VS Code-Beispiel für das Django-Tutorial

Laden Sie [python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial) herunter, oder klonen Sie diesen Code. Es handelt sich um das Ergebnis des [Django-Tutorials](https://code.visualstudio.com/docs/python/tutorial-django).

Wenn Ihre Django-App wie in diesem Beispiel eine lokale SQLite-Datenbank verwendet, müssen Sie eine vorinitialisierte und vorab aufgefüllte Kopie der Datei *db.sqlite3* in Ihr Repository einfügen. Der Grund hierfür ist, dass App Service für Linux derzeit nicht über eine Möglichkeit verfügt, den Django-Befehl `migrate` als Teil der Bereitstellung auszuführen, sodass Sie eine vorgefertigte Datenbank bereitstellen müssen. Auch dann ist die Datenbank praktisch schreibgeschützt. Das Schreiben in die Datenbank verursacht darüber hinaus Fehler.

Die beste Option ist in jedem Fall, eine separate Datenbank zu verwenden, die unabhängig vom App-Code bereitgestellt und initialisiert wird.

> [!div class="nextstepaction"]
> [Meine App ist bereit.](tutorial-deploy-app-service-on-linux-03.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=02-prepare-app)
