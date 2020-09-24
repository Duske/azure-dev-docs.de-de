---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c77a5ae60807cf25415ab86dab9d9891abab13f9
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738522"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Ermitteln, ob EJB-Zeitgeber verwendet werden

Wenn für Ihre Anwendung EJB-Zeitgeber genutzt werden, müssen Sie überprüfen, ob der EJB-Zeitgebercode von jeder WildFly-Instanz separat ausgelöst werden kann. Diese Überprüfung ist erforderlich, weil bei der Azure Kubernetes Service-Bereitstellung jeder EJB-Zeitgeber auf einer eigenen WildFly-Instanz ausgelöst wird.
