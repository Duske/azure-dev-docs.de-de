---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 3ff76d977c231b4b238f9dbec7f3bfaddfe5e484
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507600"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Sicherstellen der Konsolenprotokollierung und Konfigurieren von Diagnoseeinstellungen

Konfigurieren Sie Ihre Protokollierung so, dass alle Ausgaben nicht in Dateien, sondern an die Konsole weitergeleitet werden.

Nach der Bereitstellung einer Anwendung in Azure Spring Cloud können Sie [eine Diagnoseeinstellung hinzufügen](/azure/spring-cloud/diagnostic-services), um die Erfassung protokollierter Ereignisse zu ermöglichen (beispielsweise durch Azure Monitor Log Analytics).

#### <a name="logstashelk-stack"></a>LogStash/ELK-Stapel

Wenn Sie LogStash bzw. den ELK-Stapel für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an einen [Azure Event Hub](/azure/event-hubs/) gestreamt wird. Verwenden Sie dann das [Event Hub-Plug-In für LogStash](https://github.com/logstash-plugins/logstash-input-azure_event_hubs), um protokollierte Ereignisse in LogStash zu erfassen.

#### <a name="splunk"></a>Splunk

Wenn Sie Splunk für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an [Azure Blob Storage](/azure/storage/blobs/) gestreamt wird. Verwenden Sie dann das [Splunk-Add-On für Microsoft Cloud Services](https://splunkbase.splunk.com/app/3757/), um protokollierte Ereignisse in Splunk zu erfassen.
