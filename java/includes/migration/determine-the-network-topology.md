---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 9403614c7ae2990a35fcbcce6d2e104f87724da5
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830918"
---
### <a name="determine-the-network-topology"></a>Ermitteln der Netzwerktopologie

Die aktuelle Gruppe mit Marketplace-Angeboten ist ein Startpunkt für Ihre Migration. Falls durch das Angebot Bereiche Ihrer Architektur, die Sie migrieren müssen, nicht abgedeckt sind, müssen Sie die Netzwerktopologie Ihrer vorhandenen Bereitstellung erfassen und in Azure reproduzieren. Dies gilt auch, wenn Sie das grundlegende Angebot mit einer der Lösungsvorlagen in Anspruch genommen haben.

Dies ist ein sehr umfangreiches Thema, aber unter den folgenden Referenzthemen erhalten Sie einige Informationen zur Vorgehensweise bei der Migration:

* In dieser Referenz werden die allgemeinen Themen aufgelistet, die für die Migration der Netzwerktopologie zu Azure relevant sind: [Leitfaden für schnelle Bereitstellung](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/deploying.html#GUID-E0BE4A3E-44CD-4C95-9540-7A850BF02F6A):
* In dieser Referenz werden wichtige Punkte zum Clustering beschrieben, die sich auf die Netzwerktopologie auswirken: [WebLogic Server-Clustering](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/clustering.html#GUID-E39A18C2-B990-485F-BFB1-0549250FABFE):
* Da es sich bei Datenquellen in einem WebLogic-System um separate Server handelt, müssen Sie sie bei der Analyse der Netzwerktopologie berücksichtigen. [WebLogic Server-Datenquellen](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html#GUID-9FD5F552-B2E4-4FEC-8C10-503A08764B52):
* Messagingquellen sind ebenfalls separate Server. [WebLogic Server-Messaging](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jms.html#GUID-3B5F647D-E001-413B-AC6A-1E103BDBA93F)
* Der Lastenausgleich ist eine grundlegende Anforderung. In dieser Referenz wird die WebLogic Server-Seite des Lastenausgleichs behandelt: [Lastenausgleich in einem Cluster](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/load_balancing.html#GUID-B8F6DE4B-1AAC-428B-878B-BFDCE161C054).
