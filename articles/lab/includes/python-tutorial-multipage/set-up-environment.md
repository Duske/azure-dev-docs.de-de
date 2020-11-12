---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 17790549afaee074625b8754080f15114e9aabef
ms.sourcegitcommit: 5f64710b2b0822e789c7f15acba5a3a257c033f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93405187"
---
1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installieren Sie <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 oder höher</a>.
1. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.

Öffnen Sie ein Terminalfenster, und überprüfen Sie, ob mindestens die Python-Version 3.6 installiert ist:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Vergewissern Sie sich, dass Sie mindestens Version 2.0.80 der Azure CLI verwenden:

```azurecli
az --version
```

Melden Sie sich anschließend über die CLI bei Azure an:

```azurecli
az login
```

Dieser Befehl öffnet einen Browser zum Erfassen Ihrer Anmeldeinformationen. Wenn der Befehl abgeschlossen ist, wird eine JSON-Ausgabe mit Informationen zu Ihren Abonnements angezeigt.

Nachdem Sie sich angemeldet haben, können Sie Azure-Befehle mit der Azure CLI ausführen, um Ressourcen in Ihrem Abonnement zu verwenden.
