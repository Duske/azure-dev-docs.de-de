---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743306"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) ist ein Nachrichtenbroker für die [Unternehmensintegration](https://azure.microsoft.com/product-categories/integration/). Service Bus unterstützt zwei Kommunikationstypen: Warteschlangen und Themen. 

Warteschlangen unterstützen die asynchrone Kommunikation zwischen Anwendungen. Eine App sendet Nachrichten an eine Warteschlange, die die Nachrichten speichert. Die empfangende Anwendung stellt dann eine Verbindung mit der Warteschlange her und liest die Nachrichten.

Themen unterstützen das Veröffentlichen-Abonnieren-Muster, das eine 1:n-Beziehung zwischen dem Absender der Nachricht und den Empfängern der Nachricht ermöglicht.