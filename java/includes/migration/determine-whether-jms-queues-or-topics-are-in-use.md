---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: b2458a80eccfcae24a3364208334cce3aedea861
ms.sourcegitcommit: 21ddeb9bd9abd419d143dc2ca8a7c821a1758cf9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128537"
---
### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>Ermitteln, ob JMS-Warteschlangen oder -Themen verwendet werden

Wenn für Ihre Anwendung JMS-Warteschlangen oder -Themen verwendet werden, müssen Sie diese zu einem extern gehosteten JMS-Server migrieren (z. B. zu Azure Service Bus, siehe [Verwenden von Service Bus als Nachrichtenbroker](/azure/service-bus-messaging/message-transfers-locks-settlement)).

Wenn beständige JMS-Speicher konfiguriert wurden, müssen Sie die zugehörige Konfiguration erfassen und nach dem Migrationsvorgang anwenden.
