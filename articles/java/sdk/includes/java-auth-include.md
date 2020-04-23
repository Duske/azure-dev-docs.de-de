---
ms.openlocfilehash: 0a9b06932baa51c3cf003a4485a3a25261ffe91d
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81671866"
---
Erstellen Sie eine [Authentifizierungsdatei](../java-sdk-azure-authenticate.md#mgmt-file), und exportieren Sie die Umgebungsvariable `AZURE_AUTH_LOCATION` in der Befehlszeile mit dem vollständigen Pfad zur Datei.

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azure.auth
```

Mit der Authentifizierungsdatei wird das Eingangspunktobjekt `Azure` konfiguriert, das von den Verwaltungsbibliotheken zum Definieren, Erstellen und Konfigurieren von Azure-Ressourcen verwendet wird.

```java
// pull in the location of the security file from the environment 
final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));

Azure azure = Azure
        .configure()
        .withLogLevel(LogLevel.NONE)
        .authenticate(credFile)
        .withDefaultSubscription();
```

[Weitere Informationen](../java-sdk-azure-authenticate.md#mgmt-auth) zu Authentifizierungsoptionen bei Verwendung der Azure-Verwaltungsbibliotheken für Java.