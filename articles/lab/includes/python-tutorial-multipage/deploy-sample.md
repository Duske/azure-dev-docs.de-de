---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c8cf3e2f478265a81742093315e2a4041b2f0ed9
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405137"
---
Stellen Sie mithilfe des Befehls `az webapp up` den Code in Ihrem lokalen Ordner ( *python-docs-hello-world* ) bereit:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Wird der Befehl `az` nicht erkannt, sollten Sie sich vergewissern, dass die Azure CLI wie unter [Einrichten der anfänglichen Umgebung](../../quickstart-python-flask-multipage.yml?tutorial-step=1) beschrieben installiert wurde.
- Falls der Befehl `webapp` nicht erkannt wird, kann dies daran liegen, dass Sie Azure CLI-Version 2.0.80 oder höher verwenden. Wenn dies nicht der Fall ist, sollten Sie die [aktuelle Version installieren](/cli/azure/install-azure-cli).
- Ersetzen Sie `<app_name>` durch einen Namen, der innerhalb von Azure eindeutig ist ( *gültige Zeichen: `a-z`, `0-9` und `-`* ). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.
- Mit dem Argument `--sku F1` wird die Web-App im Tarif „Free“ erstellt. Lassen Sie dieses Argument weg, um einen schnelleren Premium-Tarif zu verwenden. Dieser verursacht jedoch stündlich Kosten.
- Optional können Sie das Argument `--location <location-name>` einfügen, wobei `<location_name>` eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) ausführen.
- Wird der Fehler „Could not auto-detect the runtime stack of your app“ (Der Runtimestapel der App konnte nicht automatisch erkannt werden.) angezeigt, sollten Sie sich vergewissern, dass der Befehl im Ordner *python-docs-hello-world* (Flask) bzw. *python-docs-hello-django* (Django) ausgeführt wird, der die Datei *requirements.txt* enthält. (Siehe [Behandeln von Problemen bei der automatischen Erkennung mit „az webapp up“](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub))

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. Bei der Ausführung werden Nachrichten zur Erstellung der Ressourcengruppe, des App Service-Plans und der Hosting-App, zur Konfiguration der Protokollierung und zur anschließenden ZIP-Bereitstellung bereitgestellt. Anschließend wird die Meldung „You can launch the app at http://&lt;app-name&gt;.azurewebsites.net“ (Sie können die App unter http://<App-Name>.azurewebsites.net starten.) angezeigt. Dabei handelt es sich um die URL der App in Azure.

![Beispielausgabe des Befehls „az webapp up“](../../media/quickstart-python/az-webapp-up-output.png)

[!include [az webapp up command note](../app-service-web-az-webapp-up-note.md)]