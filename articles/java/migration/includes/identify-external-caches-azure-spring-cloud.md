---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: f2bc0442aafcfffc963d4e5a6da18085d9205a22
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990162"
---
#### <a name="identify-external-caches"></a>Identifizieren von externen Caches

Ermitteln Sie alle verwendeten externen Caches. Redis wird häufig über Spring Data Redis verwendet. Informationen zur Konfiguration finden Sie in der [Spring Data Redis](https://spring.io/projects/spring-data-redis)-Dokumentation.

Ermitteln Sie, ob Sitzungsdaten über [Spring Session](https://spring.io/projects/spring-session) zwischengespeichert werden. Suchen Sie dazu nach der entsprechenden Konfiguration (in [Java](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-jc) oder [XML](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-xml)).
