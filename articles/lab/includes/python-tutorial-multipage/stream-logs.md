---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 5868697895ad067065627cca99f604d531dcb173
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405311"
---
Sie können auf die Konsolenprotokolle zugreifen, die innerhalb der App und des Containers, in dem sie ausgeführt wird, generiert wurden. Protokolle enthalten alle Ausgaben, die mit `print`-Anweisungen generiert werden.

Führen Sie zum Streamen von Protokollen den Befehl [az webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) aus:

```azurecli
az webapp log tail
```

Sie können auch den Parameter `--logs` mit dem Befehl `az webapp up` einfügen, damit der Protokolldatenstrom bei der Bereitstellung automatisch geöffnet wird.

Aktualisieren Sie die App im Browser, um Konsolenprotokolle zu erstellen, die Meldungen mit einer Beschreibung der HTTP-Anforderungen an die App enthalten. Wenn nicht sofort eine Ausgabe angezeigt wird, versuchen Sie es nach 30 Sekunden erneut.

Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

Sie können im Terminal jederzeit **STRG**+**C** drücken, um das Protokollstreaming zu beenden.
