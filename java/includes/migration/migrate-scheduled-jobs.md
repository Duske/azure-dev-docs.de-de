---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 1015c179c0f93485decd77bd89a3ceec8833652e
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76825878"
---
### <a name="migrate-scheduled-jobs"></a>Migrieren von geplanten Aufträgen

Erwägen Sie für die Ausführung von geplanten Aufträgen in Azure die Verwendung eines [Zeitgebertriggers für Azure Functions](/azure/azure-functions/functions-bindings-timer). Hierbei ist es nicht erforderlich, den eigentlichen Auftragscode in eine Funktion zu migrieren. Für die Funktion kann einfach eine URL in Ihrer Anwendung aufgerufen werden, um den Auftrag auszulösen. Wenn Auftragsausführungen dieser Art dynamisch aufgerufen bzw. zentral nachverfolgt werden müssen, sollten Sie die Nutzung von [Spring Batch](https://spring.io/projects/spring-batch) erwägen.

Alternativ können Sie eine Logik-App mit einem Wiederholungstrigger erstellen, um die URL aufzurufen, ohne außerhalb Ihrer Anwendung Code schreiben zu müssen. Weitere Informationen finden Sie unter [Übersicht: Was ist Azure Logic Apps?](/azure/logic-apps/logic-apps-overview) und [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Serientrigger in Azure Logic Apps](/azure/connectors/connectors-native-recurrence).

> [!NOTE]
> Zur Verhinderung einer missbräuchlichen Nutzung müssen Sie ggf. sicherstellen, dass für den Endpunkt zum Aufrufen des Auftrags Anmeldeinformationen benötigt werden. In diesem Fall müssen die Anmeldeinformationen von der Triggerfunktion bereitgestellt werden.
