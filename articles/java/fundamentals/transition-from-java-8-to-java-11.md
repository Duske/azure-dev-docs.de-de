---
title: Umstellung von Java 8 auf Java 11
titleSuffix: Azure
description: Leitfaden zur Verwaltung der Umstellung von Java 8 auf Java 11.
author: dsgrieve
manager: maverbur
tags: java
ms.service: azure
ms.devlang: java
ms.topic: article
ms.date: 11/19/2019
ms.author: dagrieve
ms.custom: devx-track-java
ms.openlocfilehash: 6aa930bb4ee5c6a5019e33b3d8075765b92f0bfc
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379784"
---
# <a name="transition-from-java-8-to-java-11"></a>Umstellung von Java 8 auf Java 11

Für die Umstellung des Codes von Java 8 auf Java 11 gibt es keine Universallösung.
Bei umfangreicheren Anwendungen kann die Umstellung von Java 8 auf Java 11 mit erheblichem Arbeitsaufwand verbunden sein. Potenzielle Probleme sind die Entfernung der API, veraltete Pakete, Nutzung der internen API, Änderungen an Klassenladeprogrammen und Änderungen der automatischen Speicherbereinigung (Garbage Collection). 

Die allgemeinen Lösungsansätze sind die versuchte Ausführung unter Java 11 ohne erneutes Kompilieren oder die vorherige Kompilierung per JDK 11. Wenn das Ziel darin besteht, für eine Anwendung so schnell wie möglich die Betriebsbereitschaft herzustellen, ist die versuchte Ausführung unter Java 11 häufig die beste Vorgehensweise. Für eine Bibliothek ist das Ziel die Veröffentlichung eines Artefakts, das per JDK 11 kompiliert und getestet wird.

Der Aufwand für die Umstellung auf Java 11 lohnt sich. Seit Java 8 wurden neue Features hinzugefügt und Erweiterungen durchgeführt. Diese Features und Erweiterungen sorgen für eine Verbesserung in Bezug auf den Start, die Leistung und die Speicherauslastung und ermöglichen eine bessere Integration in Container. Darüber hinaus wurde die API erweitert und geändert, um die Entwicklerproduktivität zu verbessern. 

In diesem Dokument werden die Tools zum Untersuchen des Codes beschrieben. Außerdem werden Probleme behandelt, die ggf. auftreten können, und es werden Empfehlungen zur Behebung angegeben. Es ist auch ratsam, andere Leitfäden zu lesen, z. B. den [Oracle JDK-Migrationsleitfaden](https://docs.oracle.com/en/java/javase/11/migrate/index.html). Es wird hier nicht beschrieben, wie vorhandener Code [modular](http://openjdk.java.net/projects/jigsaw) gemacht wird.  


## <a name="the-toolbox"></a>Toolbox

Java 11 verfügt über die beiden Tools *jdeprscan* und *jdeps*, die nützlich zum Ermitteln von potenziellen Problemen sind. Diese Tools können für vorhandene Klassen- oder JAR-Dateien ausgeführt werden. Sie können die Umstellung bewerten, ohne erneut kompilieren zu müssen. 

[jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) sucht danach, ob eine veraltete oder entfernte API genutzt wird.
Die Nutzung einer veralteten API ist kein Blockierproblem, aber Sie sollten sich trotzdem darum kümmern. Ist eine aktualisierte JAR-Datei verfügbar? Müssen Sie bei Verwendung einer veralteten API ein Problem melden? Die Nutzung einer entfernten API ist ein Blockierproblem, das behoben werden muss, bevor Sie die Ausführung unter Java 11 versuchen können.


[jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) ist ein Analysetool für Java-Klassenabhängigkeiten. Bei Verwendung mit der Option `--jdk-internals` teilt *jdeps* Ihnen mit, welche Klasse von welcher internen API abhängig ist. Sie können unter Java 11 weiterhin die interne API verwenden, aber die Umstellung auf die neue Vorgehensweise sollte Priorität haben. Die OpenJDK-Wiki-Seite zum [Analysetool für Java-Abhängigkeiten](https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool) enthält Informationen zu den empfohlenen Ersatzlösungen für einige häufig verwendete interne JDK-APIs. 

Es sind *jdeps*- und *jdeprscan*-Plug-Ins für Gradle und Maven verfügbar. Wir empfehlen Ihnen, diese Tools Ihren Buildskripts hinzuzufügen. 

> [!div class="mx-tdBreakAll"]
> |Tool|Gradle-Plug-In|Maven-Plug-In|
> |-|-|-|
> |jdeps|[jdeps-gradle-plugin](https://github.com/kordamp/jdeps-gradle-plugin)|[Apache Maven-JDeps-Plug-In](https://maven.apache.org/plugins/maven-jdeps-plugin/index.html)|
> |jdeprscan|[jdeprscan-gradle-plugin](https://github.com/kordamp/jdeprscan-gradle-plugin)|[Apache Maven-JDeprScan-Plug-In](https://maven.apache.org/plugins/maven-jdeprscan-plugin/index.html)|

Der eigentliche Java-Compiler *javac* ist ein weiteres Tool, das in der Toolbox enthalten ist. Die Warnungen und Fehler, die Sie von *jdeprscan* und *jdeps* erhalten, stammen vom Compiler.  Der Vorteil der Nutzung von *jdeprscan* und *jdeps* ist, dass Sie diese Tools für vorhandene JAR- und Klassendateien, einschließlich Drittanbieterbibliotheken, ausführen können.

Das Warnen vor der Nutzung des reflektierenden Zugriffs auf die gekapselte API ist mit *jdeprscan* und *jdeps* nicht möglich. Der reflektierende Zugriff wird zur Laufzeit überprüft. Letztendlich müssen Sie den Code unter Java 11 ausführen, um ganz sicher sein zu können.

### <a name="using-jdeprscan"></a>Verwenden von jdeprscan

Die einfachste Möglichkeit zur Verwendung von [jdeprscan](https://docs.oracle.com/en/java/javase/11/tools/jdeprscan.html) ist die Zuordnung einer JAR-Datei aus einem vorhandenen Build. Sie können auch ein Verzeichnis, z. B. das Ausgabeverzeichnis des Compilers, oder einen einzelnen Klassennamen zuordnen. Verwenden Sie die Option `--release 11`, um die aktuellste Liste mit den veralteten APIs zu erhalten. Wenn Sie priorisieren möchten, welche veraltete API verwendet werden soll, können Sie die Option auf `--release 8` zurücksetzen. Eine API, die in Java 8 als veraltet gekennzeichnet wurde, wird wahrscheinlich früher als eine API entfernt, die erst seit kurzer Zeit veraltet ist. 

```console
jdeprscan --release 11 my-application.jar
```

Mit dem Tool *jdeprscan* wird eine Fehlermeldung generiert, falls es beim Auflösen einer abhängigen Klasse zu Problemen kommt.
Beispiel: `error: cannot find class org/apache/logging/log4j/Logger`. Das Hinzufügen von abhängigen Klassen zu `--class-path` oder die Verwendung des Klassenpfads der Anwendung wird empfohlen, aber das Tool setzt die Untersuchung auch ohne diese Angabe fort.
Das Argument lautet *&#8209;&#8209;class&#8209;path*. Es funktionieren keine anderen Varianten des „class-path“-Arguments.

```console
jdeprscan --release 11 --class-path log4j-api-2.13.0.jar my-application.jar
error: cannot find class sun/misc/BASE64Encoder
class com/company/Util uses deprecated method java/lang/Double::<init>(D)V
```
Diese Ausgabe sagt aus, dass die `com.company.Util`-Klasse einen veralteten Konstruktor der `java.lang.Double`-Klasse aufruft. Von Javadoc wird die API empfohlen, die anstelle der veralteten API verwendet werden soll. `error: cannot find class sun/misc/BASE64Encoder` kann nicht gelöst werden, da die API entfernt wurde. Seit Java 8 sollte `java.util.Base64` verwendet werden. 

Führen Sie `jdeprscan --release 11 --list` aus, um Informationen dazu zu erhalten, welche APIs seit Java 8 als veraltet gekennzeichnet wurden. Führen Sie `jdeprscan --release 11 --list --for-removal` aus, um eine Liste mit den APIs anzuzeigen, die entfernt wurden.

### <a name="using-jdeps"></a>Verwenden von jdeps

Verwenden Sie [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) mit der Option `--jdk-internals`, um Abhängigkeiten von der internen JDK-API zu ermitteln. Die Befehlszeilenoption `--multi-release 11` wird für dieses Beispiel benötigt, da es sich bei *log4j-core-2.13.0.jar* um eine [JAR-Datei mit mehreren Releases](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files) handelt. Ohne diese Option tritt für *jdeps* ein Fehler auf, wenn eine JAR-Datei mit mehreren Releases gefunden wird. Die Option gibt an, welche Version der Klassendateien untersucht werden soll. 

```console
jdeps --jdk-internals --multi-release 11 --class-path log4j-core-2.13.0.jar my-application.jar
Util.class -> JDK removed internal API
Util.class -> jdk.base
Util.class -> jdk.unsupported
   com.company.Util        -> sun.misc.BASE64Encoder        JDK internal API (JDK removed internal API)
   com.company.Util        -> sun.misc.Unsafe               JDK internal API (jdk.unsupported)
   com.company.Util        -> sun.nio.ch.Util               JDK internal API (java.base)

Warning: JDK internal APIs are unsupported and private to JDK implementation that are
subject to be removed or changed incompatibly and could break your application.
Please modify your code to eliminate dependence on any JDK internal APIs.
For the most recent update on JDK internal API replacements, please check:
https://wiki.openjdk.java.net/display/JDK8/Java+Dependency+Analysis+Tool

JDK Internal API                         Suggested Replacement
----------------                         ---------------------
sun.misc.BASE64Encoder                   Use java.util.Base64 @since 1.8
sun.misc.Unsafe                          See http://openjdk.java.net/jeps/260   

```

Die Ausgabe enthält hilfreiche Hinweise dazu, wie die Nutzung der internen JDK-API beseitigt werden kann! Wo immer dies möglich ist, wird die Nutzung der Ersatz-API empfohlen. Der Name des Moduls, in dem das Paket gekapselt ist, ist in Klammern angegeben. Der Modulname kann mit `--add-exports` oder `--add-opens` verwendet werden, falls es erforderlich ist, die [Kapselung explizit aufzuheben](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66). 

Die Nutzung von *sun.misc.BASE64Encoder* oder *sun.misc.BASE64Decoder* führt in Java 11 zu einem Fehler vom Typ *java.lang.NoClassDefFoundError*. Code, für den diese APIs genutzt werden, muss so geändert werden, dass *java.util.Base64* verwendet wird. 

Versuchen Sie, die Nutzung aller APIs zu beseitigen, die aus dem Modul *jdk.unsupported* stammen. Die API aus diesem Modul verweist auf [JEP 260 (JDK Enhancement Proposal)](http://openjdk.java.net/jeps/260) als vorgeschlagene Ersatzlösung.
Kurz gesagt: JEP 260 enthält den Hinweis, dass die Verwendung der internen API unterstützt wird, bis die Ersatz-API verfügbar ist. Auch wenn für Ihren Code ggf. die interne JDK-API verwendet wird, wird er weiterhin ausgeführt (zumindest noch eine Weile). Sehen Sie sich JEP 260 an, weil darin auf Ersatzlösungen für einige interne APIs verwiesen wird. 
Beispielsweise können [Variable Handles](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/invoke/VarHandle.html) anstelle von einigen *sun.misc.Unsafe*-APIs verwendet werden. 

Mit *jdeps* kann nicht nur die Verwendung von internen JDK-Einheiten überprüft werden. Es ist ein nützliches Tool zum Analysieren von Abhängigkeiten und zum Generieren von Modulinfodateien. Weitere Informationen hierzu finden Sie in der [Dokumentation](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html).

### <a name="using-javac"></a>Verwenden von javac

Für das Kompilieren mit JDK 11 sind Updates erforderlich, um Skripts, Tools, Testframeworks und die zugehörigen Bibliotheken zu erstellen. Verwenden Sie die Option `-Xlint:unchecked` für *javac*, um die Details zur Nutzung der internen JDK-API und zu anderen Warnungen zu erhalten. Es kann auch erforderlich sein, `--add-opens` oder `--add-reads` zu verwenden, um gekapselte Pakete für den Compiler verfügbar zu machen (siehe [JEP 261](http://openjdk.java.net/jeps/261)). 

Für Bibliotheken kann das Verpacken als [JAR-Datei mit mehreren Releases](https://docs.oracle.com/en/java/javase/11/docs/specs/jar/jar.html#multi-release-jar-files) genutzt werden. Mit JAR-Dateien mit mehreren Releases können Sie sowohl Java 8- als auch Java 11-Runtimes über dieselbe JAR-Datei unterstützen. Dies erhöht die Komplexität des Builds. Die Beschreibung der Erstellung von JAR-Dateien mit mehreren Releases würde den Rahmen dieses Dokuments sprengen. 

## <a name="running-on-java-11"></a>Ausführen unter Java 11

Die meisten Anwendungen sollten ohne Änderung unter Java 11 ausgeführt werden. Als Erstes sollten Sie es mit der Ausführung unter Java 11 versuchen, ohne den Code erneut zu kompilieren. Mit dieser Ausführung soll ermittelt werden, welche Warnungen und Fehler sich dabei ergeben. Bei diesem Ansatz  
kann eine Anwendung unter Java 11 schneller ausgeführt werden, indem erst einmal nur die mindestens erforderlichen Schritte ausgeführt werden. 

Die meisten Probleme, die bei Ihnen ggf. auftreten können, können gelöst werden, ohne dass Code erneut kompiliert werden muss.
Wenn ein Problem im Code behoben werden muss, sollten Sie dies durchführen, aber die Kompilierung mit JDK 8 fortsetzen. Versuchen Sie, nach Möglichkeit die Anwendung mit `java`-Version 11 *auszuführen*, bevor Sie mit JDK 11 *kompilieren*. 

### <a name="check-command-line-options"></a>Überprüfen der Befehlszeilenoptionen

Führen Sie vor der Ausführung unter Java 11 einen schnellen Scanvorgang für die Befehlszeilenoptionen durch. 
[Optionen, die entfernt wurden](#unrecognized-options), führen zur Beendigung der Java Virtual Machine (JVM). Diese Überprüfung ist besonders wichtig, wenn Sie GC-Protokollierungsoptionen nutzen, da sich diese gegenüber Java 8 stark geändert haben. Das Tool [JaCoLine](https://jacoline.dev/about) eignet sich gut zum Erkennen von Problemen mit den Befehlszeilenoptionen. 

### <a name="check-third-party-libraries"></a>Überprüfen der Drittanbieterbibliotheken

Eine potenzielle Fehlerquelle sind die Drittanbieterbibliotheken, die nicht unter Ihrer Kontrolle sind. Sie können Drittanbieterbibliotheken proaktiv auf aktuellere Versionen aktualisieren. Alternativ können Sie abwarten, was sich bei der Ausführung der Anwendung ergibt, und nur die Bibliotheken aktualisieren, für die dies erforderlich ist. Das Problem mit der Aktualisierung aller Bibliotheken auf eine aktuelle Version besteht darin, dass die Grundursache schwieriger zu ermitteln ist, falls für die Anwendung ein Fehler auftritt. Ist der Fehler aufgrund einer aktualisierten Bibliothek aufgetreten? Oder wurde der Fehler durch eine Änderung der Runtime verursacht? Das Problem beim ausschließlichen Aktualisieren der erforderlichen Komponenten ist, dass bis zur Lösung mehrere Iterationen benötigt werden.

Die Empfehlung besteht hierbei darin, so wenig Änderungen wie möglich vorzunehmen und Drittanbieterbibliotheken [separat](#next-steps) zu aktualisieren. Beim Aktualisieren einer Drittanbieterbibliothek streben Sie normalerweise die aktuellste und höchste Version an, die mit Java 11 kompatibel ist. Je nachdem, wie weit Ihre aktuelle Version zurückliegt, kann ggf. ein etwas vorsichtigerer Ansatz ratsam sein, bei dem Sie ein Upgrade auf die erste mit Java 9+-kompatible Version durchführen. 

Zusätzlich zur Lektüre der Versionshinweise können Sie auch *jdeps* und *jdeprscan* verwenden, um die JAR-Datei zu bewerten. Von der OpenJDK Quality Group wird zudem eine Wiki-Seite zur [Qualität (Quality Outreach)](https://wiki.openjdk.java.net/display/quality/Quality+Outreach) bereitgestellt, auf der der Status der Tests vieler FOSS-Projekte (Free Open Source Software, Kostenlose Open-Source-Software) für OpenJDK-Versionen aufgelistet ist. 

### <a name="explicitly-set-garbage-collection"></a>Explizites Festlegen der Garbage Collection

Der „Parallel Garbage Collector“ (Parallel GC) wird in Java 8 standardmäßig für die automatische Speicherbereinigung genutzt. Wenn für die Anwendung die Standardeinstellung verwendet wird, sollte die GC explizit mit der Befehlszeilenoption `-XX:+UseParallelGC` festgelegt werden.
In Java 9 wurde die Standardeinstellung in „Garbage First Garbage Collector“ (G1GC) geändert. Damit die Ausführung einer Anwendung unter Java 8 und Java 11 richtig verglichen werden kann, müssen die GC-Einstellungen identisch sein. Mit dem Experimentieren mit den GC-Einstellungen sollte gewartet werden, bis die Anwendung unter Java 11 überprüft wurde. 

### <a name="explicitly-set-default-options"></a>Explizites Festlegen der Standardoptionen

Bei der Ausführung auf der HotSpot-VM führt das Festlegen der Befehlszeilenoption `-XX:+PrintCommandLineFlags` dazu, dass die Werte der von der VM festgelegten Optionen ausgegeben werden. Dies gilt vor allem für die Standardeinstellungen, die für die GC festgelegt wurden.
Verwenden Sie dieses Flag für die Ausführung unter Java 8 und die ausgegebenen Optionen für die Ausführung unter Java 11. Die Standardwerte sind für Version 8 und 11 größtenteils identisch. Die Parität ist aber sichergestellt, wenn Sie die Einstellungen aus Version 8 verwenden.

Wir empfehlen Ihnen, die Befehlszeilenoption `--illegal-access=warn` festzulegen.
In Java 11 führt die Nutzung des reflektierenden Zugriffs auf die interne JDK-API zu einer [Warnung vor einem unzulässigen reflektierenden Zugriff](#warning-an-illegal-reflective-access-operation-has-occurred).
Standardmäßig wird die Warnung nur für den ersten unzulässigen Zugriff angezeigt. Das Festlegen von `--illegal-access=warn` führt dazu, dass für *jeden* unzulässigen reflektierenden Zugriff eine Warnung angezeigt wird. Sie können eine größere Zahl von Fällen mit unzulässigem Zugriff ermitteln, wenn die Option auf *warn* festgelegt ist. Sie erhalten dann aber auch viele redundante Warnungen.  
Wenn die Anwendung unter Java 11 ausgeführt wird, können Sie `--illegal-access=deny` festlegen, um das zukünftige Verhalten der Java Runtime zu imitieren. Ab Java 16 lautet die Standardeinstellung `--illegal-access=deny`. 

### <a name="classloader-cautions"></a>Warnhinweise zu ClassLoader

In Java 8 können Sie das Systemklassen-Ladeprogramm in `URLClassLoader` umwandeln. Hierfür werden normalerweise Anwendungen und Bibliotheken genutzt, für die zur Laufzeit Klassen in den Klassenpfad eingefügt werden sollen. Die Hierarchie des Klassenladeprogramms hat sich unter Java 11 geändert. Das Systemklassen-Ladeprogramm (auch als Anwendungsklassen-Ladeprogramm bezeichnet) ist jetzt eine interne Klasse. Beim Umwandeln in `URLClassLoader` wird zur Laufzeit eine `ClassCastException` ausgelöst. Java 11 verfügt nicht über eine API zum dynamischen Erweitern des Klassenpfads zur Laufzeit. Hierfür kann aber die Reflexion genutzt werden, auch wenn dann die üblichen Einschränkungen in Bezug auf die Nutzung der internen API gelten. 

Unter Java 11 werden vom Bootklassen-Ladeprogramm nur die Kernmodule geladen. Wenn Sie ein Klassenladeprogramm ohne übergeordnetes Element erstellen, werden ggf. nicht alle Plattformklassen gefunden. In Java 11 müssen Sie in diesen Fällen `ClassLoader.getPlatformClassLoader()` anstelle von `null` als übergeordnetes Klassenladeprogramm übergeben. 

### <a name="locale-data-changes"></a>Änderungen von Gebietsschemadaten

Die Standardquelle für Gebietsschemadaten in Java 11 wurde mit [JEP 252](http://openjdk.java.net/jeps/252) in das „Common Locale Data Repository“ des Unicode Consortium geändert. Dies kann sich auf die lokalisierte Formatierung auswirken. Legen Sie die Systemeigenschaft `java.locale.providers=COMPAT,SPI` fest, um bei Bedarf zurück zum Gebietsschemaverhalten von Java 8 zurückwechseln zu können. 

### <a name="potential-issues"></a>Mögliche Probleme

Hier sind einige häufige Probleme aufgeführt, die ggf. auftreten können. Weitere Details zu diesen Problemen finden Sie unter den entsprechenden Links.

- [Nicht erkannte VM-Optionen](#unrecognized-options)
- [Nicht erkannte Option](#unrecognized-options)
- [VM-Warnung: Option wird ignoriert](#vm-warnings)
- [VM-Warnung: Option &lt;*option*&gt; wurde eingestellt](#vm-warnings)
- [WARNUNG: Unzulässiger Vorgang mit reflektierendem Zugriff](#warning-an-illegal-reflective-access-operation-has-occurred)
- [java.lang.reflect.InaccessibleObjectException](#javalangreflectinaccessibleobjectexception)
- [java.lang.NoClassDefFoundError](#javalangnoclassdeffounderror)
- [„-Xbootclasspath/p“ wird nicht mehr als Option unterstützt](#-xbootclasspathp-is-no-longer-a-supported-option)
- [java.lang.UnsupportedClassVersion-Fehler](#unsupportedclassversionerror)

#### <a name="unrecognized-options"></a>Nicht erkannte Optionen

Wenn eine Befehlszeilenoption entfernt wurde, gibt die Anwendung `Unrecognized option:` oder `Unrecognized VM option` gefolgt vom Namen der betreffenden Option aus. Eine nicht erkannte Option führt dazu, dass die VM beendet wird.
Für Optionen, die als veraltet gekennzeichnet, aber nicht entfernt wurden, wird eine [VM-Warnung](#vm-warnings) angezeigt.

Im Allgemeinen gibt es für entfernte Optionen keinen Ersatz, und die einzige Möglichkeit besteht darin, die Option aus der Befehlszeile zu entfernen. Eine Ausnahme stellen die Optionen für die Protokollierung bei der Garbage Collection dar. Die GC-Protokollierung wurde in Java 9 [erneut implementiert](http://openjdk.java.net/jeps/271), um das [vereinheitlichte Framework für die JVM-Protokollierung](http://openjdk.java.net/jeps/158) zu verwenden. Weitere Informationen finden Sie in der Referenz zu den Java SE 11-Tools unter „Table 2-2 Mapping Legacy Garbage Collection Logging Flags to the Xlog Configuration“ (Tabelle 2-2: Zuordnen von Legacy-GC-Protokollierungsflags zur Xlog-Konfiguration) im Abschnitt zum Thema [Aktivieren der Protokollierung mit dem vereinheitlichten Framework für die JVM-Protokollierung](https://docs.oracle.com/en/java/javase/11/tools/java.html#GUID-BE93ABDC-999C-4CB5-A88B-1994AAAC74D5). 

#### <a name="vm-warnings"></a>VM-Warnungen

Wenn veraltete Optionen verwendet werden, wird eine Warnung angezeigt. Eine Option gilt als veraltet, wenn sie ersetzt wurde oder keinen Nutzen mehr hat. Wie [entfernte Optionen](#unrecognized-options) auch, sollten diese Optionen aus der Befehlszeile entfernt werden.
Die Warnung `VM Warning: Option <option> was deprecated` bedeutet, dass die Option noch unterstützt wird, aber der Support ggf. bald eingestellt wird. Für eine Option, die nicht mehr unterstützt wird, wird die Warnung `VM Warning: Ignoring option` generiert.
Nicht mehr unterstützte Optionen haben keinerlei Auswirkung auf die Runtime.

Die Webseite zum [Explorer für VM-Optionen](https://chriswhocodes.com/hotspot_option_differences.html) enthält eine umfassende Liste mit Optionen, die Java seit dem JDK 7 hinzugefügt bzw. daraus entfernt wurden. 

#### <a name="error-could-not-create-the-java-virtual-machine"></a>Error: Could not create the Java Virtual Machine (Java Virtual Machine konnte nicht erstellt werden)

Diese Fehlermeldung wird ausgegeben, wenn von der JVM eine [nicht erkannte Option](#unrecognized-options) gefunden wird.

#### <a name="warning-an-illegal-reflective-access-operation-has-occurred"></a>WARNUNG: An illegal reflective access operation has occurred (Unzulässiger Vorgang mit reflektierendem Zugriff)

Wenn in Java-Code für den Zugriff auf die interne JDK-API die Reflexion genutzt wird, wird von der Runtime eine Warnung mit dem Hinweis zum unzulässigen reflektierenden Zugriff ausgegeben.

```console
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by my.sample.Main (file:/C:/sample/) to method sun.nio.ch.Util.getTemporaryDirectBuffer(int)
WARNING: Please consider reporting this to the maintainers of com.company.Main
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
```

Dies bedeutet, dass ein Modul das Paket, auf das per Reflexion zugegriffen wird, nicht exportiert hat. Das Paket ist im Modul *gekapselt*, und es wird quasi die interne API genutzt. Zu Beginn der Nutzung von Java 11 kann diese Warnung ignoriert werden.
Für Java 11 Runtime ist der reflektierende Zugriff zulässig, damit der Legacy-Code weiterhin funktioniert.  

Suchen Sie zum Beheben dieser Warnung nach aktualisiertem Code, für den die interne API nicht genutzt wird. Falls sich das Problem mit aktualisiertem Code nicht beheben lässt, können Sie entweder die Befehlszeilenoption `--add-exports` oder `--add-opens` verwenden, um den Zugriff auf das Paket zu ermöglichen.
Mit diesen Optionen ist der Zugriff auf nicht exportierte Typen eines Moduls von einem anderen Modul aus möglich.

Mit der Option [`--add-exports`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-2F61F3A9-0979-46A4-8B49-325BA0EE8B66) kann das Zielmodul auf die *öffentlichen* Typen des benannten Pakets des Quellmoduls zugreifen. Es kommt auch vor, dass im Code `setAccessible(true)` verwendet wird, um auf nicht öffentliche Member und die API zuzugreifen. Dies wird als *tiefe Reflexion* (Deep Reflection) bezeichnet. Verwenden Sie in diesem Fall [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781), um Ihrem Code den Zugriff auf die nicht öffentlichen Member eines Pakets zu gewähren. Falls Sie unsicher sind, ob Sie *--add-exports* oder *--add-opens* verwenden sollen, können Sie mit *--add-exports* beginnen. 

Die Optionen `--add-exports` und `--add-opens` sollten als Problemumgehung und nicht als langfristige Lösung betrachtet werden.
Bei Verwendung dieser Optionen wird die Kapselung des Modulsystems aufgehoben, bei der es darum geht, die Nutzung der internen JDK-API zu verhindern.  Wenn die interne API entfernt oder geändert wird, tritt für die Anwendung ein Fehler auf.  Der reflektierende Zugriff wird in Java 16 verweigert, sofern er nicht mit Befehlszeilenoptionen wie `--add-opens` aktiviert wird.
Legen Sie in der Befehlszeile `--illegal-access=deny` fest, um das zukünftige Verhalten zu imitieren.

Die Warnung im obigen Beispiel wird ausgegeben, weil das Paket `sun.nio.ch` vom `java.base`-Modul nicht exportiert wird. Anders ausgedrückt: In der Datei `module-info.java` des `java.base`-Moduls ist `exports sun.nio.ch;` nicht enthalten. Sie können dies mit `--add-exports=java.base/sun.nio.ch=ALL-UNNAMED` lösen. Klassen, die nicht implizit in einem Modul definiert sind, gehören zum Modul *unnamed* (`ALL-UNNAMED`).

#### <a name="javalangreflectinaccessibleobjectexception"></a>java.lang.reflect.InaccessibleObjectException

Diese Ausnahme gibt an, dass Sie versuchen, `setAccessible(true)` in einem Feld oder einer Methode einer gekapselten Klasse aufzurufen. Unter Umständen erhalten Sie auch eine [Warnung zu einem unzulässigen reflektierenden Zugriff](#warning-an-illegal-reflective-access-operation-has-occurred). Verwenden Sie die Option [`--add-opens`](https://docs.oracle.com/javase/9/migrate/toc.htm#JSMIG-GUID-12F945EB-71D6-46AF-8C3D-D354FD0B1781), um Ihrem Code den Zugriff auf die nicht öffentlichen Member eines Pakets zu gewähren. Die Meldung zur Ausnahme enthält den Hinweis, dass das Paket nicht von dem Modul „geöffnet“ wird, das versucht, *setAccessible* aufzurufen. Wenn es sich um ein „nicht benanntes Modul“ handelt, sollten Sie `UNNAMED-MODULE` als Zielmodul in der Option *--add-opens* verwenden.

```shell
java.lang.reflect.InaccessibleObjectException: Unable to make field private final java.util.ArrayList jdk.internal.loader.URLClassPath.loaders accessible: 
module java.base does not "opens jdk.internal.loader" to unnamed module @6442b0a6

$ java --add-opens=java.base/jdk.internal.loader=UNNAMED-MODULE example.Main
```

#### <a name="javalangnoclassdeffounderror"></a>java.lang.NoClassDefFoundError

Die Ursache von *NoClassDefFoundError* ist wahrscheinlich ein geteiltes Paket oder ein Verweis auf entfernte Module. 

##### <a name="noclassdeffounderror-caused-by-split-packages"></a>NoClassDefFoundError aufgrund von geteilten Paketen

Ein geteiltes Paket liegt vor, wenn ein Paket in mehr als einer Bibliothek gefunden wird. Das Symptom eines Problems aufgrund eines geteilten Pakets ist, dass eine Klasse nicht gefunden wird, von der Sie wissen, dass sie sich im Klassenpfad befindet. 

Dieses Problem tritt nur bei Verwendung des Modulpfads auf. Vom Java-Modulsystem wird das Nachschlagen von Klassen optimiert, indem ein Paket auf ein *benanntes* Modul beschränkt wird. Die Runtime gibt dem Modulpfad beim Nachschlagen von Klassen Vorrang vor dem Klassenpfad. Wenn ein Paket auf den Modul- und den Klassenpfad aufgeteilt ist, wird nur das Modul verwendet, um die Klasse nachzuschlagen. Dies kann zu Fehlern vom Typ `NoClassDefFound` führen. 

Eine einfache Möglichkeit zur Überprüfung auf geteilte Pakete ist das Einbinden Ihres Modul- und Klassenpfads in [jdeps](https://docs.oracle.com/en/java/javase/11/tools/jdeps.html) und das Nutzen Ihrer Anwendungsklassendateien als &lt;Pfad&gt;. Wenn ein geteiltes Paket vorliegt, wird von jdeps eine Warnung ausgegeben: `Warning: split package: <package-name> <module-path> <split-path>`. 

Sie können dieses Problem beheben, indem Sie `--patch-module <module-name>=<path>[,<path>]` verwenden, um das geteilte Paket dem benannten Modul hinzuzufügen. 

##### <a name="noclassdeffounderror-caused-by-using-java-ee-or-corba-modules"></a>NoClassDefFoundError aufgrund der Verwendung von Java EE- oder CORBA-Modulen

Wenn die Anwendung unter Java 8 ausgeführt wird, aber den Fehler `java.lang.NoClassDefFoundError` oder `java.lang.ClassNotFoundException` ausgibt, ist die Wahrscheinlichkeit hoch, dass die Anwendung ein Paket aus einem Java EE- oder CORBA-Modul verwendet. Diese Module wurden in Java 9 als veraltet gekennzeichnet und [in Java 11 entfernt](https://openjdk.java.net/jeps/320). 

Fügen Sie Ihrem Projekt eine Runtimeabhängigkeit hinzu, um das Problem zu beheben.

> [!div class="mx-tdBreakAll"]
> |Entferntes Modul|Betroffenes Paket|Vorgeschlagene Abhängigkeit|
> |-|-|-|
> |Java-API für XML-Webdienste (JAX-WS) |java.xml.ws |[JAX WS RI Runtime](https://mvnrepository.com/artifact/com.sun.xml.ws/jaxws-rt) |
> |Java Architecture for XML Binding (JAXB) |java.xml.bind |[JAXB Runtime](https://mvnrepository.com/artifact/org.glassfish.jaxb/jaxb-runtime)|
> |JavaBeans Activation Framework (JAV) |java.activation |[JavaBeans (TM) Activation Framework](https://mvnrepository.com/artifact/javax.activation/activation) |
> |Allgemeine Anmerkungen |java.xml.ws.annotation |[Javax Annotation API](https://mvnrepository.com/artifact/javax.annotation/javax.annotation-api)|
> |Common Object Request Broker Architecture (CORBA) |java.corba | [GlassFish CORBA ORB](https://mvnrepository.com/artifact/org.glassfish.corba/glassfish-corba-orb) |
> |Java Transaction API (JTA) |java.transaction | [Java Transaction API](https://mvnrepository.com/artifact/javax.transaction/jta)|

#### <a name="-xbootclasspathp-is-no-longer-a-supported-option"></a>„-Xbootclasspath/p“ wird nicht mehr als Option unterstützt

Unterstützung für `-Xbootclasspath/p` ist nicht mehr vorhanden. Verwenden Sie stattdessen `--patch-module`. Die Option *--patch-module* wird in [JEP 261](http://openjdk.java.net/jeps/261). beschrieben. Suchen Sie nach dem Abschnitt „Patching module content“ (Patchen des Modulinhalts). *--patch-module* kann mit *javac* und *java* verwendet werden, um die Klassen in einem Modul außer Kraft zu setzen oder zu erweitern. 

Mit *--patch-module* wird praktisch das Patchmodul in den Modulsystemvorgang zum Nachschlagen von Klassen eingefügt. Das Modulsystem ruft die Klasse zuerst aus dem Patchmodul ab. Dies entspricht dem Voranstellen von „bootclasspath“ in Java 8. 

#### <a name="unsupportedclassversionerror"></a>UnsupportedClassVersionError

Diese Ausnahme bedeutet, dass Sie Code ausführen möchten, der mit einer späteren Version von Java unter einer früheren Version von Java kompiliert wurde. Ein Beispiel hierfür ist die Ausführung unter Java 11 mit einer JAR-Datei, die mit JDK 13 kompiliert wurde. 

| Java-Version | Klassendateiformat-Version |
|-|-|
| 8  | 52 |
| 9  | 53 |
| 10 | 54 |
| 11 | 55 |
| 12 | 56 |
| 13 | 57 |

## <a name="next-steps"></a>Nächste Schritte

Wenn die Anwendung unter Java 11 ausgeführt wird, können Sie erwägen, Bibliotheken aus dem Klassenpfad in den Modulpfad zu verschieben. Suchen Sie nach aktualisierten Versionen der Bibliotheken, von denen Ihre Anwendung abhängig ist. Wählen Sie modulare Bibliotheken aus (falls verfügbar). Nutzen Sie so häufig wie möglich den Modulpfad. Dies gilt auch, wenn Sie nicht planen, Module in Ihrer Anwendung zu verwenden. Die Nutzung des Modulpfads führt zu einer besseren Leistung beim Laden von Klassen, als wenn Sie den Klassenpfad verwenden. 
