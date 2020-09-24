---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 9fa87bf3d0de64271a9ffb0e7e8fbff3cd1afd12
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738490"
---
### <a name="determine-whether-your-application-uses-a-resource-adapter"></a>Ermitteln, ob Ihre Anwendung einen Ressourcenadapter verwendet

Wenn für Ihre Anwendung ein Ressourcenadapter (RA) benötigt wird, muss dieser mit WildFly kompatibel sein. Ermitteln Sie, ob der RA für eine eigenständige Instanz von WildFly richtig funktioniert, indem Sie ihn auf dem Server bereitstellen und entsprechend konfigurieren. Falls der RA richtig funktioniert, müssen Sie die JARs dem Serverklassenpfad des Docker-Images hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.
