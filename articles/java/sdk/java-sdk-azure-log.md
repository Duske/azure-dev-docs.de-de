---
title: Konfigurieren der Protokollierung mit dem Azure SDK für Java
description: Hier erfahren Sie, wie Sie Protokollierungsframeworks für das Azure SDK für Java-Clientbibliotheken konfigurieren.
keywords: Azure, Java, SDK, Protokollierung
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9f7ad8d772b7de1335ebc3ba77cdea8aa1e8b683
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81671946"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Konfigurieren der Protokollierung mit dem Azure SDK für Java

Dieser Artikel enthält Beispiele für Protokollierungskonfigurationen für das [Azure SDK](https://azure.microsoft.com/downloads/) für Java. Ausführlichere Informationen zu Konfigurationsoptionen, etwa zum Festlegen von Protokollebenen oder zur benutzerdefinierten Protokollierung nach Klasse, finden Sie in der Dokumentation für Ihr ausgewähltes Protokollierungsframework.

Das Azure SDK für Java-Clientbibliotheken verwendet [Simple Logging Facade for Java](https://www.slf4j.org/) (SLF4J). SLF4J ermöglicht Ihnen die Verwendung Ihres bevorzugten Protokollierungsframeworks, das zum Zeitpunkt der Anwendungsbereitstellung aufgerufen wird.

> [!NOTE]
> Dieser Artikel gilt für die aktuellen Versionen der Azure SDK-Clientbibliotheken. Informationen dazu, ob eine Bibliothek unterstützt wird, finden Sie in der Liste mit den [neuesten Azure SDK-Releases](https://azure.github.io/azure-sdk/releases/latest/java.html). Wenn Ihre Anwendung eine ältere Version der Azure SDK-Clientbibliotheken verwendet, finden Sie entsprechende Anweisungen in der jeweiligen Dienstdokumentation.

## <a name="declare-a-logging-framework"></a>Deklarieren eines Protokollierungsframeworks

Vor der Implementierung dieser Protokollierungen müssen Sie das relevante Framework als Abhängigkeit in Ihrem Projekt deklarieren. Weitere Informationen finden Sie im [SLF4J-Benutzerhandbuch](http://www.slf4j.org/manual.html#projectDep).

## <a name="configure-log4j-or-log4j-2"></a>Konfigurieren von Log4j oder Log4j 2

Sie können die Protokollierung mit Log4j und Log4j 2 in einer Eigenschaftendatei oder einer XML-Datei konfigurieren. Ausführliche Informationen zur Log4j- und Log4j 2-Protokollierung finden Sie im [Handbuch zu Apache Log4j 2](https://logging.apache.org/log4j/2.x/manual/configuration.html).

### <a name="use-a-properties-file"></a>Verwenden einer Eigenschaftendatei

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine neue Datei mit dem Namen *log4j.properties* (oder *log4j2.properties* für Logj4 2). Verwenden Sie die folgenden Beispiele für den Einstieg:

Log4j-Beispiel:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

Log4j2-Beispiel:

```properties
appender.console.type = Console
appender.console.name = LogToConsole
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR
```

### <a name="use-an-xml-file"></a>Verwenden einer XML-Datei

Alternativ können Sie eine XML-Datei zum Konfigurieren von Log4j und Log4j2 verwenden. Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine neue Datei mit dem Namen *log4j.xml* (oder *log4j2.xml* für Logj4 2). Verwenden Sie die folgenden Beispiele für den Einstieg:

Log4j-Beispiel:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

  <appender name="console" class="org.apache.log4j.ConsoleAppender">
    <param name="Target" value="System.out"/>
    <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%m%n" />
    </layout>
  </appender>
  <logger name="com.azure.core" additivity="true">
    <level value="ERROR" />
    <appender-ref ref="console" />
  </logger>

  <root>
    <priority value ="info"></priority>
    <appender-ref ref="console"></appender>
  </root>

</log4j:configuration>
```

Log4j2-Beispiel:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.azure.core" level="error" additivity="true">
            <appender-ref ref="console" />
        </Logger>
        <Root level="info" additivity="false">
            <appender-ref ref="console" />
        </Root>
     </Loggers>
</Configuration>
```

## <a name="configure-logback"></a>Konfigurieren von Logback

[Logback](https://logback.qos.ch/manual/introduction.html) ist eines der gängigen Protokollierungsframeworks und eine native Implementierung von SLF4J. Erstellen Sie zum Konfigurieren von Logback im Verzeichnis *./src/main/resources* Ihres Projekts eine neue XML-Datei mit dem Namen *logback.xml*. Weitere Informationen zu den Konfigurationsoptionen finden Sie auf der [Logback-Projektwebsite](https://logback.qos.ch/manual/configuration.html).

Hier sehen Sie ein Beispiel für eine Logback-Konfiguration:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%message%n</pattern>
    </encoder>
  </appender>

  <logger name="com.azure.core" level="ERROR" />

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

Hier sehen Sie eine einfache Logback-Konfiguration für die Protokollierung in der Konsole:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="Console"
    class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="Console" />
  </root>
</configuration>
```

Nachfolgend sehen Sie eine Konfiguration für die Protokollierung in einer Datei, für die stündlich ein Rollover ausgeführt und die im GZIP-Dateiformat archiviert wird:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder
      class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="info">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="configure-logback-for-a-spring-boot-application"></a>Konfigurieren von Logback für eine Spring Boot-Anwendung

Spring sucht in der Datei *application.properties* (im Verzeichnis *./src/main/resources*) nach Ihren Projektkonfigurationen, einschließlich Protokollierung. Fügen Sie in der Datei *application.properties* die folgende Zeile hinzu, um die Datei *logback.xml* mit Ihrer Spring Boot-Anwendung zu verknüpfen:

```properties
logging.config=classpath:logback.xml
```

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Konfigurieren der Fallbackprotokollierung für temporäres Debuggen

In Szenarien, in denen Ihre Anwendung nicht erneut mit der SLF4J-Protokollierung bereitgestellt werden kann, können Sie die Fallbackprotokollierung verwenden. Diese ist ab Azure Core 1.3.0 oder höher in die Azure-Clientbibliotheken für Java integriert. Zum Aktivieren dieser Protokollierung müssen Sie sich zunächst vergewissern, dass nicht die SLF4J-Protokollierung eingerichtet ist, da diese Vorrang hat. Legen Sie anschließend die Umgebungsvariable `AZURE_LOG_LEVEL` fest. Nach dem Festlegen der Umgebungsvariable müssen Sie die Anwendung neu starten, um mit dem Erstellen von Protokollen zu beginnen.

Die folgende Tabelle enthält die zulässigen Werte für diese Umgebungsvariable:

|Log level   |Zulässige Werte für Umgebungsvariablen   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATION|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|ERROR    |"err", "error"  |

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Überprüfen der Optionen für die Azure-Sicherheitsprotokollierung und -Überwachung](/azure/security/fundamentals/log-audit)
- [Informieren über die Verwendung der Protokolle der Azure-Plattform](/azure/azure-monitor/platform/platform-logs-overview)
