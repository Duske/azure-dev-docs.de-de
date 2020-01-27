---
author: yevster
ms.author: yebronsh
ms.topic: include
ms.date: 1/20/2020
ms.openlocfilehash: 4228d1db44ac16da1c05fe97d20a3491cc9c5f51
ms.sourcegitcommit: 3585b1b5148e0f8eb950037345bafe6a4f6be854
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288569"
---
### <a name="inventory-secrets"></a>Bestand: Geheimnisse

#### <a name="passwords-and-secure-strings"></a>Kennwörter und sichere Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimniszeichenfolgen und Kennwörter. Überprüfen Sie *server.xml* und *context.xml* in „$CATALINA_BASE/conf“. Unter Umständen finden Sie in Ihrer Anwendung auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen. Dies können Dateien wie *META-INF/context.xml* und für Spring Boot-Anwendungen *application.properties* oder *application.yml* sein.

#### <a name="certificates"></a>Zertifikate

Dokumentieren Sie alle Zertifikate, die für öffentliche SSL-Endpunkte verwendet werden. Sie können alle Zertifikate auf den Produktionsservern anzeigen, indem Sie den folgenden Befehl ausführen:

```bash
keytool -list -v -keystore <path to keystore>
```
