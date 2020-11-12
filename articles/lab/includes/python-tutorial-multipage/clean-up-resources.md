---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: cc541400419bf3269a7bb31b36fab788271539f4
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405148"
---
In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Die Ressourcengruppe hat abhängig von Ihrem Standort einen Namen wie appsvc_rg_Linux_CentralUS. Wenn Sie eine andere App Service-SKU als den kostenlosen Tarif „F1“ verwenden, fallen laufende Kosten für diese Ressourcen an (siehe [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Wenn Sie diese Ressourcen voraussichtlich nicht mehr benötigen, löschen Sie die Ressourcengruppen mit folgendem Befehl:

```azurecli
az group delete --no-wait
```

Der Befehl verwendet den Ressourcengruppennamen, der in der Datei *.azure/config* zwischengespeichert ist.

Mit dem Argument `--no-wait` kann für den Befehl die Rückgabe erfolgen, bevor der Vorgang abgeschlossen ist.