---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f5995a3c5efc46bc58b446589ce089bf7d86b2ba
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673076"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Ermitteln, ob Ihre Anwendung einen Ressourcenadapter verwendet

Wenn für Ihre Anwendung ein Ressourcenadapter (RA) benötigt wird, muss dieser mit WildFly kompatibel sein. Ermitteln Sie, ob der RA für eine eigenständige Instanz von WildFly richtig funktioniert, indem Sie ihn auf dem Server bereitstellen und entsprechend konfigurieren. Falls der RA richtig funktioniert, müssen Sie die JARs dem Serverklassenpfad des Docker-Images hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.
