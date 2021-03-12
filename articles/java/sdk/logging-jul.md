---
title: Protokollieren mit dem Azure SDK für Java und java.util.logging
description: Übersicht über die Azure SDK für Java-Integration mit java.util.logging
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: ca3a431debec21bad2099371e711c1df73d9b344
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118582"
---
# <a name="log-with-the-azure-sdk-for-java-and-javautillogging"></a>Protokollieren mit dem Azure SDK für Java und java.util.logging

Dieser Artikel bietet eine Übersicht, wie Sie Protokollierung zu Anwendungen mithilfe von java.util.logging hinzufügen, die das Azure SDK für Java verwenden. Das java.util.logging-Framework ist Teil des JDK. Wie unter [Konfigurieren von Protokollierung im Azure SDK für Java](logging-overview.md) beschrieben, führen alle Azure-Clientbibliotheken Protokollierung über [SLF4J](http://www.slf4j.org/) aus, damit Sie Protokollierungsframeworks wie [java.util.logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/Logger.html) verwenden können.

Zum Aktivieren von java.util.logging müssen Sie zwei Schritte ausführen:

1. Binden Sie den SLF4J-Adapter für java.util.logging als Abhängigkeit ein.
2. Erstellen Sie im Projektverzeichnis */src/main/resources* eine Datei namens *logging.properties*.

Weitere Informationen zum Konfigurieren der Protokollierung finden Sie unter [Konfigurieren der Protokollierungsausgabe](https://docs.oracle.com/cd/E23549_01/doc.1111/e14568/handler.htm) in der Oracle-Dokumentation.

## <a name="add-the-maven-dependency"></a>Hinzufügen der Maven-Abhängigkeit

Um die Maven-Abhängigkeit hinzuzufügen, fügen Sie den folgenden XML-Code in die Datei *pom.xml* des Projekts ein. Ersetzen Sie die Versionsnummer *1.7.30* durch die neueste veröffentlichte Versionsnummer, die auf der Seite [SLF4J JDK14 Binding](https://mvnrepository.com/artifact/org.slf4j/slf4j-jdk14) (SLF4J JDK14-Bindung) angezeigt wird.

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>1.7.30</version> <!-- replace this version with the latest available version on Maven central -->
</dependency>
```

## <a name="add-loggingproperties-to-your-project"></a>Hinzufügen von logging.properties zu Ihrem Projekt

Um mithilfe von `java.util.logging` zu protokollieren, erstellen Sie im Verzeichnis */src/main/resources* Ihres Projekts eine Datei namens *logging.properties*. Diese Datei enthält die Protokollierungskonfigurationen, mit denen die Protokollierungsanforderungen angepasst werden können. Weitere Informationen finden Sie unter [Java-Protokollierung: Konfiguration](http://tutorials.jenkov.com/java-logging/configuration.html).

Wenn Sie einen anderen Dateinamen als *logging.properties* verwenden möchten, legen Sie die `java.util.logging.config.file`-Systemeigenschaft fest. Diese Eigenschaft muss festgelegt werden, bevor die Protokollierungsinstanz erstellt wird.

### <a name="console-logging"></a>Konsolenprotokollierung

Sie können eine Konfiguration erstellen, um die Protokollierung wie im folgenden Beispiel gezeigt in der Konsole auszugeben. Dieses Beispiel ist so konfiguriert, dass alle Protokollierungsereignisse unabhängig von der Quelle protokolliert werden, bei denen es sich um die Ebene INFO oder höher handelt.

```properties
handlers = java.util.logging.ConsoleHandler
.level = INFO

java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.SimpleFormatter.format=[%1$tF %1$tT] [%4$s] %5$s %n
```

### <a name="log-to-a-file"></a>Protokollieren in eine Datei

Das oben genannte Beispiel protokolliert in die Konsole, was normalerweise nicht der bevorzugte Ort für Protokolle ist. Verwenden Sie zum Konfigurieren der Protokollierung in eine Datei stattdessen die folgende Konfiguration:

```properties
handlers = java.util.logging.FileHandler
.level = INFO

java.util.logging.FileHandler.pattern = %h/myapplication.log
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.level = INFO
```

Mit diesem Code wird eine Datei namens *myapplication.log* in Ihrem Basisverzeichnis (`%h`) erstellt. Diese Protokollierung unterstützt keine automatische Dateirotation nach einem bestimmten Zeitraum. Wenn Sie diese Funktionalität benötigen, müssen Sie einen Scheduler schreiben, um die Protokolldateirotation zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die Konfiguration von `java.util.logging` behandelt und erläutert, wie das Azure SDK für Java für Protokollierung verwendet wird. Da das Azure SDK für Java mit allen SLF4J-Protokollierungsframeworks funktioniert, sollten Sie das [SLF4J-Benutzerhandbuch](http://www.slf4j.org/manual.html) zu Rate ziehen, um weitere Informationen zu erhalten.

Nachdem Sie die Protokollierung verstanden haben, können Sie sich die Integrationen von Azure in Frameworks wie [Spring](../spring-framework/spring-boot-starters-for-azure.md) und [MicroProfile](../eclipse-microprofile/index.yml) ansehen.