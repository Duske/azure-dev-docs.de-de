---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e4370e6db3589e6050395fe806541505fa3eb8bb
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738134"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Ermitteln, ob für Ihre Anwendung geplante Aufträge benötigt werden

Für Azure Kubernetes Service sollten geplante Aufträge, z. B. Quartz Scheduler-Aufgaben oder Unix-Cron-Aufträge, NICHT verwendet werden. Azure Kubernetes Service hindert Sie nicht an der Bereitstellung einer Anwendung, die intern geplante Aufgaben enthält. Wenn Ihre Anwendung aber horizontal hochskaliert wird, wird derselbe geplante Auftrag unter Umständen mehrmals pro geplantem Zeitraum ausgeführt. Diese Situation kann unerwünschte Konsequenzen haben.

Definieren Sie zum Ausführen von geplanten Aufträgen in Ihrem AKS-Cluster je nach Bedarf Kubernetes-Cron-Aufträge. Weitere Informationen finden Sie unter [Ausführen von automatisierten Aufgaben per Cron-Auftrag](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
