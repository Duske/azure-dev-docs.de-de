---
author: yevster
ms.author: yebronsh
ms.date: 5/28/2020
ms.openlocfilehash: 70f54f911d97febf30b78888e6922e2d4ac3eb54
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512619"
---
#### <a name="identify-local-state"></a>Identifizieren des lokalen Zustands

In PaaS-Umgebungen ist es nicht gewährleistet, dass die Anwendung zu einem beliebigen Zeitpunkt genau ein Mal ausgeführt wird. Auch wenn Sie eine Anwendung so konfigurieren, dass Sie in einer einzelnen Instanz ausgeführt wird, kann in den folgenden Fällen eine doppelte Instanz erstellt werden:

* Die Anwendung muss aufgrund eines Fehlers oder eines Systemupdates auf einen physischen Host verschoben werden.
* Die Anwendung wird aktualisiert.

In diesen Fällen wird die ursprüngliche Instanz weiterhin ausgeführt, bis der Start der neuen Instanz abgeschlossen wurde. Dies besitzt die folgenden potenziell erheblichen Auswirkungen auf Ihre Anwendung:

* Für kein [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern) kann gewährleistet werden, dass es wirklich einzeln ist.
* Alle Daten, die nicht in einem externen Speicher persistent gespeichert wurden, gehen wahrscheinlich viel schneller verloren als auf einem einzelnen physischen Server oder einer VM.

Bevor Sie zu Azure Spring Cloud migrieren, stellen Sie sicher, dass Ihr Code keinen lokalen Zustand enthält, der nicht verloren gehen oder dupliziert werden darf. Wenn ein lokaler Zustand vorhanden ist, ändern Sie den Code so, dass dieser Zustand außerhalb der Anwendung gespeichert wird. Für die Cloud bereite Anwendungen speichern den Anwendungsstatus in der Regel in folgenden Speicherorten:

* [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-java-get-started)
* [Azure CosmosDB](/azure/cosmos-db/create-sql-api-java)
* Andere externe Datenbank, z. B. [Azure SQL](/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview), [Azure DB for MySQL](/azure/mysql/overview) oder [Azure DB for PostgreSQL](/azure/postgresql/overview).
