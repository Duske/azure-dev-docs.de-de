---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: b53308d4a9db52a25665d0daa74be678e726c499
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990182"
---
### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Sicherstellen der Konsolenprotokollierung und Konfigurieren von Diagnoseeinstellungen

Konfigurieren Sie Ihre Protokollierung so, dass die Protokollierungsinformationen aller Anwendungen in Azure Spring Cloud nicht in Dateien, sondern in der Konsole ausgegeben werden.

Nach der Bereitstellung einer Anwendung in Azure Spring Cloud können Sie [eine Diagnoseeinstellung hinzufügen](/azure/spring-cloud/diagnostic-services), um die Erfassung protokollierter Ereignisse zu ermöglichen (beispielsweise durch Azure Monitor Log Analytics).

#### <a name="logstashelk-stack"></a>LogStash/ELK-Stapel

Wenn Sie LogStash bzw. den ELK-Stapel für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an einen [Azure Event Hub](/azure/event-hubs/) gestreamt wird. Verwenden Sie dann das [Event Hub-Plug-In für LogStash](https://github.com/logstash-plugins/logstash-input-azure_event_hubs), um protokollierte Ereignisse in LogStash zu erfassen.

#### <a name="splunk"></a>Splunk

Wenn Sie Splunk für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an [Azure Blob Storage](/azure/storage/blobs/) gestreamt wird. Verwenden Sie dann das [Splunk-Add-On für Microsoft Cloud Services](https://splunkbase.splunk.com/app/3757/), um protokollierte Ereignisse in Splunk zu erfassen.
