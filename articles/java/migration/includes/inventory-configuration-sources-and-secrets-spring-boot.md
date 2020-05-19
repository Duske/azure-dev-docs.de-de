---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 430f4fef9c512f91da4fd95f04320ddd127ea784
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990252"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Ermitteln des Bestands an Konfigurationsquellen und Geheimnissen

#### <a name="inventory-passwords-and-secure-strings"></a>Ermitteln des Bestands an Kennwörtern und sicheren Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien sowie alle Umgebungsvariablen in den Produktionsbereitstellungen auf Geheimniszeichenfolgen und Kennwörter. In einer Spring Boot-Anwendung befinden sich diese Zeichenfolgen in der Regel in der Datei *application.properties* oder *application.yml*.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]
