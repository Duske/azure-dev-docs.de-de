---
author: mnriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: 82cf9553654e1efc884542ecdd52d294688291df
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738138"
---
### <a name="inspect-the-deployment-architecture"></a>Untersuchen der Bereitstellungsarchitektur

#### <a name="document-hardware-requirements-for-each-service"></a>Dokumentieren der Hardwareanforderungen für die einzelnen Dienste

Dokumentieren Sie die folgenden Informationen für Ihre Spring Boot-Anwendung:

* Anzahl ausgeführter Instanzen
* Anzahl zugewiesener CPUs für die jeweilige Instanz
* Zugewiesener Arbeitsspeicher für die jeweilige Instanz

#### <a name="document-geo-replicationdistribution"></a>Dokumentieren von Georeplikation/Verteilung

Ermitteln Sie, ob die Spring Boot-Anwendungsinstanzen derzeit auf mehrere Regionen oder Rechenzentren verteilt sind. Dokumentieren Sie die Betriebszeitanforderungen/SLA für die zu migrierenden Anwendungen.
