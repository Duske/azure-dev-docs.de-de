---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672146"
---
### <a name="inventory-certificates"></a>Inventarisieren von Zertifikaten

Dokumentieren Sie alle Zertifikate, die für öffentliche SSL-Endpunkte oder für die Kommunikation mit Back-End-Datenbanken und anderen Systemen verwendet werden. Sie können alle Zertifikate auf den Produktionsservern anzeigen, indem Sie den folgenden Befehl ausführen:

```bash
keytool -list -v -keystore <path to keystore>
```
