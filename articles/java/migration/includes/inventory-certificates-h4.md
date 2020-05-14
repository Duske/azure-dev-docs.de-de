---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 975c6ce1011f76a34f33ec6093f70f281eefa6c3
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990212"
---
#### <a name="inventory-certificates"></a>Inventarisieren von Zertifikaten

Dokumentieren Sie alle Zertifikate, die für öffentliche SSL-Endpunkte oder für die Kommunikation mit Back-End-Datenbanken und anderen Systemen verwendet werden. Sie können alle Zertifikate auf den Produktionsservern anzeigen, indem Sie den folgenden Befehl ausführen:

```bash
keytool -list -v -keystore <path to keystore>
```
