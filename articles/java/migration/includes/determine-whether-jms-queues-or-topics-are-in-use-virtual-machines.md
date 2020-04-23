---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 69bddfce67388d3392e6908f3ddf1af378b116cf
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673486"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Ermitteln, ob JMS-Warteschlangen oder -Themen (Java Message Service) verwendet werden

Wenn Ihre Anwendung JMS-Warteschlangen oder -Themen nutzt, müssen Sie diese zu einem extern gehosteten JMS-Server migrieren. Azure Service Bus und das Advanced Message Queuing Protocol können bei Verwendung von JMS Teil einer gut geeigneten Migrationsstrategie sein. Weitere Informationen finden Sie unter [Verwenden von Java Message Service (JMS) mit Azure Service Bus und AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Wenn beständige JMS-Speicher konfiguriert wurden, müssen Sie die zugehörige Konfiguration erfassen und nach dem Migrationsvorgang anwenden.

Bei Verwendung von Oracle Message Broker können Sie diese Software zu virtuellen Azure-Computern migrieren und unverändert verwenden.
