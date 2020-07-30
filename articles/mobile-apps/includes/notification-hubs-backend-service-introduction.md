---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 20a8fa10a27a41621c2b08839682fefbd023ee2e
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401448"
---
Ein [ASP.NET Core-Web-API](https://dotnet.microsoft.com/apps/aspnet/apis)-Backend wird verwendet, um die [Geräteregistrierung](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) für den Client unter Verwendung des neuesten und besten [Installationsansatzes](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) zu verarbeiten. Der Dienst senden auch plattformübergreifend Pushbenachrichtigungen. 

Diese Vorgänge werden mithilfe des [Notification Hubs SDKs für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) verarbeitet. Weitere Details zum allgemeinen Ansatz finden Sie in der Dokumentation zur [Registrierung Ihres App-Back-Ends](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
