---
title: Konfigurieren der Protokollierung mit dem Azure SDK für Java
description: Hier erfahren Sie, wie Sie Protokollierungsframeworks für das Azure SDK für Java-Clientbibliotheken konfigurieren.
keywords: Azure, Java, SDK, Protokollierung
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.custom: devx-track-java
ms.openlocfilehash: 5bb7f711eae230a08893d2f94c242a06af809f88
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400618"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Konfigurieren der Protokollierung mit dem Azure SDK für Java

Dieser Artikel enthält Beispiele für Protokollierungskonfigurationen für das [Azure SDK](https://azure.microsoft.com/downloads/) für Java. Ausführlichere Informationen zu Konfigurationsoptionen, etwa zum Festlegen von Protokollebenen oder zur benutzerdefinierten Protokollierung nach Klasse, finden Sie in der Dokumentation für Ihr ausgewähltes Protokollierungsframework.

Das Azure SDK für Java-Clientbibliotheken verwendet [Simple Logging Facade for Java](https://www.slf4j.org/) (SLF4J). SLF4J ermöglicht Ihnen die Verwendung Ihres bevorzugten Protokollierungsframeworks, das zum Zeitpunkt der Anwendungsbereitstellung aufgerufen wird.

> [!NOTE]
> Dieser Artikel gilt für die aktuellen Versionen der Azure SDK-Clientbibliotheken. Informationen dazu, ob eine Bibliothek unterstützt wird, finden Sie in der Liste mit den [neuesten Azure SDK-Releases](https://azure.github.io/azure-sdk/releases/latest/java.html). Wenn Ihre Anwendung eine ältere Version der Azure SDK-Clientbibliotheken verwendet, finden Sie entsprechende Anweisungen in der jeweiligen Dienstdokumentation.

## <a name="declare-a-logging-framework"></a>Deklarieren eines Protokollierungsframeworks

Vor der Implementierung dieser Protokollierungen müssen Sie das relevante Framework als Abhängigkeit in Ihrem Projekt deklarieren. Weitere Informationen finden Sie im [SLF4J-Benutzerhandbuch](https://www.slf4j.org/manual.html#projectDep).

In den folgenden Abschnitten finden Sie Konfigurationsbeispiele für gängige Protokollierungsframeworks.

## <a name="use-log4j"></a>Verwenden von Log4j

In den folgenden Beispielen werden Konfigurationen für das Log4j-Protokollierungsframework veranschaulicht. Weitere Informationen finden Sie in der [Log4j-Dokumentation](https://logging.apache.org/log4j/1.2/).

**Aktivieren von Log4j durch Hinzufügen einer Maven-Abhängigkeit**

Fügen Sie der Datei *pom.xml* Ihres Projekts Folgendes hinzu:

```xml
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>[1.0,)</version> <!-- Version number 1.0 and above -->
</dependency>
```

**Aktivieren von Log4j mithilfe einer Eigenschaftendatei**

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine Datei vom Typ *log4j.properties*, und fügen Sie den folgenden Inhalt hinzu:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

**Aktivieren von Log4j mithilfe einer XML-Datei**

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine Datei vom Typ *log4j.xml*, und fügen Sie den folgenden Inhalt hinzu:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%m%n" />
        </layout>
    </appender>
    <logger name="com.azure.core">
        <level value="ERROR" />
        <appender-ref ref="console" />
    </logger>

    <root>
        <level value="info" />
        <appender-ref ref="console" />
    </root>

</log4j:configuration>
```

## <a name="use-log4j-2"></a>Verwenden von Log4j 2

In den folgenden Beispielen werden Konfigurationen für das Log4j 2-Protokollierungsframework veranschaulicht. Weitere Informationen finden Sie in der [Log4j 2-Dokumentation](https://logging.apache.org/log4j/2.x/manual/configuration.html).

**Aktivieren von Log4j 2 durch Hinzufügen einer Maven-Abhängigkeit**

Fügen Sie der Datei *pom.xml* Ihres Projekts Folgendes hinzu:

```
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>[2.0,)</version> <!-- Version number 2.0 and above -->
</dependency>
```

**Aktivieren von Log4j 2 mithilfe einer Eigenschaftendatei**

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine Datei vom Typ *log4j2.properties*, und fügen Sie den folgenden Inhalt hinzu:

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

**Aktivieren von Log4j 2 mithilfe einer XML-Datei**

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine Datei vom Typ *log4j2.xml*, und fügen Sie den folgenden Inhalt hinzu:

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

## <a name="use-logback"></a>Verwenden von Logback

In den folgenden Beispielen werden grundlegende Konfigurationen für das Logback-Protokollierungsframework veranschaulicht. Weitere Informationen finden Sie in der [Logback-Dokumentation](https://logback.qos.ch/manual/configuration.html).

**Aktivieren von Logback durch Hinzufügen einer Maven-Abhängigkeit**

Fügen Sie der Datei *pom.xml* Ihres Projekts Folgendes hinzu:

```
<!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>[0.2.5,)</version> <!-- Version number 0.2.5 and above -->
</dependency>
```

**Aktivieren von Logback mithilfe einer XML-Datei**

Erstellen Sie im Verzeichnis *./src/main/resource* Ihres Projekts eine Datei vom Typ *Logback.xml*, und fügen Sie den folgenden Inhalt hinzu:

```xml
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

## <a name="use-logback-in-a-spring-boot-application"></a>Verwenden von Logback in einer Spring Boot-Anwendung

In den folgenden Beispielen werden einige Konfigurationen für die Verwendung von Logback mit Spring veranschaulicht. In der Regel fügen Sie einer Datei vom Typ *logback.xml* im Verzeichnis *./src/main/resources* Ihres Projekts Projektkonfigurationen hinzu. Spring überprüft diese Datei auf verschiedene Konfigurationen, einschließlich Protokollierung. Weitere Informationen finden Sie in der [Logback-Dokumentation](https://logback.qos.ch/manual/configuration.html).

Sie können Ihre Anwendung so konfigurieren, dass sie Logback-Konfigurationen aus beliebigen Dateien liest. Wenn Sie die Datei *logback.xml* mit Ihrer Spring-Anwendung verknüpfen möchten, erstellen Sie im Verzeichnis *./src/main/resources* Ihres Projekts eine Datei vom Typ *application.properties*, und fügen Sie den folgenden Inhalt hinzu:

```properties
logging.config=classpath:logback.xml
```

Fügen Sie der Datei *logback.xml* Folgendes hinzu, um eine Logback-Konfiguration für die Protokollierung in der Konsole zu erstellen:

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

Fügen Sie der Datei *logback.xml* Folgendes hinzu, um die Protokollierung in einer Datei zu konfigurieren, für die stündlich ein Rollover ausgeführt und die im GZIP-Dateiformat archiviert wird:

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

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Konfigurieren der Fallbackprotokollierung für temporäres Debuggen

In Szenarien, in denen Ihre Anwendung nicht erneut mit der SLF4J-Protokollierung bereitgestellt werden kann, können Sie die Fallbackprotokollierung verwenden. Diese ist ab Azure Core 1.3.0 in die Azure-Clientbibliotheken für Java integriert. Zum Aktivieren dieser Protokollierung müssen Sie sich zunächst vergewissern, dass nicht die SLF4J-Protokollierung eingerichtet ist, da diese Vorrang hat. Legen Sie anschließend die Umgebungsvariable `AZURE_LOG_LEVEL` fest. Nach dem Festlegen der Umgebungsvariable müssen Sie die Anwendung neu starten, um mit dem Erstellen von Protokollen zu beginnen.

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
