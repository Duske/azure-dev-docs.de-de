---
title: Konfigurieren von Ablaufverfolgung im Azure SDK für Java
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit Ablaufverfolgung
author: samvaity
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: savaity
ms.openlocfilehash: e5ead01c22fdb80e7c5fc49c3a980add883eb6d9
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395215"
---
# <a name="configure-tracing-in-the-azure-sdk-for-java"></a>Konfigurieren von Ablaufverfolgung im Azure SDK für Java

Dieser Artikel bietet eine Übersicht, wie Sie das Azure SDK für Java konfigurieren, um Ablaufverfolgungsfunktionalität zu integrieren.

Das Azure SDK für Java ermöglicht die Ablaufverfolgung in allen Clientbibliotheken, indem eine Abhängigkeit vom auf [OpenTelemetery](https://opentelemetry.io/) basierenden [Plug-In azure-core-tracing-opentelemetry](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java) eingeschlossen wird. OpenTelemetry ist ein gängiges Open-Source-Ablaufverfolgungsframework zum Generieren, Erfassen und Sammeln von Telemetriedaten für cloudnative Software.

Es gibt zwei grundlegende Konzepte im Zusammenhang mit Ablaufverfolgung: **Spanne** und **Stapelüberwachung**. Eine Spanne stellt einen einzelnen Vorgang in einer Stapelüberwachung dar. Eine Spanne kann eine HTTP-Anforderung, einen Remoteprozeduraufruf (RPC), eine Datenbankabfrage oder sogar den Pfad darstellen, den Ihr Code annimmt. Bei einer Stapelüberwachung handelt es sich um eine Struktur von Spannen, die den Arbeitspfad durch ein System zeigt. Sie können eine Stapelüberwachung durch eine eindeutige 16-Byte-Sequenz, die als TraceID bezeichnet wird, unterscheiden. Weitere Informationen zu diesen Konzepten und ihrer Beziehung zu OpenTelemetry finden Sie in der [OpenTelemetry-Dokumentation](https://opentelemetry.io/docs/).

Es gibt zwei Möglichkeiten, die Ablaufverfolgung in den Azure-Clientbibliotheken für Java zu aktivieren:

1. Durch Aktivieren von Funktionen, die in das Azure SDK für Java integriert sind.
2. Durch Aktivieren eines In-Process-Agents, um Ablaufverfolgungsdaten zu erfassen und ohne Codeänderungen zu übermitteln.

## <a name="enable-tracing-in-the-azure-sdk-for-java"></a>Aktivieren von Ablaufverfolgung im Azure SDK für Java

Um Ablaufverfolgung für alle Azure Java-Clientbibliotheken zu aktivieren, fügen Sie der Anwendung die Abhängigkeiten `azure-core-tracing-opentelemetry` und `opentelemetry-sdk` hinzu. Fügen Sie in Maven z. B. die folgenden Abhängigkeiten hinzu:

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-tracing-opentelemetry</artifactId>
  <version>1.0.0-beta.6</version>
</dependency>

<dependency>
  <groupId>io.opentelemetry</groupId>
  <artifactId>opentelemetry-sdk</artifactId>
  <version>0.8.0</version>
</dependency>
```

Wenn diese Abhängigkeit hinzugefügt wird, wird Ablaufverfolgung aktiviert, und Stapelüberwachungen sind in allen HTTP-Anforderungen enthalten. Es ergeben sich jetzt zwei Probleme:

1. Es ist keine Integration in eine eingehende übergeordnete Spanne vorhanden.
2. Die generierten Stapelüberwachungen werden für eine spätere Analyse nicht exportiert.

Diese Probleme werden in den folgenden Abschnitten behandelt.

### <a name="integrate-parent-spans"></a>Integrieren übergeordneter Spannen

Wie weiter oben bereits erwähnt, wird durch das Einbeziehen der Abhängigkeiten Ablaufverfolgung innerhalb der Azure-Clientbibliotheken aktiviert. Es erfolgt jedoch keine Integration eingehender Ablaufverfolgungsdaten, z. B. in einer Webumgebung, in der eine eingehende Anforderung zu einem Aufruf einer Azure-Clientbibliothek führt. Um Ablaufverfolgung zu aktivieren, können Sie eine Stammspanne in Ihrer Anwendung erstellen und diese an Aufrufe der Azure-Clientbibliothek übergeben, sodass diese Spanne in entsprechende ausgehende Anforderungen an Azure-Dienste gekapselt werden kann. Sie können diese Aufgabe ausführen, indem Sie den `Context`-Parameter für alle Clientmethoden verwenden, wie im folgenden Beispiel gezeigt:

```java
// The 'span' given in this context is the parent span key received from the incoming request.
Context traceContext = new Context(PARENT_SPAN_KEY, span);

// This example code passes a new configuration setting to a service, but also includes
// the traceContext from above, so that it may be picked up by the http transport and included as appropriate.
appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world"), true, traceContext);
```

In Fällen, in denen keine übergeordnete Spanne bereitgestellt wird, wird eine neue übergeordnete Spanne erstellt, um alle ausgehenden Anforderungen von Clientbibliotheken zu kapseln. Für jeden Aufruf einer Methode der Azure-Clientbibliothek werden zwei Spannen erstellt: eine Spanne, die den Fortschritt durch die Clientbibliotheken verfolgt, und eine andere Spanne, die die ausgehende HTTP-Anforderungsspanne nachverfolgt.

#### <a name="tracer-span-attributes"></a>Tracerspannenattribute

Zusätzlich zu den erforderlichen Standardattributen, die in den [Semantikkonventionen](https://github.com/open-telemetry/opentelemetry-specification/blob/e9340d74f1ba0b651b3581d6bd5df6a92b772e18/semantic-conventions.md) von OpenTelemetry dokumentiert werden, kommentieren die Azure-Clientbibliotheken die Spannen mit den folgenden Attributen:

* `az.namespace`: [Namespaces](/azure/azure-resource-manager/management/azure-services-resource-providers) des Microsoft-Ressourcenanbieters, die Azure-Diensten zugeordnet werden.
* `x-ms-request-id`: Der eindeutige Bezeichner für die Anforderung.
* `span.kind`: Beschreibt die Beziehung zwischen der Spanne, ihren übergeordneten Elementen und ihren untergeordneten Elementen in einer Ablaufverfolgung.
* `span.status.message`: Stellt den Zustand einer fertigen Spanne dar.
* `span.status.code`: Stellt den Statuscode einer fertigen Spanne dar.

Weitere Metadaten zum ausgeführten Vorgang werden in den Spannennamen erfasst. Die HTTP-Spannennamen werden auf den URI-Pfadwert festgelegt, und die Spanne des Bibliotheksmethodenaufrufs weist das Format `<namespace qualified type>.<method name>` auf.

Beispielsweise führt eine App-Konfigurationsclientanforderung zum Festlegen der Konfigurationseinstellung (`appConfigClient.setConfigurationSettingWithResponse(new ConfigurationSetting().setKey("hello").setValue("world")`) zu den folgenden beiden Spannen:

* Zu einer Bibliotheksmethodenspanne mit dem Namen `AppConfig.setKey`.
* Zu einer ausgehenden HTTP-Anforderungsspanne namens `/kv/hello`.

### <a name="configure-tracing-exports"></a>Konfigurieren von Ablaufverfolgungsexporten

Anwendungen, die Ablaufverfolgungsinformationen verwenden möchten, müssen Ablaufverfolgungen in einen verteilten Ablaufverfolgungsspeicher exportieren (z. B. in [Zipkin](https://zipkin.io/), [Jaeger](https://www.jaegertracing.io/) oder [Azure Monitor](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/monitor/azure-monitor-opentelemetry-exporter#azure-monitor-opentelemetry-exporter-client-library-for-java)). Im folgenden Beispiel wird der Export von Ablaufverfolgungsinformationen mithilfe von Jaeger-spezifischen APIs in einen verteilten Jaeger-Ablaufverfolgungsspeicher konfiguriert, der auf dem localhost-Port 14250 ausgeführt wird:

```java
ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 14250).usePlaintext().build();
JaegerGrpcSpanExporter exporter = JaegerGrpcSpanExporter.newBuilder()
    .setChannel(channel)
    .setServiceName("Sample")
    .setDeadline(0)
    .build();
TracerSdkFactory tracerSdkFactory = (TracerSdkFactory) OpenTelemetry.getTracerFactory();
tracerSdkFactory.addSpanProcessor(SimpleSpansProcessor.newBuilder(exporter).build());
```

## <a name="enable-tracing-with-the-in-process-agent"></a>Aktivieren von Ablaufverfolgung mit dem In-Prozess-Agent

Sie können Application Insights (eine Funktion von [Azure Monitor](/azure/azure-monitor/overview)) für die automatische Erfassung und Übertragung von Daten zur späteren Analyse von Anwendungen in umfangreichen verteilten Systemen verwenden. Diese Instrumentierung überwacht Ihre Anwendung und leitet die Telemetriedaten an eine [Azure Application Insights-Ressource](/azure/azure-monitor/app/app-insights-overview) weiter. Dabei wird eine eindeutige GUID (ein so genannter „Instrumentierungsschlüssel“) verwendet.

Durch die Verwendung eines [Java-In-Prozess-Agents](/azure/azure-monitor/app/java-in-process-agent) können Sie Überwachung Ihrer Anwendungen ohne Codeänderungen aktivieren. Außerdem müssen Sie Ihrem Projekt die Abhängigkeit [azure-core-tracing-opentelemetry](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core-tracing-opentelemetry#azure-tracing-opentelemetry-client-library-for-java) hinzufügen. Nachdem Sie diese Aufgabe abgeschossen haben, können Sie das Application Insights SDK verwenden, um Anforderungen zu instrumentieren, Leistungsindikatoren zu erfassen, Leistungsprobleme und Ausnahmen zu diagnostizieren und Code zu schreiben, um Aktivitäten von Benutzern in einer App zu verfolgen.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun mit den übergreifenden Kernfunktionen des Azure SDK für Java vertraut sind, finden Sie weitere Informationen zum Erstellen von sicheren Anwendungen unter [Azure-Authentifizierung mit Java und Azure Identity](identity.md).
