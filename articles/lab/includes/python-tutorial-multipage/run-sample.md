---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 6f9e5e8f30c5108996f14bef9dc4c90a668c60c1
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405182"
---
1. Stellen Sie sicher, dass Sie sich im Ordner *python-docs-hello-world* befinden. 

1. Erstellen Sie eine virtuelle Umgebung, und installieren Sie Abhängigkeiten:

    [!include [virtual environment setup](../app-service-quickstart-python-venv.md)]

    Vergewissern Sie sich, dass Sie sich im Ordner *python-docs-hello-world* befinden, wenn die Meldung „[Errno 2] Datei oder Verzeichnis nicht vorhanden: 'requirements.txt'.“ angezeigt wird.

1. Führen Sie den Entwicklungsserver aus.

    ```terminal  
    flask run
    ```
    
    Standardmäßig wird vom Server angenommen, dass sich das Eingabemodul der App in *app.py* befindet (wie im Beispiel). (Wenn Sie einen anderen Modulnamen verwenden, müssen Sie die Umgebungsvariable `FLASK_APP` auf diesen Namen festlegen.)

1. Öffnen Sie einen Webbrowser, und navigieren Sie zur Beispiel-App unter `http://localhost:5000/`. Die App zeigt die Meldung **Hello, World!** an.

    ![Lokales Ausführen einer Python-Beispiel-App](../../media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. Drücken Sie in Ihrem Terminalfenster **STRG**+**C** , um den Entwicklungsserver zu beenden.
