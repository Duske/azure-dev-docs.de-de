---
title: Protokollieren mit dem Azure SDK für Java und Log4j
description: Übersicht über die Azure SDK für Java-Integration mit log4j
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 0de76274b7f33f724c339eb0137a89d74f3e8678
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118472"
---
# <a name="log-with-the-azure-sdk-for-java-and-log4j"></a>Protokollieren mit dem Azure SDK für Java und Log4j

Dieser Artikel bietet eine Übersicht, wie Sie Protokollierung zu Anwendungen mithilfe von Log4j hinzufügen, die das Azure SDK für Java verwenden. Wie unter [Konfigurieren von Protokollierung im Azure SDK für Java](logging-overview.md) beschrieben, führen alle Azure-Clientbibliotheken Protokollierung über [SLF4J](http://www.slf4j.org/) aus, damit Sie Protokollierungsframeworks wie [log4j](https://logging.apache.org/log4j/2.x/) verwenden können.

Dieser Artikel enthält Anleitungen zur Verwendung der Log4j 2.x-Releases, aber auch Log4j 1.x wird gleichermaßen vom Azure SDK für Java unterstützt. Zum Aktivieren der log4j-Protokollierung müssen Sie zwei Schritte ausführen:

1. Schließen Sie die log4j-Bibliothek als Abhängigkeit ein.
2. Erstellen Sie eine Konfigurationsdatei (entweder *log4j2.properties* oder *log4j2.xml*) im Projektverzeichnis */src/main/resources*.

Weitere Informationen zum Konfigurieren von log4j finden Sie unter [Willkommen bei Log4j 2](https://logging.apache.org/log4j/2.x/manual/index.html).

## <a name="add-the-maven-dependency"></a>Hinzufügen der Maven-Abhängigkeit

Um die Maven-Abhängigkeit hinzuzufügen, fügen Sie den folgenden XML-Code in die Datei *pom.xml* des Projekts ein. Ersetzen Sie die Versionsnummer *2.14.0* durch die neueste veröffentlichte Versionsnummer, die auf der Seite [Apache Log4j SLF4J Binding](https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl) (Apache Log4j SLF4J-Bindung) angezeigt wird.

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.14.0</version>
</dependency>
```

## <a name="configuring-log4j"></a>Konfigurieren von Log4j

Es gibt zwei gängige Methoden zum Konfigurieren von log4j: durch eine externe Eigenschaftendatei oder durch eine externe XML-Datei. Diese Vorgehensweisen werden unten aufgeführt.

### <a name="using-a-property-file"></a>Verwenden einer Eigenschaftendatei

Sie können eine flache Eigenschaftendatei namens *log4j2.properties* in das Verzeichnis */src/main/resource* des Projekts einfügen. Diese Datei sollte die folgende Form haben:

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n

logger.app.name = com.azure.core
logger.app.level = ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

### <a name="using-an-xml-file"></a>Verwenden einer XML-Datei

Sie können eine XML-Datei namens *log4j2.xml* in das Verzeichnis */src/main/resource* des Projekts einfügen. Diese Datei sollte die folgende Form haben:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%msg%n" />
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

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die Konfiguration der Log4j behandelt und erläutert, wie das Azure SDK für Java für Protokollierung verwendet wird. Da das Azure SDK für Java mit allen SLF4J-Protokollierungsframeworks funktioniert, sollten Sie das [SLF4J-Benutzerhandbuch](http://www.slf4j.org/manual.html) zu Rate ziehen, um weitere Informationen zu erhalten. Wenn Sie Log4j verwenden, finden Sie auch zahlreiche Konfigurationsanleitungen auf der zugehörigen Website. Weitere Informationen finden Sie unter [Willkommen bei Log4j 2!](https://logging.apache.org/log4j/2.x/manual/index.html)

Nachdem Sie die Protokollierung verstanden haben, können Sie sich die Integrationen von Azure in Frameworks wie [Spring](../spring-framework/spring-boot-starters-for-azure.md) und [MicroProfile](../eclipse-microprofile/index.yml) ansehen.