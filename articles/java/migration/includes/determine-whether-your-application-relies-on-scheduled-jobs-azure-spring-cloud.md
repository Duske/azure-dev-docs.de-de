---
author: yevster
ms.author: yebronsh
ms.date: 7/21/2020
ms.openlocfilehash: b1acff280bddea700b0382609c1003129b05dad2
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062643"
---
#### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Ermitteln, ob für Ihre Anwendung geplante Aufträge benötigt werden

Für Azure Spring Cloud sollten geplante Aufträge, z. B. Quartz Scheduler-Aufgaben oder Unix-Cron-Aufträge, NICHT verwendet werden. Azure Spring Cloud hindert Sie nicht an der Bereitstellung einer Anwendung, die intern geplante Aufgaben enthält. Wenn Ihre Anwendung aber horizontal hochskaliert wird, wird derselbe geplante Auftrag unter Umständen mehrmals pro geplantem Zeitraum ausgeführt. Diese Situation kann unerwünschte Konsequenzen haben.

Inventarisieren Sie alle geplanten Aufgaben, die auf Produktionsservern ausgeführt werden, innerhalb oder außerhalb Ihres Anwendungscodes.
