---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 510218bcbed53dfb4383b6a906911790f7865975
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401444"
---
Es ermöglicht Ihnen die Registrierung und die Aufhebung der Registrierung von einem Benachrichtigungshub über den von Ihnen erstellten Back-End-Dienst. 

Wird eine Aktion angegeben, und befindet sich die Anwendung im Vordergrund, wird ein Alarm angezeigt. Andernfalls erscheinen die Benachrichtigungen in der Mitteilungszentrale.

> [!NOTE]
> Normalerweise würden Sie die Aktionen zur Registrierung und Aufhebung der Registrierung während des entsprechenden Zeitpunkts im Lebenszyklus der Anwendung (oder vielleicht als Teil Ihres Eindrucks beim ersten Ausführen der Anwendung) ohne explizite Eingaben zur Registrierung/Aufhebung der Registrierung des Benutzers durchführen. Dieses Beispiel erfordert jedoch eine explizite Benutzereingabe, damit diese Funktionalität leichter untersucht und getestet werden kann.
