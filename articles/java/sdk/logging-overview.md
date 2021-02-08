---
title: Konfigurieren der Protokollierung im Azure SDK für Java
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit der Protokollierung
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: bbb9733a21eabf5d13a698a0785908dcd997af45
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528485"
---
# <a name="configure-logging-in-the-azure-sdk-for-java"></a>Konfigurieren der Protokollierung im Azure SDK für Java

Dieser Artikel bietet eine Übersicht, wie Sie die Protokollierung in Anwendungen aktivieren, die das Azure SDK für Java verwenden. Die Azure-Clientbibliotheken für Java besitzen zwei Protokollierungsoptionen:

* Ein integriertes Protokollierungsframework für temporäres Debuggen.
* Unterstützung für die Protokollierung mithilfe der [SLF4J](https://www.slf4j.org/)-Schnittstelle.

Wir empfehlen, dass Sie SLF4J verwenden, da es im Java-Ökosystem gut etabliert und solide dokumentiert ist. Weitere Informationen finden Sie im [SLF4J-Benutzerhandbuch](https://www.slf4j.org/manual.html).

Dieser Artikel verlinkt mit anderen Artikeln, die viele der beliebten Java-Protokollierungsframeworks abdecken. Diese anderen Artikel stellen Konfigurationsbeispiele bereit und beschreiben, wie die Azure-Clientbibliotheken die Protokollierungsframeworks verwenden können.

Je nachdem, welche Protokollierungskonfiguration Sie verwenden, ist dieselbe Protokollausgabe in beiden Fällen verfügbar, da die gesamte Protokollierungsausgabe in den Azure-Clientbibliotheken für Java durch eine azure-core `ClientLogger`-Abstraktion weitergeleitet wird.

Im weiteren Verlauf dieses Artikels wird die Konfiguration aller verfügbaren Protokollierungsoptionen ausführlich erläutert.

## <a name="default-logger-for-temporary-debugging"></a>Standardprotokollierung (für temporäres Debuggen)

Wie bereits erwähnt, verwenden alle Azure-Clientbibliotheken SLF4J für die Protokollierung, aber es gibt eine als Fallback in die Azure-Clientbibliotheken für Java integrierte Standardprotokollierung. Diese Standardprotokollierung wird für Fälle bereitgestellt, in denen eine Anwendung bereitgestellt wurde und die Protokollierung erforderlich ist, es aber nicht möglich ist, die Anwendung mit einer enthaltenen SLF4J-Protokollierung erneut bereitzustellen. Zum Aktivieren dieser Protokollierung müssen Sie sich zunächst sicher sein, dass keine SLF4J-Protokollierung vorhanden ist (da diese sonst Vorrang hat), und dann die Umgebungsvariable `AZURE_LOG_LEVEL` festlegen. Die folgende Tabelle enthält die zulässigen Werte für diese Umgebungsvariable:

| Protokollebene              | Zulässige Werte für Umgebungsvariablen     |
|------------------------|-----------------------------------------|
| VERBOSE                | "verbose", "debug"                      |
| INFORMATION          | "info", "information", "informational"  |
| WARNING                | "warn", "warning"                       |
| ERROR                  | "err", "error"                          |

Nachdem die Umgebungsvariable festgelegt wurde, starten Sie die Anwendung neu, damit die Umgebungsvariable wirksam wird. Diese Protokollierung protokolliert in die Konsole und bietet nicht die erweiterten Anpassungsfunktionen einer SLF4J-Implementierung, z. B. Rollover und Protokollierung in eine Datei. Um die Protokollierung wieder zu deaktivieren, entfernen Sie einfach die Umgebungsvariable, und starten Sie die Anwendung neu.

## <a name="slf4j-logging"></a>SLF4J-Protokollierung

Standardmäßig sollten Sie die Protokollierung mithilfe eines SLF4J-unterstützten Protokollierungsframeworks konfigurieren. Schließen Sie zunächst eine relevante SLF4J-Protokollierungsimplementierung als Abhängigkeit von Ihrem Projekt ein. Weitere Informationen finden Sie unter [Deklarieren von Projektabhängigkeiten für die Protokollierung](http://www.slf4j.org/manual.html#projectDep) im SLF4J-Benutzerhandbuch. Als Nächstes konfigurieren Sie Ihre Protokollierung so, dass Sie gemäß Ihren Ansprüchen in Ihrer Umgebung funktioniert, also z. B. das Festlegen von Protokollebenen, das Konfigurieren, welche Klassen protokolliert oder nicht protokolliert werden usw. Einige Beispiele werden über die folgenden Links bereitgestellt, doch weitere Informationen finden Sie in der Dokumentation zu Ihrem ausgewählten Protokollierungsframework.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun gesehen haben, wie die Protokollierung im Azure SDK für Java funktioniert, sollten Sie die folgenden Links überprüfen, um eine Anleitung zu erhalten, wie Sie einige der beliebteren Frameworks für die Java-Protokollierung für die Zusammenarbeit mit SLF4J und den Java-Clientbibliotheken konfigurieren:

* [java.util.logging](logging-jul.md)
* [Logback](logging-logback.md)
* [Log4J](logging-log4j.md)
