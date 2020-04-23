---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672906"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Ermitteln, ob EJB-Zeitgeber verwendet werden

Wenn für Ihre Anwendung EJB-Zeitgeber genutzt werden, müssen Sie überprüfen, ob der EJB-Zeitgebercode von jeder WildFly-Instanz separat ausgelöst werden kann. Diese Überprüfung ist erforderlich, weil bei der Azure Kubernetes Service-Bereitstellung jeder EJB-Zeitgeber auf einer eigenen WildFly-Instanz ausgelöst wird.
