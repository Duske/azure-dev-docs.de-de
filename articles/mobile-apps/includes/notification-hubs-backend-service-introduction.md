---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: 98e39efca4ddaacb0e46bf5e42bd2df496529beb
ms.sourcegitcommit: e97cb81a245ce7dcabeac3260abc3db7c30edd79
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91493149"
---
Ein [ASP.NET Core-Web-API](https://dotnet.microsoft.com/apps/aspnet/apis)-Backend wird verwendet, um die [Geräteregistrierung](/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) für den Client unter Verwendung des neuesten und besten [Installationsansatzes](/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations) zu verarbeiten. Der Dienst senden auch plattformübergreifend Pushbenachrichtigungen. 

Diese Vorgänge werden mithilfe des [Notification Hubs SDKs für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) verarbeitet. Weitere Details zum allgemeinen Ansatz finden Sie in der Dokumentation zur [Registrierung Ihres App-Back-Ends](/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).