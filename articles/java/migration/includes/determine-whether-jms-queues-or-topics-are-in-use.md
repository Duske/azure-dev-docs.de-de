---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a521396292214fd4e68b4625a0e5f5bcfca8be92
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672916"
---
### <a name="determine-whether-java-message-service-jms-queues-or-topics-are-in-use"></a>Ermitteln, ob JMS-Warteschlangen oder -Themen (Java Message Service) verwendet werden

Wenn Ihre Anwendung JMS-Warteschlangen oder -Themen nutzt, müssen diese zu einem extern gehosteten JMS-Server migriert werden. Azure Service Bus und das Advanced Message Queuing Protocol (AMQP) können bei Verwendung von JMS eine hervorragende Migrationsstrategie sein. Weitere Informationen finden Sie unter [Verwenden von Java Message Service (JMS) mit Azure Service Bus und AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp).

Wenn beständige JMS-Speicher konfiguriert wurden, müssen Sie die zugehörige Konfiguration erfassen und nach dem Migrationsvorgang anwenden.
