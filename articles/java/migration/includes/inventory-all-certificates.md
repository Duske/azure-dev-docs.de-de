---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673156"
---
### <a name="inventory-all-certificates"></a>Inventarisieren aller Zertifikate

Dokumentieren Sie alle Zertifikate, die für öffentliche SSL-Endpunkte verwendet werden. Sie können alle Zertifikate auf den Produktionsservern anzeigen, indem Sie den folgenden Befehl ausführen:

```bash
keytool -list -v -keystore <path to keystore>
```
