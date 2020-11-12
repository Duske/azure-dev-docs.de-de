---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: e500a760430d92fa6640d964320a3ef3f161b7cc
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405152"
---
In diesem Abschnitt nehmen Sie eine geringfügige Codeänderung vor und stellen den Code dann erneut in Azure bereit. Die Codeänderung umfasst eine `print`-Anweisung, um die Protokollierungsausgabe für den nächsten Abschnitt zu generieren.

Öffnen Sie *app.py* in einem Editor, und aktualisieren Sie die Funktion `hello` so, dass sie dem folgenden Code entspricht. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```

    
Speichern Sie Ihre Änderungen, und stellen Sie die App dann mit dem Befehl `az webapp up` erneut bereit:

```azurecli
az webapp up
```

In diesem Befehl werden lokal zwischengespeicherte Werte aus der Datei *.azure/config* verwendet (einschließlich App-Name, Ressourcengruppe und App Service-Plan).

Wechseln Sie nach Abschluss der Bereitstellung wieder zum Browserfenster, in dem `http://<app-name>.azurewebsites.net` geöffnet ist. Aktualisieren Sie die Seite. Nun sollte die geänderte Meldung angezeigt werden:

![Ausführen einer aktualisierten Python-Beispiel-App in Azure](../../media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code bietet leistungsstarke Erweiterungen für Python und Azure App Service, die die Bereitstellung von Python-Web-Apps in App Service vereinfachen. Weitere Informationen finden Sie unter [Bereitstellen von Python-Apps in App Service aus Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).