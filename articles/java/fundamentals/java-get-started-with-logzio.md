---
title: Erste Schritte mit Logz.io für in Azure ausgeführte Java-Apps
description: In diesem Tutorial erfahren Sie, wie Sie Logz.io für in Azure ausgeführte Java-Apps integrieren und konfigurieren.
author: jdubois
manager: bborges
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: judubois
ms.custom: devx-track-java
ms.openlocfilehash: 89dc2a40e39a417d33e59b22f28202c61f036c79
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831486"
---
# <a name="tutorial-getting-started-with-monitoring-and-logging-using-logzio-for-java-apps-running-on-azure"></a>Tutorial: Erste Schritte bei der Überwachung und Protokollierung mithilfe von Logz.io für in Azure ausgeführte Java-Apps

In diesem Tutorial erfahren Sie, wie Sie eine klassische Java-Anwendung konfigurieren, um Protokolle zur Erfassung und Analyse an den Dienst [Logz.io](https://logz.io/) zu senden. Logz.io bietet eine umfassende Überwachungslösung auf der Grundlage von Elasticsearch, Logstash, Kibana (ELK) und Grafana.

In diesem Tutorial wird davon ausgegangen, dass Sie Log4J oder Logback verwenden. Da diese beiden Bibliotheken für die Protokollierung in Java am gängigsten sind, sollte das Tutorial für die meisten in Azure ausgeführten Anwendungen funktionieren. Falls Sie Ihre Java-Anwendungen bereits mithilfe von Elastic Stack überwachen, erfahren Sie in diesem Tutorial, wie Sie die Konfiguration so ändern, dass der Logz.io-Endpunkt als Ziel verwendet wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Senden von Protokollen aus einer vorhandenen Java-Anwendung an Logz.io
> * Senden von Diagnoseprotokollen und Metriken von Azure-Diensten an Logz.io

## <a name="prerequisites"></a>Voraussetzungen

* [Java Developer Kit](./java-jdk-long-term-support.md) ab Version 8
* Ein Logz.io-Konto aus dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/logz.logzio-elk-as-a-service-pro)
* Eine vorhandene Java-Anwendung, die Log4J oder Logback verwendet.

## <a name="send-java-application-logs-to-logzio"></a>Senden von Java-Anwendungsprotokollen an Logz.io

Als Erstes erfahren Sie, wie Sie Ihre Java-Anwendung mit einem Token konfigurieren, das es ihr ermöglicht, auf Ihr Logz.io-Konto zuzugreifen.

### <a name="get-your-logzio-access-token"></a>Abrufen Ihres Logz.io-Zugriffstokens

Melden Sie sich zum Abrufen Ihres Tokens bei Ihrem Logz.io-Konto an, wählen Sie in der rechten Ecke das Zahnradsymbol aus, und wählen Sie anschließend **Settings > General** (Einstellungen > Allgemein) aus. Kopieren Sie das in Ihren Kontoeinstellungen angezeigte Zugriffstoken, um es später zur Hand zu haben.

### <a name="install-and-configure-the-logzio-library-for-log4j-or-logback"></a>Installieren und Konfigurieren der Logz.io-Bibliothek für Log4J oder Logback

Die Logz.io-Java-Bibliothek steht bei Maven Central zur Verfügung und kann Ihrer App-Konfiguration als Abhängigkeit hinzugefügt werden. Überprüfen Sie die Versionsnummer bei Maven Central, und verwenden Sie in den folgenden Konfigurationseinstellungen die neueste Version.

Falls Sie Maven verwenden, fügen Sie der Datei `pom.xml` die folgende Abhängigkeit hinzu:

**Log4J:**

```xml
<dependency>
    <groupId>io.logz.log4j2</groupId>
    <artifactId>logzio-log4j2-appender</artifactId>
    <version>1.0.11</version>
</dependency>
```

**Logback:**

```xml
<dependency>
    <groupId>io.logz.logback</groupId>
    <artifactId>logzio-logback-appender</artifactId>
    <version>1.0.22</version>
</dependency>
```

Falls Sie Gradle verwenden, fügen Sie Ihrem Buildskript die folgende Abhängigkeit hinzu:

**Log4J:**

```
implementation 'io.logz.log4j:logzio-log4j-appender:1.0.11'
```

**Logback:**

```
implementation 'io.logz.logback:logzio-logback-appender:1.0.22'
```

Aktualisieren Sie als Nächstes Ihre Log4J- oder Logback-Konfigurationsdatei:

**Log4J:**

```xml
<Appenders>
    <LogzioAppender name="Logzio">
        <logzioToken><your-logz-io-token></logzioToken>
        <logzioType>java-application</logzioType>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
    </LogzioAppender>
</Appenders>

<Loggers>
    <Root level="info">
        <AppenderRef ref="Logzio"/>
    </Root>
</Loggers>
```

**Logback:**

```xml
<configuration>
    <!-- Use shutdownHook so that we can close gracefully and finish the log drain -->
    <shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
    <appender name="LogzioLogbackAppender" class="io.logz.logback.LogzioLogbackAppender">
        <token><your-logz-io-token></token>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
        <logzioType>java-application</logzioType>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="LogzioLogbackAppender"/>
    </root>
</configuration>
```

Ersetzen Sie den Platzhalter `<your-logz-io-token>` durch Ihr Zugriffstoken und den Platzhalter `<your-logz-io-listener-host>` durch den Listenerhost Ihrer Region (z. B. „listener.logz.io“). Weitere Informationen zum Ermitteln der Region Ihres Kontos finden Sie unter [Kontoregion](https://docs.logz.io/user-guide/accounts/account-region.html).

Das Element `logzioType` verweist auf ein logisches Feld in Elasticsearch, das dazu dient, unterschiedliche Dokumente voneinander zu trennen. Dieser Parameter muss ordnungsgemäß konfiguriert werden, um Logz.io optimal nutzen zu können.

Bei einem „Typ“ in Logz.io handelt es sich um Ihr Protokollformat (Apache, NGinx, MySQL oder Ähnliches), nicht um Ihre Quelle (beispielsweise Server 1, Server 2 oder Server 3). In diesem Tutorial rufen wir den Typ `java-application` auf, da wir Java-Anwendungen konfigurieren und davon ausgehen, dass diese Anwendungen alle das gleiche Format haben.

In komplexeren Szenarien können Sie Ihre Java-Anwendungen zu verschiedenen Typen gruppieren, die jeweils über ein eigenes spezifisches Protokollformat verfügen (konfigurierbar mit Log4J und Logback). Mögliche Typen wären beispielsweise „spring-boot-monolith“ und „spring-boot-microservice“.

### <a name="test-your-configuration-and-log-analysis-on-logzio"></a>Testen Ihrer Konfiguration und Ihrer Protokollanalyse in Logz.io

Nach dem Konfigurieren der Logz.io-Bibliothek sollte Ihre Anwendung Protokolle direkt an die Bibliothek senden. Um zu testen, ob alles ordnungsgemäß funktioniert, wählen Sie in der Logz.io-Konsole die Registerkarte **Live tail** (Liveüberwachung) und anschließend **run** (Ausführen) aus. Daraufhin sollte eine Meldung wie die folgende angezeigt werden, die bestätigt, dass die Verbindung funktioniert:

```output
Requesting Live Tail access...
Access granted. Opening connection...
Connected. Tailing...
````

Starten Sie als Nächstes Ihre Anwendung, oder verwenden Sie sie, um Protokolle zu generieren. Die Protokolle sollten direkt auf Ihrem Bildschirm angezeigt werden. Das folgende Beispiel zeigt die ersten Startmeldungen einer Spring Boot-Anwendung:

```output
2019-09-19 12:54:40.685Z Starting JavaApp on javaapp-default-9-5cfcb8797f-dfp46 with PID 1 (/workspace/BOOT-INF/classes started by cnb in /workspace)
2019-09-19 12:54:40.686Z The following profiles are active: prod
2019-09-19 12:54:42.052Z Bootstrapping Spring Data repositories in DEFAULT mode.
2019-09-19 12:54:42.169Z Finished Spring Data repository scanning in 103ms. Found 6 repository interfaces.
2019-09-19 12:54:43.426Z Bean 'spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties' of type [org.springframework.boot.autoconfigure.task.TaskExecutionProperties] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
```

Nachdem Ihre Protokolle nun von Logz.io verarbeitet werden, können Sie sämtliche Dienste der Plattform nutzen.

## <a name="send-azure-services-data-to-logzio"></a>Senden von Azure-Dienstdaten an Logz.io

In diesem Abschnitt erfahren Sie, wie Sie Protokolle und Metriken von Ihren Azure-Ressourcen aus an Logz.io senden.

### <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Als Erstes muss die Azure-Integrationsvorlage für Logz.io bereitgestellt werden. Die Integration basiert auf einer vorgefertigten Azure-Bereitstellungsvorlage, die alle erforderlichen Pipelinekomponenten einrichtet. Die Vorlage erstellt einen Event Hub-Namespace, einen Event Hub, zwei Speicherblobs und alle erforderlichen Berechtigungen und Verbindungen. Die im Rahmen der automatisierten Bereitstellung eingerichteten Ressourcen können Daten für eine einzelne Azure-Region erfassen und an Logz.io übermitteln.

Suchen Sie im [ersten Schritt der Anleitung des Repositorys](https://github.com/logzio/logzio-azure-serverless) nach der Schaltfläche **Deploy to Azure** (In Azure bereitstellen).

Wenn Sie **Deploy to Azure** (In Azure bereitstellen) auswählen, wird die Seite **Benutzerdefinierte Bereitstellung** im Azure-Portal mit einer Liste vorab ausgefüllter Felder angezeigt.

In den meisten Feldern sind keine Änderungen erforderlich. Folgende Einstellungen müssen allerdings eingegeben werden:

* **Ressourcengruppe**: Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue.
* **Logzio Logs/Metrics Host** (Host für Logz.io-Protokolle/Metriken): Geben Sie die URL des Logz.io-Listeners ein. Sollten Sie sich bei dieser URL nicht sicher sein, überprüfen Sie Ihre Anmelde-URL. Lautet sie „app.logz.io“, verwenden Sie „listener.logz.io“ (Standardeinstellung). Lautet sie „app-eu.logz.io“, verwenden Sie „listener-eu.logz.io“.
* **Logzio Logs/Metrics Token** (Token für Logz.io-Protokolle/Metriken): Geben Sie das Token des Logz.io-Kontos ein, an das Sie Azure-Protokolle oder -Metriken übermitteln möchten. Sie finden dieses Token auf der Kontoseite der Logz.io-Benutzeroberfläche.

Stimmen Sie unten auf der Seite den Bedingungen zu, und wählen Sie **Purchase** (Kaufen) aus. Die Vorlage wird daraufhin von Azure bereitgestellt. Dies kann ein bis zwei Minuten dauern. Nach Abschluss des Vorgangs wird im oberen Bereich des Portals die Meldung „Bereitstellung erfolgreich“ angezeigt.

Sie können zur definierten Ressourcengruppe navigieren, um die bereitgestellten Ressourcen zu überprüfen.

Informationen dazu, wie Sie „logzio-azure-serverless“ zum Sichern von Daten in Azure Blob Storage konfigurieren, finden Sie unter [Übermitteln von Azure-Aktivitätsprotokollen](https://docs.logz.io/shipping/log-sources/azure-activity-logs.html).

### <a name="stream-azure-logs-and-metrics-to-logzio"></a>Streamen von Azure-Protokollen und -Metriken an Logz.io

Nachdem Sie die Integrationsvorlage bereitgestellt haben, müssen Sie Azure so konfigurieren, dass Diagnosedaten an den soeben bereitgestellten Event Hub gestreamt werden. Beim Event Hub eingehende Daten werden von der Funktions-App an Logz.io weitergeleitet.

1. Geben Sie auf der Suchleiste den Suchbegriff „Diagnose“ ein, und wählen Sie anschließend **Diagnoseeinstellungen** aus.

2. Wählen Sie in der Ressourcenliste eine Ressource und anschließend **Diagnoseeinstellung hinzufügen** aus, um den Bereich **Diagnoseeinstellungen** für diese Ressource zu öffnen.

    ![Bereich „Diagnoseeinstellungen“](media/java-get-started-with-logzio/diagnostics-settings.png)

3. Geben Sie unter **Name** einen Namen für Ihre Diagnoseeinstellungen ein.

4. Wählen Sie **An einen Event Hub streamen** und anschließend **Konfigurieren** aus, um den Bereich **Event Hub auswählen** zu öffnen.

5. Wählen Sie Ihren Event Hub aus:

    * **Event Hub-Namespace auswählen**: Wählen Sie den Namespace aus, der mit **Logzio** beginnt (beispielsweise `LogzioNS6nvkqdcci10p`).
    * **Event Hub-Name auswählen**: Wählen Sie für Protokolle die Option **insights-operational-logs** und für Metriken die Option **insights-operational-metrics** aus.
    * **Event Hub-Richtlinienname auswählen**: Wählen Sie **LogzioSharedAccessKey** aus.

6. Wählen Sie **OK** aus, um zum Bereich **Diagnoseeinstellungen** zurückzukehren.

7. Wählen Sie im Abschnitt „Protokoll“ die zu streamenden Daten und anschließend **Speichern** aus.

Die ausgewählten Daten werden nun an den Event Hub gestreamt.

### <a name="visualize-your-data"></a>Visualisieren Ihrer Daten

Warten Sie eine Weile, damit die Daten aus Ihrem System an Logz.io übertragen werden können, und öffnen Sie dann Kibana. Ihre Dashboards sollten nun über Daten vom Typ _eventhub_ verfügen. Weitere Informationen zum Erstellen von Dashboards finden Sie unter [Erstellen des perfekten Kibana-Dashboards](https://logz.io/blog/perfect-kibana-dashboard/).

Von dort aus können Sie bestimmte Daten auf der Registerkarte **Discover** (Entdecken) abfragen oder Kibana-Objekte erstellen, um Ihre Daten auf der Registerkarte **Visualize** (Visualisieren) zu visualisieren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie mithilfe des folgenden Befehls löschen:

```azurecli-interactive
az group delete --name <resource group>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Ihre Java-Anwendung und Azure-Dienste so konfigurieren, dass Protokolle und Metriken an Logz.io gesendet werden.

Im nächsten Artikel erfahren Sie mehr zur Überwachung Ihrer Anwendung unter Verwendung von Event Hub:

> [!div class="nextstepaction"]
> [Streamen von Azure-Überwachungsdaten an einen Event Hub](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)