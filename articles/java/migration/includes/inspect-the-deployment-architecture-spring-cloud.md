---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 7b2ad87dfe2e2c7358737ff02ec52b97b1332ae7
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990132"
---
### <a name="inspect-the-deployment-architecture"></a>Untersuchen der Bereitstellungsarchitektur

#### <a name="document-hardware-requirements-for-each-service"></a>Dokumentieren der Hardwareanforderungen für die einzelnen Dienste

Dokumentieren Sie für jeden der Spring Cloud-Dienste (mit Ausnahme des Konfigurationsservers, der Registrierung und des Gateways) die folgenden Informationen:

* Anzahl ausgeführter Instanzen
* Anzahl zugewiesener CPUs für die jeweilige Instanz
* Zugewiesener Arbeitsspeicher für die jeweilige Instanz

#### <a name="document-geo-replicationdistribution"></a>Dokumentieren von Georeplikation/Verteilung

Ermitteln Sie, ob die Spring Cloud-Anwendungen derzeit auf mehrere Regionen oder Rechenzentren verteilt sind. Dokumentieren Sie die Betriebszeit-/SLA-Anforderungen für die zu migrierenden Anwendungen.

#### <a name="identify-clients-that-bypass-the-service-registry"></a>Ermitteln von Clients, von denen die Dienstregistrierung umgangen wird

Ermitteln Sie alle Clientanwendungen, die einen der zu migrierenden Dienste aufrufen, ohne die Spring Cloud-Dienstregistrierung zu verwenden. Aufrufe dieser Art sind nach der Migration nicht mehr möglich. Aktualisieren Sie solche Clients vor der Migration für die Verwendung von [Spring Cloud OpenFeign](https://spring.io/projects/spring-cloud-openfeign).
