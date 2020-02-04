---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 4c20feba0a91d10846963f8697457735e5932c20
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830728"
---
### <a name="determine-whether-session-replication-is-used"></a>Ermitteln, ob die Sitzungsreplikation verwendet wird

Wenn für Ihre Anwendung die Sitzungsreplikation benötigt wird, haben Sie drei Optionen (mit oder ohne Oracle Coherence*Web):

1. Coherence*Web kann auf den virtuellen Azure-Computern neben WebLogic Server ausgeführt werden, aber Sie müssen diese Option manuell konfigurieren, nachdem Sie das Angebot bereitgestellt haben. Falls Sie Coherence allein nutzen, können Sie die Anwendung auch auf einem virtuellen Azure-Computer ausführen, aber Sie müssen diese Option nach der Bereitstellung des Angebots manuell konfigurieren.
2. Gestalten Sie Ihre Anwendung so um, dass eine Datenbank für die Sitzungsverwaltung verwendet wird.
3. Gestalten Sie Ihre Anwendung so um, dass die Sitzung für den Azure Redis-Dienst externalisiert wird. Weitere Informationen finden Sie unter [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

Bei all diesen Optionen ist es ratsam, sich damit vertraut zu machen, wie von WebLogic die Replikation des HTTP-Sitzungsstatus durchgeführt wird. Weitere Informationen finden Sie in der Oracle-Dokumentation unter [Replikation des HTTP-Sitzungsstatus](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD).
