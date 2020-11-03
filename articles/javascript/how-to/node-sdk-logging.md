---
title: Protokollierung, Metriken und Telemetrie in Azure
description: Hier finden Sie Informationen zu Protokollierungsoptionen in Azure.
ms.topic: how-to
ms.date: 10/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: 32abae7005cea4076c000ec92039df4d27a0ec10
ms.sourcegitcommit: 03240a3beece1407a140656d246e11856dc72ac7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92493926"
---
# <a name="logging-metrics-and-telemetry-in-azure"></a>Protokollierung, Metriken und Telemetrie in Azure 

Für Azure stehen mehrere Protokollierungs-, Metrik- und Telemetrieoptionen zur Verfügung. Machen Sie sich mit den Optionen vertraut, um das gewünschte Tool oder den gewünschten Dienst zu finden:

* [Ressourcenmetriken:](#resource-metrics-provided-by-azure-services) Bei Verwendung von Azure-Diensten überwacht Azure Ihre individuellen Ressourcen und erfasst Metriken.  
* [Benutzerdefinierte Metriken:](#custom-logging-to-azure) Verwenden Sie diese Option, wenn Ihre Anwendung (lokal, cloudbasiert oder hybrid) Informationen protokollieren muss.
* [Azure SDK-Clientbibliotheken:](#azure-sdk-client-library-logging) Verwenden Sie diese Option, um die bereits in Azure-Clientbibliotheken integrierte Protokollierung anzuzeigen.

## <a name="resource-metrics-provided-by-azure-services"></a>Von Azure-Diensten bereitgestellte Ressourcenmetriken

[Azure Monitor](/azure/azure-monitor/overview) maximiert die Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste durch die Bereitstellung einer umfassenden Lösung für das Sammeln, Analysieren und Reagieren auf Telemetriedaten aus Ihren cloudbasierten und lokalen Umgebungen.

Die Metriken stehen im Azure-Portal innerhalb Ihrer Ressource zur Verfügung. 

:::image type="content" source="../media/logging-metrics/azure-resource-metrics-portal.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::

Sobald Daten überwacht werden, können Sie sie entweder mithilfe allgemeiner Abfragen über das Azure-Portal abfragen oder [Kusto-Abfragen](/azure/data-explorer/kusto/query/) erstellen. 

## <a name="custom-logging-to-azure"></a>Benutzerdefinierte Protokollierung in Azure

Verwenden Sie [Application Insights](/azure/azure-monitor/app/app-insights-overview) von Azure Monitor für [Serverszenarien](/azure/azure-monitor/app/nodejs) (Node.js) und [Clientszenarien](/azure/azure-monitor/app/javascript) (Browser):

* Server: Protokollierung über Node.js mit [Application Insights](/azure/azure-monitor/app/app-insights-overview) - [npm-Paket](https://www.npmjs.com/package/applicationinsights)
* Client: Protokollierung über Ihren Clientcode: [npm-Paket](https://www.npmjs.com/package/@microsoft/applicationinsights-web)
* Container und virtuelle Computer: Protokollierung über Ihren [Kubernetes-Cluster](/azure/azure-monitor/insights/container-insights-overview) oder über [Azure Virtual Machines](/azure/azure-monitor/insights/vminsights-overview)
 
## <a name="azure-sdk-client-library-logging"></a>Protokollierung der Azure SDK-Clientbibliothek

In der Regel sollte es nicht nötig sein, auf die interne Protokollierung der Azure SDK-Clientbibliothek zuzugreifen. Die Azure-Clientkernbibliothek für die Protokollierung wurde für die Verwendung durch Azure-Dienste konzipiert. 

[NPM-Paket](https://www.npmjs.com/package/@azure/logger) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)

### <a name="enable-logging"></a>Aktivieren der Protokollierung

Sie können die Protokollierung in der gesamten Anwendung mithilfe einer einzelnen Umgebungsvariablen aktivieren oder die Protokollierung für einen Teil der Anwendung dynamisch konfigurieren. In diesem Artikel werden die wichtigsten Konzepte des Protokollierungspakets sowie die Vorgehensweise zur Aktivierung der Protokollierung mit den folgenden Methoden erläutert:

- Legen Sie die Umgebungsvariable `AZURE_LOG_LEVEL` fest.
- Rufen Sie das aus der Protokollierungsbibliothek importierte `setLogLevel`-Element auf.
- Rufen Sie `enable()` für bestimmte Protokollierungen auf.

> [!NOTE]
> Dieser Artikel gilt für Clientbibliotheken, die die aktuellen Versionen des Azure SDK verwenden. Informationen dazu, ob eine Bibliothek unterstützt wird, finden Sie in der Liste mit den [neuesten Azure SDK-Releases](https://azure.github.io/azure-sdk/releases/latest/index.html#javascript). Wenn Ihre Anwendung eine ältere Version der Azure SDK-Clientbibliotheken verwendet, finden Sie entsprechende Anweisungen in der jeweiligen Dienstdokumentation.

### <a name="log-levels"></a>Protokollebenen

Das `@azure/logger`-Paket unterstützt die folgenden Protokollebenen (absteigend nach Ausführlichkeit sortiert):

- Ausführlich
- info
- warning
- error

Wenn Sie eine Protokollebene festlegen (entweder programmgesteuert oder über die Umgebungsvariable `AZURE_LOG_LEVEL`), werden alle Protokolle ausgegeben, deren Protokollebene der ausgewählten Ebene entspricht oder darunter liegt. Wenn Sie die Protokollebene beispielsweise auf `warning` festlegen, werden alle Protokolle mit der Ebene `warning` oder `error` ausgegeben.

### <a name="install-the-logger-package"></a>Installieren des Protokollierungspakets

Das Azure SDK für die JavaScript-Protokollierungsbibliothek wird als [npm](https://www.npmjs.com/)-Paket bereitgestellt. Verwenden Sie npm zum Installieren des Pakets `@azure/logger`:

```cmd
npm install @azure/logger
```

### <a name="set-the-logging-environment-variable"></a>Festlegen der Umgebungsvariablen für die Protokollierung

Sie können die einzelne Umgebungsvariable `AZURE_LOG_LEVEL` verwenden, um die Protokollierung in Ihrer Anwendung zu aktivieren. Die Protokolle werden in stderr ausgegeben. Nach dem Festlegen der Umgebungsvariable müssen Sie die Anwendung neu starten, um mit dem Erstellen von Protokollen zu beginnen.

In diesem Bash-Beispiel wird die Protokollebene auf „verbose“ festgelegt:

```bash
export AZURE_LOG_LEVEL="verbose"
```

In diesem Beispiel wird PowerShell verwendet:

```powershell
$env:AZURE_LOG_LEVEL="verbose"
```

In diesem Beispiel wird CMD verwendet:

```cmd
set AZURE_LOG_LEVEL="verbose"
```

### <a name="configure-dynamic-logging"></a>Konfigurieren der dynamischer Protokollierung

Das Azure SDK für JavaScript ermöglicht das dynamische Aktivieren der Protokollierung nach Bedarf oder für bestimmte Clientbibliotheken. Dies ist hilfreich für Entwickler, die eine benutzerdefinierte Protokollierungsimplementierung für einige Anwendungskomponenten oder temporäre Protokolle für das Debuggen nutzen möchten.

Sie können das Modul `@azure/logger` zum Festlegen der Protokollebene im Code verwenden:

```js
import { setLogLevel } from "@azure/logger";

setLogLevel("verbose");
```

Importieren Sie zum Aktivieren eines bestimmten Protokollkanals `logger` aus dem Paket, für das Sie Protokolle ausgeben möchten. Im folgenden Beispiel wird die Informationsprotokollierung nur für Event Hubs aktiviert:

```js
import { logger } from "@azure/event-hubs";

logger.info.enable();
```

### <a name="redirect-log-output"></a>Umleiten der Protokollausgabe

Wenn Sie Protokollmeldungen selbst behandeln möchten, weisen Sie die Methode `log` für `AzureLogger` oder für eine andere aus einer Bibliothek importierte Protokollierung neu zu.

Im folgenden Beispiel werden Protokollmeldungen an stderr umgeleitet:

```js
import { AzureLogger } from "@azure/logger";

AzureLogger.log = msg => console.error(msg);
```

Im folgenden Beispiel werden nur Informationsprotokollmeldungen von Azure Event Hub umgeleitet:

```js
import { logger } from "@azure/event-hubs";
logger.info.log = msg => console.error(msg);
```

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/troubleshoot-diagnostic-logs)
- [Überprüfen der Optionen für die Azure-Sicherheitsprotokollierung und -Überwachung](/azure/security/fundamentals/log-audit)
- [Informieren über die Verwendung der Protokolle der Azure-Plattform](/azure/azure-monitor/platform/platform-logs-overview)