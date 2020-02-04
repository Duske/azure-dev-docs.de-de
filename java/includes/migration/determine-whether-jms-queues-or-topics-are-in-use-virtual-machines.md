---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: e36fae7adfda8c05e222151872b137fa600cdd97
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830768"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Ermitteln, ob JMS-Warteschlangen oder -Themen verwendet werden

Wenn Ihre Anwendung JMS-Warteschlangen oder -Themen nutzt, müssen Sie diese zu einem extern gehosteten JMS-Server migrieren. Azure Service Bus und das Advanced Message Queuing Protocol können bei Verwendung von JMS Teil einer gut geeigneten Migrationsstrategie sein. Weitere Informationen finden Sie unter [Verwenden von Java Message Service (JMS) mit Azure Service Bus und AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Wenn beständige JMS-Speicher konfiguriert wurden, müssen Sie die zugehörige Konfiguration erfassen und nach dem Migrationsvorgang anwenden.

Bei Verwendung von Oracle Message Broker können Sie diese Software zu virtuellen Azure-Computern migrieren und unverändert verwenden.
