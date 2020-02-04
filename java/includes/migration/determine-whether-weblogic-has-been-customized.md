---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830848"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Ermitteln, ob WebLogic angepasst wurde

Ermitteln Sie, welche der folgenden Anpassungen vorgenommen wurden, und erfassen Sie sie.

* Wurden die Startskripts geändert? Zu diesen Skripts gehören *setDomainEnv*, *commEnv*, *startWebLogic* und *stopWebLogic*.
* Werden an JVM spezifische Parameter übergeben?
* Werden dem Klassenpfad des Servers JAR-Dateien hinzugefügt?
