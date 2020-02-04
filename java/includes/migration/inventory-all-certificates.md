---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830798"
---
### <a name="inventory-all-certificates"></a>Inventarisieren aller Zertifikate

Dokumentieren Sie alle Zertifikate, die für öffentliche SSL-Endpunkte verwendet werden. Sie können alle Zertifikate auf den Produktionsservern anzeigen, indem Sie den folgenden Befehl ausführen:

```bash
keytool -list -v -keystore <path to keystore>
```
