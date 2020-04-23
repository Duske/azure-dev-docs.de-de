---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: cc634f89170f4db6f961da91e3eb3abe7393539d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673596"
---
### <a name="validate-that-the-supported-java-version-works-correctly"></a>Überprüfen, ob die unterstützte Java-Version richtig funktioniert

Für alle Migrationspfade für WebLogic zu Azure wird eine bestimmte Java-Version benötigt, die für jeden Pfad variiert. Sie müssen überprüfen, ob Ihre Anwendung mit dieser unterstützten Version richtig ausgeführt werden kann.

Melden Sie sich an Ihrem Produktionsserver an, und führen Sie diesen Befehl aus, um Ihre aktuelle Version zu ermitteln:

```bash
java -version
```

> [!NOTE]
> Beim Migrieren zu WebLogic auf virtuellen Azure-Computern werden die Anforderungen für die spezifischen Java-Versionen anhand der vorinstallierten Java-Version auf den virtuellen Computern bestimmt.
