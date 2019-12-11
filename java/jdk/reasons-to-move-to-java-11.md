---
title: Argumente für die Migration zu Java 11
description: Eine Zusammenfassung für Entscheidungsträger, die wissen möchten, welche Vorteile eine Migration von Java 8 zu Java 11 hat.
author: dsgrieve
manager: maverberg
tags: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.openlocfilehash: 7daf058c2abebbf2cca85dadc4f9ffe3e8771fa1
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812221"
---
# <a name="reasons-to-move-to-java-11"></a>Argumente für die Migration zu Java 11

Die Frage ist nicht, *ob* Sie zu Java 11 migrieren sollten, sondern *wann*. In ein paar Jahren wird Java 8 nicht mehr unterstützt, und Benutzer müssen zu Java 11 migrieren. Die Migration zu Java 11 hat unserer Meinung nach gewisse Vorteile, und wir plädieren dafür, diese Migration baldmöglichst durchzuführen.

Seit Java 8 sind neue Features und Erweiterungen hinzugekommen. Es gibt beachtliche Ergänzungen und Änderungen für die API sowie Erweiterungen zur Verbesserung des Starts, der Leistung und der Speicherauslastung.

## <a name="transitioning-to-java-11"></a>Migrieren zu Java 11

Die Migration zu Java 11 kann schrittweise erfolgen. Code muss *keine* Java-Module verwenden, um unter Java 11 ausgeführt werden zu können. Java 11 kann zum Ausführen von Code verwendet werden, der mit JDK 8 entwickelt und erstellt wurde.
Es gibt jedoch einige potenzielle Probleme, die hauptsächlich mit veralteten APIs und Klassenladeprogrammen sowie mit der veralteten Reflektion zusammenhängen.

Ein umfassender Leitfaden für die Migration von Java 8 zu Java 11 wird in Kürze von der Java-Entwicklungsgruppe von Microsoft veröffentlicht. Bis dahin stehen zahlreiche Leitfäden für die Migration von Java 8 zu Java 9 zur Verfügung, die Sie bei Ihren ersten Schritten unterstützen. Beispiele wären etwa [Java-Plattform, Standard-Edition: Oracle JDK 9-Migrationsleitfaden](https://docs.oracle.com/javase/9/migrate/toc.htm) und [Der Stand des Modulsystems: Kompatibilität und Migration](http://openjdk.java.net/projects/jigsaw/spec/sotms/#compatibility--migration).

## <a name="high-level-changes-between-java-8-and-11"></a>Allgemeine Änderungen zwischen Java 8 und 11

In diesem Abschnitt sind nicht alle Änderungen aufgeführt, die in den Java-Versionen 9 \[[1](#ref1)\], 10 \[[2](#ref2)\] und 11 \[[3](#ref3)\] vorgenommen wurden. Vielmehr werden Änderungen mit Auswirkungen auf die Leistung, Diagnose und Produktivität hervorgehoben.

### <a name="modules-4ref4"></a>Module \[[4](#ref4)\]

Module dienen zur Behandlung von Problemen bei der Konfiguration und Kapselung, die in umfangreichen, am *Klassenpfad* ausgeführten Anwendungen schwierig zu bewältigen sind. Bei einem *Modul* handelt es sich um eine selbstbeschreibende Sammlung von Java-Klassen und -Schnittstellen sowie von zugehörigen Ressourcen.

Module ermöglichen die Anpassung von Laufzeitkonfigurationen, die nur die für eine Anwendung erforderlichen Komponenten enthalten. Diese Anpassung sorgt für einen geringeren Speicherbedarf und ermöglicht die statische Verknüpfung einer Anwendung (mithilfe von [jlink](https://docs.oracle.com/en/java/javase/11/tools/jlink.html)) mit einer benutzerdefinierten Runtime für die Bereitstellung. Der geringere Speicherbedarf ist insbesondere in einer Microservices-Architektur von Vorteil.

Intern können Module von JVM auf eine Weise genutzt werden, die das Laden von Klassen effizienter macht. Das Ergebnis ist eine kleinere, kompaktere und schneller startende Runtime. Optimierungstechniken, die von JVM zur Verbesserung der Anwendungsleistung verwendet werden, können effektiver genutzt werden, da Module codieren, welche Komponenten eine Klasse benötigt.

Programmierer können mithilfe von Modulen eine starke Kapselung erreichen, indem sie eine explizite Deklaration der von einem Modul exportierten Pakete und der erforderlichen Komponenten erzwingen und den reflektierenden Zugriff beschränken.
Dieses Maß an Kapselung trägt zur Verbesserung der Sicherheit und Verwaltbarkeit von Anwendungen bei.

Eine Anwendung kann weiterhin den *Klassenpfad* verwenden und muss nicht auf Module umgestellt werden, um unter Java 11 ausgeführt werden zu können.

### <a name="profiling-and-diagnostics"></a>Profilerstellung und Diagnose

#### <a name="java-flight-recorder-5ref5"></a>Java Flight Recorder \[[5](#ref5)\]

Java Flight Recorder (JFR) sammelt Diagnose- und Profilerstellungsdaten einer aktiven Java-Anwendung. JFR hat nur minimale Auswirkungen auf die aktive Java-Anwendung. Die gesammelten Daten können dann mit Java Mission Control (JMC) und anderen Tools analysiert werden. In Java 8 waren JFR und JMC kommerzielle Features. In Java 11 stehen sie dagegen als Open-Source-Features zur Verfügung.

#### <a name="java-mission-control-6ref6"></a>Java Mission Control \[[6](#ref6)\]

Java Mission Control (JMC) ist eine Open-Source-Lösung in Java und bietet eine grafische Darstellung der von Java Flight Recorder (JFR) gesammelten Daten.
11. Neben allgemeinen Informationen zur aktiven Anwendung kann der Benutzer mit JMC auch Detailinformationen anzeigen. JFR und JMC können zur Diagnose von Runtimeproblemen wie Speicherverlusten, GC-Overhead, „heißen“ Methoden, Threadengpässen und blockierenden Eingaben/Ausgaben verwendet werden.

#### <a name="unified-logging-7ref7"></a>Einheitliche Protokollierung \[[7](#ref7)\]

Java 11 verfügt über ein gemeinsames Protokollierungssystem für alle JVM-Komponenten.
Dank dieses einheitlichen Protokollierungssystems kann der Benutzer definieren, welche Komponenten in welchem Umfang protokolliert werden sollen. Diese differenzierte Protokollierung ist hilfreich für die Ursachenanalyse bei JVM-Abstürzen sowie für die Diagnose von Leistungsproblemen in einer Produktionsumgebung.

#### <a name="low-overhead-heap-profiling-8ref8"></a>Heapprofilerstellung mit geringem Mehraufwand \[[8](#ref8)\]

Java Virtual Machine Tool Interface (JVMTI) wurde eine neue API zur Stichprobenentnahme für Java-Heapzuordnungen hinzugefügt. Die Stichprobenentnahme verursacht nur einen geringen Mehraufwand und kann kontinuierlich aktiviert werden. Die Heapzuordnung kann zwar mit Java Flight Recorder (JFR) überwacht werden, die Stichprobenentnahme in JFR funktioniert jedoch nur für Zuordnungen. Darüber hinaus kann es bei der JFR-Implementierung vorkommen, dass Zuordnungen nicht erkannt werden. Im Gegensatz dazu kann die Heapstichprobenentnahme in Java 11 Informationen zu aktiven und inaktiven Objekten bereitstellen.

Dieses neue Feature wird zunehmend von APM-Anbietern (Application Performance Monitoring, Anwendungsleistungsüberwachung) genutzt, und die Java-Entwicklungsgruppe untersucht das Verwendungspotenzial dieses Features in Kombination mit Azure-Leistungsüberwachungstools.

#### <a name="stackwalker-9ref9"></a>StackWalker \[[9](#ref9)\]

Bei der Protokollierung wird häufig eine Momentaufnahme des Stapels für den aktuellen Thread abgerufen. Die Frage ist jedoch, wie viel von der Stapelüberwachung protokolliert werden soll (falls überhaupt). Es kann beispielsweise sein, dass die Stapelüberwachung nur für eine bestimmte Ausnahme einer Methode von Interesse ist. Die in Java 9 hinzugefügte StackWalker-Klasse liefert eine Momentaufnahme des Stapels und bietet Methoden, mit denen Programmierer die Nutzung der Stapelüberwachung präzise steuern können.

### <a name="garbage-collection-10ref10"></a>Garbage Collection \[[10](#ref10)\]

In Java 11 stehen folgende Garbage Collectors zur Verfügung: seriell, parallel, Garbage-First und Epsilon. Der Standard-Garbage Collector in Java 11 ist der Garbage-First-Garbage Collector (G1GC).

Der Vollständigkeit halber werden hier noch drei weitere Collectors erwähnt. Der Z-Garbage Collector (ZGC) ist ein gleichzeitiger Collector mit geringer Wartezeit, der versucht, Pausenzeiten unter 10 ms zu halten. ZGC ist als experimentelles Feature in Java 11 verfügbar. Der Shenandoah-Collector verkürzt GC-Pausenzeiten, indem mehr Garbage Collections gleichzeitig mit dem aktiven Java-Programm ausgeführt werden.
Shenandoah ist ein experimentelles Feature in Java 12. Es sind jedoch Backports für Java 11 verfügbar. Der CMS-Collector (Concurrent Mark and Sweep, gleichzeitiges Markieren und Bereinigen) ist zwar verfügbar, aber seit Java 9 als veraltet eingestuft.

JVM legt GC-Standardwerte für den durchschnittlichen Anwendungsfall fest. Diese Standardwerte sowie andere GC-Einstellungen müssen meist optimiert werden, um den bestmöglichen Durchsatz oder die bestmögliche Wartezeit für die Anforderungen der Anwendung zu erreichen.
Für eine ordnungsgemäße GC-Optimierung sind umfassende GC-Kenntnisse erforderlich. Dieses Know-how wird von der [Java-Entwicklungsgruppe von Microsoft](mailto:javaplatformgroup@microsoft.com) bereitstellt.

#### <a name="g1gc"></a>G1GC

Der Standard-Garbage Collector in Java 11 ist der G1-Garbage Collector (G1GC). G1GC ist für ein ausgewogenes Verhältnis zwischen Wartezeit und Durchsatz konzipiert. Der G1-Garbage Collector versucht, einen hohen Durchsatz zu erzielen, indem Pausenzeitenziele mit hoher Wahrscheinlichkeit erreicht. G1GC ist auf die Vermeidung vollständiger ‚Garbage Collections ausgelegt. Sollte Arbeitsspeicher allerdings von den gleichzeitigen Garbage Collections nicht schnell genug freigegeben werden, wird auf eine vollständige Garbage Collection zurückgegriffen. Die vollständige Garbage Collection verwendet die gleiche Anzahl paralleler Arbeitsthreads wie die neuen und gemischten Garbage Collections.

#### <a name="parallel-gc"></a>Paralleler GC

Der parallele Garbage Collector ist der Standard-Garbage Collector in Java 8. Bei dem parallelen GC handelt es sich um einen Durchsatz-Garbage Collector, der mehrere Threads verwendet, um die Garbage Collection zu beschleunigen.

#### <a name="epsilon-11ref11"></a>Epsilon \[[11](#ref11)\]

Der Epsilon-Garbage Collector kümmert sich um Zuordnungen, gibt aber keinen Arbeitsspeicher frei. Ist der Heap erschöpft, wird JVM heruntergefahren.
Epsilon eignet sich für kurzlebige Dienste sowie für Anwendungen ohne „Datenmüll“.

#### <a name="improvements-for-docker-containers-12ref12"></a>Verbesserungen für Docker-Container \[[12](#ref12)\]

Vor Java 10 wurden für einen Container festgelegte Arbeitsspeicher- und CPU-Einschränkungen von JVM nicht erkannt. In Java 8 legt JVM beispielsweise die maximale Heapgröße standardmäßig auf ¼ des physischen Speichers des zugrunde liegenden Hosts fest. Ab Java 10 verwendet JVM von Containersteuerungsgruppen (cgroups) festgelegte Einschränkungen, um Arbeitsspeicher- und CPU-Limits festzulegen (siehe Hinweis weiter unten).
Die standardmäßige maximale Heapgröße beträgt zum Beispiel ¼ des Container-Arbeitsspeicherlimits (also beispielsweise 500 MB für „-m2G“).

Darüber hinaus wurden JVM-Optionen hinzugefügt, mit denen Docker-Containerbenutzer präziser steuern können, wie viel Systemspeicher für den Java-Heap verwendet wird.

Diese Unterstützung ist standardmäßig aktiviert und steht nur auf Linux-basierten Plattformen zur Verfügung.

> [!NOTE]
> Der Großteil der Arbeit der Containersteuerungsgruppen wurde im Zuge von „jdk8u191“ zu Java 8 zurückportiert. Weitere Verbesserungen werden allerdings nicht unbedingt zu Java 8 zurückportiert.

#### <a name="multi-release-jar-files-13ref13"></a>JAR-Dateien mit mehreren Releases \[[13](#ref13)\]

In Java 11 kann eine JAR-Datei mit mehreren Java-releasespezifischen Versionen von Klassendateien erstellt werden. Dank JAR-Dateien mit mehreren Releases können Bibliotheksentwickler mehrere Versionen von Java unterstützen, ohne mehrere Versionen von JAR-Dateien bereitstellen zu müssen. Nutzer dieser Bibliotheken müssen dank JAR-Dateien mit mehreren Releases keine passenden spezifischen JAR-Dateien für spezifische Runtimeziele mehr verwenden.

## <a name="miscellaneous-performance-improvements"></a>Sonstige Leistungsverbesserungen

Die folgenden JVM-Änderungen wirken sich direkt auf die Leistung aus:

-   **JEP 197: Segmentierter Codecache** \[[14](#ref14)\] – Unterteilt den Codecache in individuelle Segmente. Diese Segmentierung ermöglicht eine bessere Steuerung des JVM-Speicherbedarfs, verkürzt die Überprüfungszeit kompilierter Methoden, sorgt für eine erhebliche Verringerung der Fragmentierung des Codecaches und verbessert die Leistung.

-   **JEP 254: Kompakte Zeichenfolgen** \[[15](#ref15)\] – Ändert die interne Darstellung einer Zeichenfolge von zwei Bytes pro Zeichen in ein bis zwei Bytes pro Zeichen (abhängig von der Zeichencodierung). Da die meisten Zeichenfolgen ISO-8859-1-/LATIN-1-Zeichen enthalten, halbiert sich durch diese Änderung der Speicherplatzbedarf für Zeichenfolgen.

-   **JEP 310: Gemeinsame Nutzung von Anwendungsklassendaten** \[[16](#ref16)\] – Die gemeinsame Nutzung von Klassendaten verkürzt die Startzeit dadurch, dass archivierte Klassen zur Laufzeit im Speicher abgebildet werden können. Die gemeinsame Nutzung von Anwendungsklassendaten erweitert die gemeinsame Nutzung von Klassendaten durch die Möglichkeit, Anwendungsklassen im CDS-Archiv zu platzieren. Wenn mehrere JVMs die gleiche Archivdatei nutzen, wird weniger Arbeitsspeicher beansprucht, und die Reaktionszeit des Systems verbessert sich.

-   **JEP 312: Threadlokale Handshakes** \[[17](#ref17)\] – Ermöglicht das Ausführen eines Rückrufs für Threads ohne Erstellung eines globalen VM-Sicherungspunkts. Dies trägt zur Verkürzung der Wartezeit für den virtuellen Computer bei, da sich die Anzahl globaler Sicherungspunkte verringert.

-   **Verzögerte Zuordnung von Compilerthreads** \[[18](#ref18)\] – Im mehrstufigen Kompilierungsmodus startet der virtuelle Computer zahlreiche Compilerthreads.
    Dieser Modus ist die Standardeinstellung für Systeme mit vielen CPUs. Die Threads werden unabhängig vom verfügbaren Arbeitsspeicher oder der Anzahl von Kompilierungsanforderungen erstellt. Von Threads wird auch dann Arbeitsspeicher beansprucht, wenn sie sich im Leerlauf befinden (was größtenteils der Fall ist). Dies führt zu einer ineffizienten Ressourcennutzung. Zur Behebung dieses Problems wurde die Implementierung so geändert, dass beim Start nur ein einzelner Compilerthread jedes Typs gestartet wird. Das Starten zusätzlicher Threads und das Herunterfahren nicht verwendeter Threads wird dynamisch gehandhabt. 

Die folgenden Änderungen an den Kernbibliotheken haben Auswirkungen auf die Leistung von neuem oder geändertem Code:

-   **JEP 193: Variable Handles** \[[19](#ref19)\] – Definiert eine Standardmethode zum Aufrufen der Entsprechungen verschiedener Vorgänge vom Typ „java.util.concurrent.atomic“ und „sun.misc.Unsafe“ für Objektfelder und Arrayelemente, einen Standardsatz von Fence-Vorgängen zur differenzierten Steuerung der Speicheranordnung sowie einen standardmäßigen Erreichbarkeits-Fence-Vorgang, um sicherzustellen, dass ein referenziertes Objekt zuverlässig erreichbar bleibt.

-   **JEP 269: Praktische Factorymethoden für Sammlungen** \[[20](#ref20)\] – Definiert Bibliotheks-APIs, um die Erstellung von Sammlungs- und Zuordnungsinstanzen mit wenigen Elementen zu vereinfachen. Die statischen Factorymethoden für die Sammlungsschnittstellen zur Erstellung kompakter, unveränderbarer Sammlungsinstanzen. Diese Instanzen sind grundsätzlich effizienter. Die APIs erstellen kompakt dargestellte Sammlungen ohne Wrapperklasse.

-   **JEP 285: Spin-Wait-Hinweise** \[[21](#ref21)\] – Stellt eine API bereit, mit der Java dem Runtimesystem signalisieren kann, dass es sich in einer Spinloop befindet. Bestimmte Hardwareplattformen profitieren davon, wenn ihnen von der Software signalisiert wird, dass ein Thread ausgelastet ist bzw. sich in einem Wartezustand befindet.

-   **JEP 321: HTTP-Client (Standard)** \[[22](#ref22)\] – Stellt eine neue HTTP-Client-API bereit, die HTTP/2 und WebSocket implementiert und die Legacy-API „HttpURLConnection“ ersetzen kann.

## <a name="references"></a>Referenzen

<a id="ref1">\[1\]</a> Oracle Corporation, Versionshinweise für Java Development Kit 9 (online). Verfügbar unter https://www.oracle.com/technetwork/java/javase/9u-relnotes-3704429.html.
(Zugriff am 13. November 2019)

<a id="ref2">\[2\]</a> Oracle Corporation, Versionshinweise für Java Development Kit 10 (online). Verfügbar unter https://www.oracle.com/technetwork/java/javase/10u-relnotes-4108739.html.
(Zugriff am 13. November 2019)

<a id="ref3">\[3\]</a> Oracle Corporation, Versionshinweise für Java Development Kit 11 (online). Verfügbar unter https://www.oracle.com/technetwork/java/javase/11u-relnotes-5093844.html.
(Zugriff am 13. November 2019)

<a id="ref4">\[4\]</a> Oracle Corporation, „Project Jigsaw“, 22. September 
2017. (online). Verfügbar unter http://openjdk.java.net/projects/jigsaw/.
(Zugriff am 13. November 2019)

<a id="ref5">\[5\]</a> Oracle Corporation, „JEP 328: Flight Recorder“, 9. September 2018 (online). Verfügbar unter http://openjdk.java.net/jeps/328. (Zugriff am 13. November 2019)

<a id="ref6">\[6\]</a> Oracle Corporation, „Mission Control“, 25. April 2019 (online). Verfügbar unter https://wiki.openjdk.java.net/display/jmc/Main. (Zugriff am 13. November 2019)

<a id="ref7">\[7\]</a> Oracle Corporation, „JEP 158: Einheitliche JVM-Protokollierung“, 14. Februar 2019 (online). Verfügbar unter http://openjdk.java.net/jeps/158.
(Zugriff am 13. November 2019)

<a id="ref8">\[8\]</a> Oracle Corporation, „JEP 331: Heapprofilerstellung mit geringem Mehraufwand“, 5. September 2018 (online). Verfügbar unter http://openjdk.java.net/jeps/331. (Zugriff am 13. November 2019)

<a id="ref9">\[9\]</a> Oracle Corporation, „JEP 259: Stackwalk-API“, 18. Juli 2017 (online).
Verfügbar unter http://openjdk.java.net/jeps/259. (Zugriff am 13. November 2019)

<a id="ref10">\[10\]</a> Oracle Corporation, „JEP 248: Festlegen von G1 als Standard-Garbage Collector“, 12. September 2017 (online). Verfügbar unter http://openjdk.java.net/jeps/248. (Zugriff am 13. November 2019)

<a id="ref11">\[11\]</a> Oracle Corporation, „JEP 318: Epsilon: Ein No-Op-Garbage Collector“, 24. September 2018
(online). Verfügbar unter http://openjdk.java.net/jeps/318. (Zugriff am 13. November 2019)

<a id="ref12">\[12\]</a> Oracle Corporation, „JDK-8146115: Verbessern der Erkennung von Docker-Containern und der Verwendung der Ressourcenkonfiguration“, 16. September 2019
(online). Verfügbar unter https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=JDK-8146115.
(Zugriff am 13. November 2019)

<a id="ref13">\[13\]</a> Oracle Corporation, „JEP 238: JAR-Dateien mit mehreren Releases“, 22. Juni 2017 (online). Verfügbar unter http://openjdk.java.net/jeps/238. (Zugriff am 13. November 2019)

<a id="ref14">\[14\]</a> Oracle Corporation, „JEP 197: Segmentierter Codecache“, 28. April 2017 (online).
Verfügbar unter http://openjdk.java.net/jeps/197. (Zugriff am 13. November 2019)

<a id="ref15">\[15\]</a> Oracle Corporation, „JEP 254: Kompakte Zeichenfolgen“, 18. Mai 2019 (online). Verfügbar unter http://openjdk.java.net/jeps/254.
(Zugriff am 13. November 2019)

<a id="ref16">\[16\]</a> Oracle Corporation, „JEP 310: Gemeinsame Nutzung von Anwendungsklassendaten“, 17. August 2018 (online). Verfügbar unter https://openjdk.java.net/jeps/310. (Zugriff am 13. November 2019)

<a id="ref17">\[17\]</a> Oracle Corporation, „JEP 312: Threadlokale Handshakes“, 21. August 2019
(online). Verfügbar unter https://openjdk.java.net/jeps/312. (Zugriff am 13. November 2019)

<a id="ref18">\[18\]</a> Oracle Corporation, „JDK-8198756: Verzögerte Zuordnung von Compilerthreads“, 29. Oktober 2018 (online). Verfügbar unter https://bugs.java.com/bugdatabase/view\_bug.do?bug\_id=8198756.
(Zugriff am 13. November 2019)

<a id="ref19">\[19\]</a> Oracle Corporation, „JEP 193: Variable Handles“, 17. August 2017 (online). Verfügbar unter https://openjdk.java.net/jeps/193. (Zugriff am 13. November 2019)

<a id="ref20">\[20\]</a> Oracle Corporation, „JEP 269: Praktische Factorymethoden für Sammlungen“, 26. Juni 2017 (online). Verfügbar unter https://openjdk.java.net/jeps/269.
(Zugriff am 13. November 2019)

<a id="ref21">\[21\]</a> Oracle Corporation, „JEP 285: Spin-Wait-Hinweise“, 20. August 2017 (online). Verfügbar unter https://openjdk.java.net/jeps/285. (Zugriff am 13. November 2019)

<a id="ref22">\[22\]</a> Oracle Corporation, „JEP 321: HTTP-Client (Standard)“, 27. September 2018 (online).
Verfügbar unter https://openjdk.java.net/jeps/321. (Zugriff am 13. November 2019)
