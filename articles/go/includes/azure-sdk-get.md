---
ms.date: 09/05/2018
ms.technology: azure-cli
ms.openlocfilehash: af03f2efbb74e55dfcd14b6a2ac894a74eba321f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80319747"
---
Das [Azure SDK für Go](https://github.com/Azure/azure-sdk-for-go) ist mit Go-Version 1.8 und höheren Versionen kompatibel. Bei Umgebungen mit [Azure Stack-Profilen](/azure/azure-stack/user/azure-stack-version-profiles-go) ist mindestens Go-Version 1.9 erforderlich.
Falls Sie Go installieren müssen, befolgen Sie die [Installationsanweisungen für Go](https://golang.org/doc/install).

Sie können das Azure SDK für Go und seine Abhängigkeiten über `go get` herunterladen.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Schreiben Sie `Azure` in der URL unbedingt groß. Andernfalls können bei der Verwendung des SDK Importprobleme aufgrund der Groß-/Kleinschreibung auftreten. `Azure` muss außerdem in den Importanweisungen großgeschrieben werden.
