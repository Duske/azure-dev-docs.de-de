---
author: mriem
ms.author: manriem
ms.date: 5/8/2020
ms.openlocfilehash: f99653f3ce30c629c2c11127089445a4c3072a69
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990242"
---
### <a name="inspect-the-deployment-architecture"></a>Untersuchen der Bereitstellungsarchitektur

#### <a name="document-hardware-requirements-for-each-service"></a>Dokumentieren der Hardwareanforderungen für die einzelnen Dienste

Dokumentieren Sie die folgenden Informationen für Ihre Spring Boot-Anwendung:

* Anzahl ausgeführter Instanzen
* Anzahl zugewiesener CPUs für die jeweilige Instanz
* Zugewiesener Arbeitsspeicher für die jeweilige Instanz

#### <a name="document-geo-replicationdistribution"></a>Dokumentieren von Georeplikation/Verteilung

Ermitteln Sie, ob die Spring Boot-Anwendungsinstanzen derzeit auf mehrere Regionen oder Rechenzentren verteilt sind. Dokumentieren Sie die Betriebszeitanforderungen/SLA für die zu migrierenden Anwendungen.
