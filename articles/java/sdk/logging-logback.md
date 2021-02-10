---
title: Protokollieren mit dem Azure SDK für Java und Logback
description: Übersicht über die Azure SDK für Java-Integration mit Logback
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: ca59b7dc9f861dd833788c88c18ce8b14a016e70
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528489"
---
# <a name="log-with-the-azure-sdk-for-java-and-logback"></a>Protokollieren mit dem Azure SDK für Java und Logback

Dieser Artikel bietet eine Übersicht, wie Sie Protokollierung zu Anwendungen mithilfe von Logback hinzufügen, die das Azure SDK für Java verwenden. Wie unter [Konfigurieren von Protokollierung im Azure SDK für Java](logging-overview.md) beschrieben, führen alle Azure-Clientbibliotheken Protokollierung über [SLF4J](http://www.slf4j.org/) aus, damit Sie Protokollierungsframeworks wie [Logback](http://logback.qos.ch/) verwenden können.

Zum Aktivieren der Logback-Protokollierung müssen Sie zwei Schritte ausführen:

1. Schließen Sie die Logback-Bibliothek als Abhängigkeit ein.
2. Erstellen Sie im Projektverzeichnis */src/main/resources* eine Datei namens *logback.xml*.

Weitere Informationen zum Konfigurieren von Logback finden Sie unter [Logback-Konfiguration](http://logback.qos.ch/manual/configuration.html) in der Logback-Dokumentation.

## <a name="add-the-maven-dependency"></a>Hinzufügen der Maven-Abhängigkeit

Um die Maven-Abhängigkeit hinzuzufügen, fügen Sie den folgenden XML-Code in die Datei *pom.xml* des Projekts ein. Ersetzen Sie die Versionsnummer *1.2.3* durch die neueste veröffentlichte Versionsnummer, die auf der Seite [Logback Classic Module](https://mvnrepository.com/artifact/ch.qos.logback/logback-classic) (Klassisches Logback-Modul) angezeigt wird.

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

## <a name="add-logbackxml-to-your-project"></a>Hinzufügen von „logback.xml“ zu Ihrem Projekt

[Logback](https://logback.qos.ch/manual/introduction.html) ist eines der gängigen Protokollierungsframeworks. Erstellen Sie zum Aktivieren von Logback-Protokollierung im Verzeichnis *./src/main/resources* Ihres Projekts eine Datei namens *logback.xml*. Diese Datei enthält die Protokollierungskonfigurationen, mit denen die Protokollierungsanforderungen angepasst werden können. Weitere Informationen zum Konfigurieren von *logback.xml* finden Sie unter [Logback-Konfiguration](https://logback.qos.ch/manual/configuration.html) in der Logback-Dokumentation.

### <a name="console-logging"></a>Konsolenprotokollierung

Sie können eine Logback-Konfiguration erstellen, um die Protokollierung wie im folgenden Beispiel gezeigt in der Konsole auszugeben. Dieses Beispiel ist so konfiguriert, dass alle Protokollierungsereignisse unabhängig von der Quelle protokolliert werden, bei denen es sich um die Ebene INFO oder höher handelt.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

### <a name="log-azure-core-errors"></a>Protokollieren von Azure Core-Fehlern

Die folgende Beispielkonfiguration ähnelt der vorherigen Konfiguration, verringert jedoch die Ebene, bei der die Protokollierung von allen `com.azure.core`-Paketklassen (einschließlich der untergeordneten Pakete) stammt. Auf diese Weise werden alle Ebenen INFO und höher protokolliert, mit Ausnahme von `com.azure.core`. Für dieses Element wird nur die Ebene ERROR und höher protokolliert. Sie können diesen Ansatz zum Beispiel verwenden, wenn Sie den Code in `com.azure.core` zu unruhig finden. Diese Art der Konfiguration kann auch auf beide Arten erfolgen. Wenn Sie z. B. weitere Debuginformationen von Klassen in `com.azure.core` erhalten möchten, können Sie diese Einstellung in DEBUG ändern.

Es ist möglich, die Protokollierung bestimmter Klassen oder bestimmter Pakete präzise zu steuern. Wie unten gezeigt, steuert `com.azure.core` die Ausgabe aller core-Klassen, aber Sie könnten genauso gut `com.azure.security.keyvault` oder ein Äquivalent verwenden, um die Ausgabe so zu steuern, wie es für die Umstände, die im Kontext der ausgeführten Anwendung am informativsten sind, angemessen ist.

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

### <a name="log-to-a-file-with-log-rotation-enabled"></a>Protokollieren in eine Datei mit aktivierter Protokollrotation

Die oben genannten Beispiele protokollieren in die Konsole, was normalerweise nicht der bevorzugte Ort für Protokolle ist. Verwenden Sie die folgende Konfiguration, um stattdessen in eine Datei mit stündlichem Rollover zu protokollieren und für die Archivierung das gzip-Format zu verwenden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="INFO">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="spring-applications"></a>Spring-Anwendungen

Das Spring-Framework arbeitet, indem die Spring-Datei *application.properties* für verschiedene Konfigurationen (einschließlich der Protokollierungskonfiguration) gelesen wird. Es ist jedoch möglich, die Spring-Anwendung so zu konfigurieren, dass sie Logback-Konfigurationen aus einer beliebigen Datei liest. Konfigurieren Sie dazu die Eigenschaft `logging.config` so, dass sie auf die Konfigurationsdatei *logback.xml* verweist, indem Sie die folgende Zeile in Ihre Spring-Datei */src/main/resources/application.properties* einfügen:

```properties
logging.config=classpath:logback.xml
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die Konfiguration der Logback-Protokollierung behandelt und erläutert, wie das Azure SDK für Java für Protokollierung verwendet wird. Da das Azure SDK für Java mit allen SLF4J-Protokollierungsframeworks funktioniert, sollten Sie das [SLF4J-Benutzerhandbuch](http://www.slf4j.org/manual.html) zu Rate ziehen, um weitere Informationen zu erhalten. Wenn Sie Logback verwenden, finden Sie auch zahlreiche Konfigurationsanleitungen auf der zugehörigen Website. Weitere Informationen finden Sie unter [Logback-Konfiguration](http://logback.qos.ch/manual/configuration.html) in der Logback-Dokumentation.

Nachdem Sie die Protokollierung verstanden haben, können Sie sich die Integrationen von Azure in Frameworks wie [Spring](/azure/developer/java/spring-framework/spring-boot-starters-for-azure) und [MicroProfile](/azure/developer/java/eclipse-microprofile/) ansehen.
