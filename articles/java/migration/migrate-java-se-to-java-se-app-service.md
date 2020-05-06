---
title: Migrieren von Java SE-Anwendungen zu Java SE unter Azure App Service
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene Spring Boot-Anwendung oder eine andere eigenständige Webanwendung per Java SE zu Azure App Service migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 9576a3831afb00b10b9bec3531c2dbc1dc60f29f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672186"
---
# <a name="migrate-executable-jar-web-applications-to-java-se-on-azure-app-service"></a>Migrieren von ausführbaren JAR-Webanwendungen zu Java SE unter Azure App Service

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene Spring Boot-Anwendung oder eine andere Webanwendung mit Servereinbettung per Java SE zu Azure App Service migrieren möchten.

## <a name="before-you-start"></a>Vorbereitung

Falls Sie keine Anforderungen der Migrationsvorbereitung erfüllen können, helfen Ihnen die Informationen in den folgenden relevanten Migrationsleitfäden weiter:

* Migrieren ausführbarer JAR-Anwendungen zu Containern in Azure Kubernetes Service (Leitfaden geplant)
* Migrieren ausführbarer JAR-Anwendungen zu Azure Virtual Machines (Leitfaden geplant)

## <a name="pre-migration"></a>Vor der Migration

### <a name="switch-to-a-supported-platform"></a>Wechseln zu einer unterstützten Plattform

App Service verfügt über bestimmte Versionen von Java SE. Migrieren Sie Ihre Anwendung zur Sicherstellung der Kompatibilität zu einer der unterstützten Versionen der aktuellen Umgebung, bevor Sie mit der Ausführung der restlichen Schritte fortfahren. Achten Sie darauf, dass Sie die sich ergebende Konfiguration umfassend testen. Verwenden Sie für diese Tests das neueste stabile Release Ihrer Linux-Distribution.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="inventory-external-resources"></a>Bestand: Externe Ressourcen

Identifizieren Sie externe Ressourcen, z. B. Datenquellen, JMS-Nachrichtenbroker und URLs anderer Dienste. Bei Spring Boot-Anwendungen finden Sie die Konfiguration dieser Ressourcen normalerweise unter *src/main/directory* in einer Datei, die meist den Namen *application.properties* oder *application.yml* hat. Überprüfen Sie außerdem die Umgebungsvariablen der Produktionsbereitstellung auf relevante Konfigurationseinstellungen.

#### <a name="databases"></a>Datenbanken

Identifizieren Sie für jede SQL-Datenbank die Verbindungszeichenfolge.

Für eine Spring Boot-Anwendung werden Verbindungszeichenfolgen normalerweise in Konfigurationsdateien angezeigt.

Hier ist ein Beispiel aus der Datei *application.properties* angegeben:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

Hier ist ein Beispiel aus der Datei *application.yaml* angegeben:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

Weitere Informationen finden Sie in der Spring-Dokumentation unter [JPA-Repositorys](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories) bzw. unter [JDBC-Repositorys](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories).

#### <a name="jms-message-brokers"></a>JMS-Nachrichtenbroker

Ermitteln Sie, welche Broker verwendet werden. Dies sollte möglich sein, indem Sie das Buildmanifest (normalerweise *pom.xml* oder *build.gradle*) auf die relevanten Abhängigkeiten untersuchen.

Eine Spring Boot-Anwendung mit Verwendung von ActiveMQ enthält diese Abhängigkeit üblicherweise in *pom.xml*:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

Bei Spring Boot-Anwendungen, für die proprietäre Broker genutzt werden, sind Abhängigkeiten meist direkt in den JMS-Treiberbibliotheken der Broker enthalten. Hier ist ein Beispiel aus der Datei *build.gradle* angegeben:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```

Nachdem Sie die verwendeten Broker identifiziert haben, können Sie nach den entsprechenden Einstellungen suchen. Diese sind normalerweise in der Datei *application.properties* und *application.yml* für Spring Boot enthalten.

Hier ist ein Beispiel aus der Datei *application.properties* angegeben:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Hier ist ein Beispiel aus der Datei *application.yaml* angegeben:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

#### <a name="all-other-external-resources"></a>Alle anderen externen Ressourcen

Es würde den Rahmen dieses Leitfadens sprengen, jede mögliche externe Abhängigkeit zu dokumentieren. Ihr Team ist für die Sicherstellung verantwortlich, dass nach einer App Service-Migration die Anforderungen aller externen Abhängigkeiten Ihrer Anwendung erfüllt werden können.

### <a name="inventory-secrets"></a>Bestand: Geheimnisse

#### <a name="passwords-and-secure-strings"></a>Kennwörter und sichere Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien sowie alle Umgebungsvariablen in den Produktionsbereitstellungen auf Geheimniszeichenfolgen und Kennwörter. In einer Spring Boot-Anwendung befinden sich diese Zeichenfolgen meist in *application.properties* oder *application.yml*.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [inventory-persistence-usage](includes/inventory-persistence-usage.md)]

### <a name="special-cases"></a>Sonderfälle

Für bestimmte Produktionsszenarien sind unter Umständen zusätzliche Änderungen erforderlich, oder es gelten zusätzliche Einschränkungen. Szenarien dieser Art treten zwar meist nicht sehr häufig auf, aber Sie sollten trotzdem sicherstellen, dass sie für Ihre Anwendung entweder nicht zutreffen oder korrekt behoben werden.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Ermitteln, ob für die Anwendung geplante Aufträge benötigt werden

Geplante Aufträge, z. B. Quartz Scheduler-Aufgaben oder cron-Aufträge, können mit App Service nicht verwendet werden. App Service hindert Sie nicht an der Bereitstellung einer Anwendung, die intern geplante Aufgaben enthält. Wenn Ihre Anwendung aber horizontal hochskaliert wird, wird derselbe geplante Auftrag unter Umständen mehrmals pro geplantem Zeitraum ausgeführt. Diese Situation kann unerwünschte Konsequenzen haben.

Inventarisieren Sie alle geplanten Aufträge innerhalb oder außerhalb des Anwendungsprozesses.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Ermitteln, ob Ihre Anwendung betriebssystemspezifischen Code enthält

Falls Ihre Anwendung Code enthält, in den das Betriebssystem für die Ausführung der Anwendung einbezogen ist, müssen Sie Ihre Anwendung so umgestalten, dass KEINE Abhängigkeit vom zugrunde liegenden Betriebssystem besteht. Beispielsweise müssen alle Vorkommen von `/` oder `\` in Dateisystempfaden ggf. durch [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) oder [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-) ersetzt werden.

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Ermitteln aller externen Prozesse/Daemons, die auf den Produktionsservern ausgeführt werden

Für Prozesse, die außerhalb des Anwendungsservers ausgeführt werden, z. B. die Überwachung von Daemons, muss die Migration zu einem anderen Ort oder die Beseitigung durchgeführt werden.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Ermitteln der Verarbeitung von anderen Anforderungen als HTTP-Anforderungen oder von mehreren Ports

App Service unterstützt nur einen einzelnen HTTP-Endpunkt für einen einzelnen Port. Wenn Ihre Anwendung an mehreren Ports lauscht oder Anforderungen über andere Protokolle als HTTP akzeptiert, sollten Sie Azure App Service nicht verwenden.

## <a name="migration"></a>Migration

### <a name="parameterize-the-configuration"></a>Parametrisieren der Konfiguration

Stellen Sie sicher, dass alle externen Ressourcenkoordinaten (z. B. Datenbank-Verbindungszeichenfolgen) und andere anpassbare Einstellungen aus Umgebungsvariablen ausgelesen werden können. Wenn Sie eine Spring Boot-Anwendung migrieren, sollten alle Konfigurationseinstellungen bereits externalisierbar sein. Weitere Informationen finden Sie in der Spring Boot-Dokumentation unter [Externalisierte Konfiguration](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config).

Hier ist ein Beispiel angegeben, in dem aus der Datei `SERVICEBUS_CONNECTION_STRING`application.properties*auf die Umgebungsvariable* verwiesen wird:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Bereitstellen eines App Service-Plans

Wählen Sie in der [Liste mit den verfügbaren App Service-Plänen](https://azure.microsoft.com/pricing/details/app-service/linux/) die Option aus, bei der die Spezifikationen die Vorgaben der aktuellen Produktionshardware erfüllen bzw. übererfüllen.

> [!NOTE]
> Wenn Sie die Ausführung von Staging- bzw. Canarybereitstellungen planen oder [Bereitstellungsslots](/azure/app-service/deploy-staging-slots) nutzen möchten, muss der App Service-Plan diese zusätzliche Kapazität enthalten. Wir empfehlen Ihnen die Verwendung eines Premium-Plans (oder höher) für Java-Anwendungen.

[Erstellen Sie den App Service-Plan](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Erstellen und Bereitstellen von Web-Apps

Sie müssen in Ihrem App Service-Plan für jede ausführbare JAR-Datei, die Sie ausführen möchten, eine Web-App erstellen (mit Auswahl von „Java SE“ als Laufzeitstapel).

#### <a name="maven-applications"></a>Maven-Anwendungen

Wenn Ihre Anwendung über eine Maven-POM-Datei erstellt wurde, sollten Sie [das Web-App-Plug-In für Maven verwenden](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service), um die Web-App zu erstellen und Ihre Anwendung bereitzustellen.

#### <a name="non-maven-applications"></a>Nicht auf Maven basierende Anwendungen

Falls Sie das Maven-Plug-In nicht verwenden können, müssen Sie die Web-App über andere Mechanismen bereitstellen, z. B.:

* [Azure portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [Azure PowerShell](/powershell/module/az.websites/new-azwebapp)

Verwenden Sie nach der Erstellung der Web-App einen der [verfügbaren Bereitstellungsmechanismen](/azure/app-service/deploy-ftp), um Ihre Anwendung bereitzustellen. Ihre Anwendung sollte nach Möglichkeit in */home/site/wwwroot/app.jar* hochgeladen werden. Wenn Sie Ihre JAR-Datei nicht in „app.jar“ umbenennen möchten, können Sie ein Shellskript mit dem Befehl zum Ausführen Ihrer JAR-Datei hochladen. Fügen Sie anschließend den vollständigen Pfad zu diesem Skript in das Textfeld [Startdatei](/azure/app-service/containers/app-service-linux-faq#built-in-images) im Abschnitt „Konfiguration“ des Portals ein. Das Startskript wird nicht aus dem Verzeichnis ausgeführt, in dem es abgelegt wurde. Verwenden Sie daher immer absolute Pfade, um auf Dateien in Ihrem Startskript zu verweisen (z. B.: `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Migrieren von JVM-Runtimeoptionen

Wenn für Ihre Anwendung bestimmte Runtimeoptionen benötigt werden, sollten Sie [zum Angeben den am besten geeigneten Mechanismus verwenden](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Migrieren von externen Ressourcenkoordinaten und anderen Einstellungen

Führen Sie [diese Schritte zum Migrieren von Verbindungseinstellungen und anderen Einstellungen](/azure/app-service/containers/configure-language-java#spring-boot-1) aus.

> [!NOTE]
> Für alle Spring Boot-Anwendungseinstellungen, die im Abschnitt [Parametrisieren der Konfiguration](#parameterize-the-configuration) mit Variablen parametrisiert wurden, müssen diese Umgebungsvariablen in der Anwendungskonfiguration definiert werden. Alle Spring Boot-Anwendungseinstellungen, die nicht explizit mit Umgebungsvariablen parametrisiert wurden, können hierdurch per Anwendungskonfiguration weiterhin außer Kraft gesetzt werden. Beispiel: 

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![App Service-Anwendungskonfiguration](media/migrate-java-se-to-java-se-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Neustarten und Durchführen des Buildüberprüfungstests

Abschließend müssen Sie Ihre Web-App neu starten, um alle Konfigurationsänderungen anzuwenden. Vergewissern Sie sich nach Abschluss des Neustarts, dass Ihre Anwendung korrekt ausgeführt wird.

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie Ihre Anwendung nun zu Azure App Service migriert haben, sollten Sie überprüfen, ob sie wie erwartet funktioniert. Als Nächstes können Sie sich dann über unsere Empfehlungen informieren, mit denen Sie Ihre Anwendung cloudnativer gestalten können.

### <a name="recommendations"></a>Empfehlungen

* Wenn Sie sich für die Verwendung des Verzeichnisses */home* zum Speichern der Dateien entschieden haben, können Sie erwägen, dafür die [Umstellung auf Azure Storage](/azure/app-service/containers/how-to-serve-content-from-azure-storage) durchzuführen.

* Falls Sie über Konfigurationsdaten im Verzeichnis */home* mit Verbindungszeichenfolgen, SSL-Schlüsseln und anderen Geheimnisinformationen verfügen, können Sie, falls möglich, [Azure Key Vault](/azure/app-service/app-service-key-vault-references) bzw. die [Parametereinfügung mit Anwendungseinstellungen](/azure/app-service/configure-common#configure-app-settings) verwenden.

* Erwägen Sie die [Verwendung von Bereitstellungsslots](/azure/app-service/deploy-staging-slots), um zuverlässige Bereitstellungen ohne jegliche Ausfallzeiten zu erzielen.

* Entwerfen und implementieren Sie eine DevOps-Strategie. Sie können [Bereitstellungen automatisieren und mit Azure Pipelines testen](/azure/devops/pipelines/ecosystems/java-webapp), um die Zuverlässigkeit sicherzustellen, während gleichzeitig die Entwicklungsgeschwindigkeit erhöht wird. Bei Verwendung von Bereitstellungsslots können Sie nicht nur die [Bereitstellung für einen Slot automatisieren](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot), sondern auch den nachfolgenden Slotaustausch.

* Entwerfen und implementieren Sie eine Strategie für Geschäftskontinuität und Notfallwiederherstellung. Bei unternehmenskritischen Anwendungen sollten Sie erwägen, eine [Bereitstellungsarchitektur mit mehreren Regionen](/azure/architecture/reference-architectures/app-service-web-app/multi-region) zu verwenden.
