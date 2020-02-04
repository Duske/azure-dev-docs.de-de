---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: e37d0337361ce742ce7f7994ab634e63bc4b2ead
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76825819"
---
### <a name="inventory-secrets"></a>Bestand: Geheimnisse

#### <a name="passwords-and-secure-strings"></a>Kennwörter und sichere Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimniszeichenfolgen und Kennwörter. Überprüfen Sie unbedingt *server.xml* und *context.xml* in *$CATALINA_BASE/conf*. Unter Umständen finden Sie in Ihrer Anwendung auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen. Dies können Dateien wie *META-INF/context.xml* und für Spring Boot-Anwendungen *application.properties* oder *application.yml* sein.
