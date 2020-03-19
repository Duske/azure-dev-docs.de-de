---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f5995a3c5efc46bc58b446589ce089bf7d86b2ba
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897503"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Ermitteln, ob Ihre Anwendung einen Ressourcenadapter verwendet

Wenn für Ihre Anwendung ein Ressourcenadapter (RA) benötigt wird, muss dieser mit WildFly kompatibel sein. Ermitteln Sie, ob der RA für eine eigenständige Instanz von WildFly richtig funktioniert, indem Sie ihn auf dem Server bereitstellen und entsprechend konfigurieren. Falls der RA richtig funktioniert, müssen Sie die JARs dem Serverklassenpfad des Docker-Images hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.
