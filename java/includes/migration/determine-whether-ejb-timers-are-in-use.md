---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897469"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Ermitteln, ob EJB-Zeitgeber verwendet werden

Wenn für Ihre Anwendung EJB-Zeitgeber genutzt werden, müssen Sie überprüfen, ob der EJB-Zeitgebercode von jeder WildFly-Instanz separat ausgelöst werden kann. Diese Überprüfung ist erforderlich, weil bei der Azure Kubernetes Service-Bereitstellung jeder EJB-Zeitgeber auf einer eigenen WildFly-Instanz ausgelöst wird.
