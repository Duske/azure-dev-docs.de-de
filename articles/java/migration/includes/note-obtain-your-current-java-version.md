---
author: yevster
ms.author: yebronsh
ms.date: 1/22/2020
ms.openlocfilehash: 2fc4e3b43a051103e7aea6aa77f66666ca454910
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672136"
---
<!-- Included in "### Switch to a supported platform" sections that have different (required) intro paragraphs. For example:

### Switch to a supported platform

App Service offers specific versions of Java SE. To ensure compatibility, migrate your application to one of the supported versions of in its current environment before you proceed with any of the remaining steps. Be sure to fully test the resulting configuration. Use the latest stable release of your Linux distribution in such tests.

-->

> [!NOTE]
> Diese Überprüfung ist besonders wichtig, wenn Ihr aktueller Server auf einem nicht unterstützten JDK (z. B. Oracle JDK oder IBM OpenJ9) ausgeführt wird.

Melden Sie sich an Ihrem Produktionsserver an, und führen Sie den folgenden Befehl aus, um Ihre aktuelle Java-Version zu ermitteln:

```bash
java -version
```

Laden Sie zum Ermitteln der aktuellen Version, die von Azure App Service verwendet wird, [Zulu 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) (bei Nutzung der Java 8 Runtime) bzw. [Zulu 11](https://www.azul.com/downloads/azure-only/zulu/?&version=java-11-lts&architecture=x86-64-bit&package=jdk) (bei Nutzung der Java 11 Runtime) herunter.
