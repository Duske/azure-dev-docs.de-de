---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 4d2df28d5c752d20454c28a6d4a53698aa7ff5b6
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673016"
---
### <a name="determine-whether-your-application-relies-on-scheduled-jobs"></a>Ermitteln, ob für Ihre Anwendung geplante Aufträge benötigt werden

Für Azure Kubernetes Service sollten geplante Aufträge, z. B. Quartz Scheduler-Aufgaben oder Unix-Cron-Aufträge, NICHT verwendet werden. Azure Kubernetes Service hindert Sie nicht an der Bereitstellung einer Anwendung, die intern geplante Aufgaben enthält. Wenn Ihre Anwendung aber horizontal hochskaliert wird, wird derselbe geplante Auftrag unter Umständen mehrmals pro geplantem Zeitraum ausgeführt. Diese Situation kann unerwünschte Konsequenzen haben.

Definieren Sie zum Ausführen von geplanten Aufträgen in Ihrem AKS-Cluster je nach Bedarf Kubernetes-Cron-Aufträge. Weitere Informationen finden Sie unter [Ausführen von automatisierten Aufgaben per Cron-Auftrag](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).
