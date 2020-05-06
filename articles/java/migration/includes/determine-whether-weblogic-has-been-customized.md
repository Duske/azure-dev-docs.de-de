---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673406"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Ermitteln, ob WebLogic angepasst wurde

Ermitteln Sie, welche der folgenden Anpassungen vorgenommen wurden, und erfassen Sie sie.

* Wurden die Startskripts geändert? Zu diesen Skripts gehören *setDomainEnv*, *commEnv*, *startWebLogic* und *stopWebLogic*.
* Werden an JVM spezifische Parameter übergeben?
* Werden dem Klassenpfad des Servers JAR-Dateien hinzugefügt?
