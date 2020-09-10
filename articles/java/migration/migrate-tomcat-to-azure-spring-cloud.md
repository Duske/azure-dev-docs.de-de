---
title: Migrieren von Tomcat-Anwendungen zu Azure Spring Cloud
description: In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Tomcat-Anwendung zu Azure Spring Cloud migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 6/16/2020
ms.openlocfilehash: 7b8a29c2769b3c4b04a40053d0470bfc6b1a0cca
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275184"
---
# <a name="migrate-a-tomcat-application-to-azure-spring-cloud"></a>Migrieren einer Tomcat-Anwendungen zu Azure Spring Cloud

In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie die Ausführung einer vorhandenen Tomcat-Anwendung zu Azure Spring Cloud migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

### <a name="switch-to-a-supported-platform"></a>Wechseln zu einer unterstützten Plattform

Azure Spring Cloud verfügt über bestimmte Versionen von Java SE. Migrieren Sie Ihre Anwendung zur Sicherstellung der Kompatibilität zu einer der unterstützten Versionen der aktuellen Umgebung, bevor Sie mit der Ausführung der restlichen Schritte fortfahren. Achten Sie darauf, dass Sie die sich ergebende Konfiguration umfassend testen. Verwenden Sie für diese Tests das neueste stabile Release Ihrer Linux-Distribution.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="identify-the-application-builddependency-system"></a>Identifizieren des Anwendungsbuilds/Abhängigkeitssystems

Identifizieren Sie, welche Tools zum Erstellen und Packen der Anwendung verwendet werden, einschließlich des Herunterladens aller Abhängigkeiten.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

### <a name="inventory-secrets"></a>Bestand: Geheimnisse

#### <a name="passwords-and-secure-strings"></a>Kennwörter und sichere Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimniszeichenfolgen und Kennwörter. Überprüfen Sie unbedingt *server.xml* und *context.xml* in *$CATALINA_BASE/conf*. Unter Umständen finden Sie in Ihrer Anwendung in *META-INF/context.xml* auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

### <a name="determine-whether-your-application-contains-os-specific-code"></a>Ermitteln, ob Ihre Anwendung betriebssystemspezifischen Code enthält

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="determine-whether-tomcat-is-connected-to-a-web-server"></a>Ermitteln, ob Tomcat mit einem Webserver verbunden ist

Tomcat kann über einen Tomcat-Connector wie `mod_jk` mit einem statischen Webserver (z. B. Apache) verbunden werden. Überprüfen Sie die Datei `workers.properties` im Verzeichnis `conf`, um zu ermitteln, ob eine solche Verbindung vorhanden ist.

### <a name="special-cases"></a>Spezialfälle

Für bestimmte Produktionsszenarien sind unter Umständen zusätzliche Änderungen erforderlich, oder es gelten zusätzliche Einschränkungen. Szenarien dieser Art sind zwar eher selten, aber Sie sollten trotzdem sicherstellen, dass sie für Ihre Anwendung entweder nicht zutreffen oder korrekt behoben werden.

#### <a name="determine-if-the-application-uses-filters"></a>Ermitteln, ob die Anwendung Filter verwendet

Überprüfen Sie die Datei *web.xml* der Anwendung auf [konfigurierte Filter](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#Expires_Filter/Basic_configuration_sample).

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud](includes/determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud.md)]

#### <a name="determine-whether-non-http-connectors-are-used"></a>Ermitteln, ob Connectors ohne HTTP genutzt werden

Azure Spring Cloud unterstützt nur HTTP-Verbindungen an einem einzelnen, nicht anpassbaren HTTP-Port. Wenn Ihre Anwendung zusätzliche Ports oder zusätzliche Protokolle erfordert, verwenden Sie Azure Spring Cloud nicht.

Suchen Sie in der Datei *server.xml* Ihrer Tomcat-Konfiguration nach `<Connector>`-Elementen, um die von Ihrer Anwendung genutzten HTTP-Connectors zu ermitteln.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Ermitteln, ob die SSL-Sitzungsverfolgung genutzt wird

In Azure Spring Cloud wird die SSL-Sitzung vor dem Erreichen des Anwendungscodes beendet, sodass Sie die [SSL-Sitzungsverfolgung](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL) nicht verwenden können. Stattdessen müssen Sie [Spring Session](https://docs.spring.io/spring-session/docs/current/reference/html5/index.html) wechseln.

#### <a name="determine-whether-tomcat-realms-are-used"></a>Ermitteln, ob Tomcat-Bereiche verwendet werden

In Azure Spring Cloud muss Spring Security anstelle von Tomcat-Bereichen verwendet werden. Überprüfen Sie die Datei *server.xm*, um alle [konfigurierten Bereiche](https://tomcat.apache.org/tomcat-9.0-doc/realm-howto.html#Configuring_a_Realm) zu inventarisieren.

#### <a name="determine-whether-servlet-filters-are-used"></a>Ermitteln, ob Servlet-Filter verwendet werden

Überprüfen Sie die Datei *web.xml* in der Anwendung auf `<filter>`-Elemente. Eine Liste der verfügbaren Filter finden Sie in der [Dokumentation zu Tomcat-Filtern](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html).

## <a name="migration"></a>Migration

### <a name="remove-connection-to-web-server-if-present"></a>Verbindung mit Webserver entfernen, falls vorhanden

Wenn Tomcat mit einem statischen Webserver verbunden ist (in der Regel über `mod_jk` mit Apache), deaktivieren Sie diese Verbindung, sodass Tomcat als eigenständiger Server ausgeführt wird und Webumleitungen vom Standardserver nach Bedarf erstellt werden. Es empfiehlt sich, statische Webinhalte mit Azure Content Delivery Network (CDN) zu Azure Storage zu migrieren. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website)und[Schnellstart: Integrieren eines Azure Storage-Kontos in Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

### <a name="update-to-tomcat-9"></a>Aktualisieren auf Tomcat 9

Wenn Ihre aktuelle Anwendung auf einer älteren Version als Tomcat 9 ausgeführt wird, migrieren Sie zu Tomcat 9, und überprüfen Sie, ob die Anwendung voll funktionsfähig ist. Weitere Informationen finden Sie im [Migrationshandbuch für Tomcat 9](http://tomcat.apache.org/migration-9.html).

### <a name="switch-to-maven-or-gradle"></a>Wechseln zu Maven oder Gradle

Spring Boot und Spring Cloud erfordern Maven oder Gradle für die Build- und Abhängigkeitsverwaltung. Wenn Ihre Anwendung ein anderes System zur Build- oder Abhängigkeitsverwaltung verwendet, wechseln Sie zur aktuellen Version von [Maven](https://maven.apache.org/download.cgi), bevor Sie fortfahren. Gradle wird ebenfalls unterstützt, für die Schritte in diesem Leitfaden verwenden wir jedoch Maven. Wenn Sie sich für die Verwendung von Gradle entscheiden, passen Sie die Anweisungen entsprechend an.

Erstellen Sie eine [POM-Datei](https://maven.apache.org/pom.html) für Ihre Anwendung, und stellen Sie sicher, dass die Anwendung mit vollständiger Funktionalität erstellt und ausgeführt wird, bevor Sie fortfahren.

### <a name="migrate-to-spring-boot"></a>Migration zu Spring Boot

Die folgende Tabelle zeigt eine Zusammenfassung der erforderlichen Migrationen und Codeänderungen für die Migration einer Tomcat-Anwendung zu Spring Boot und anschließend zu Azure Spring Cloud. Wenn ein Element in der Spalte „Legacy“ in der Anwendung verwendet wird, muss es durch das entsprechende Element in der Spalte „Minimum“ oder im Idealfall „Empfohlen“ ersetzt werden.

|Alt | Wo suchen? |Mindestanforderung für Migration |Empfohlene Migration|
|---|---|---|---|
|[JDBC über DataSource](https://docs.oracle.com/javase/tutorial/jdbc/basics/connecting.html)|*server.xml*|[Spring Data-Datenquelle](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-sql) mit [JDBC-Vorlage](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template)|Ziehen Sie gegebenenfalls Spring Data und JPA in Betracht.|
|Servlets |*web.xml*|[Kontextinitialisierung für Servlets](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) aktivieren und mit Anmerkung `@WebServlet` versehen|Umschreiben als [Spring-MVC-Controller (mit `@RestController`](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller))
|Filter |*web.xml*| [Kontextinitialisierung für Servlets](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) aktivieren und [mit Anmerkung `@WebFilter` versehen](https://docs.oracle.com/javaee/7/api/javax/servlet/annotation/WebFilter.html) |Wie Mindestanforderung|
|Java Server Pages (JSPs)|Inhalte von *web.xml* und der WAR-Datei|[JSP-Ansichten für Spring MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view-jsp)|Ansichtsebene separat hosten|
|Java Message Service (JMS)|*server.xml*|Instanziieren der Verbindungsfactory als [Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)|[Spring JMS](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/integration.html#jms-using) verwenden
|Statischer Inhalt (Bilder, JavaScript-Dateien usw.) in der WAR-Datei|Verzeichnis für statischen Inhalt (in der Regel */static*, */public*, oder */resources*)|Inhalt in */src/main/resources* verschieben|Siehe [Empfehlungen für statischen Inhalt vor der Migration](#read-only-static-content).
|Statischer Inhalt (Bilder, JavaScript-Dateien usw.) außerhalb der WAR-Datei|Ein Pfad im lokalen Dateisystem|Inhalt in *src/main/resources* verschieben. Quellcode nach hartcodierten Pfaden suchen und durch [ClassPathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ClassPathResource.html) ersetzen |Siehe [Empfehlungen für statischen Inhalt vor der Migration](#read-only-static-content).

1. Wenn Ihre Anwendung auf Bibliotheken basiert, die über JNDI-Ressourcen (z. B. JDBC-Treiber) eingefügt werden, fügen Sie diese Bibliotheken in ihrer POM-Datei als Abhängigkeiten hinzu. Entfernen Sie die Bibliotheken vom Tomcat-Server (in der Regel aus dem Verzeichnis *tomcat/lib*), und vergewissern Sie sich, dass die Anwendung mit voller Funktionalität ausgeführt wird, bevor Sie fortfahren.

1. Fügen Sie Ihrer POM-Datei das übergeordnete Spring Boot-POM hinzu. Weitere Informationen finden Sie in der Spring Boot-Dokumentation unter [Erstellen des POM](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-pom).

1. Fügen Sie Ihrer POM-Datei den Spring Boot-Tomcat-Starter als Abhängigkeit hinzu:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

    Obwohl es sich hierbei um eine ehemalige Tomcat-Anwendung handelt, fügen Sie `war` nicht als Zielverpackung hinzu.

1. Ersetzen Sie Tomcat-Datenquellen durch Spring-Datenquellen. [Konfigurieren Sie die Spring DataSources](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-configure-a-datasource) für alle Datenbanken, die von der Anwendung verwendet werden. Wenn Code direkte SQL-Abfragen ausführt, ändern Sie ihn so, dass [JdbcTemplate](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template) verwendet wird. In der [Spring Framework-Dokumentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#jdbc) und der [Spring Data-Dokumentation](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) finden Sie Informationen zu weiteren Datenzugriffsfunktionen wie Transaktionsverwaltung und CRUD-Tools.

1. Servlet-Implementierungen innerhalb eines [eingebetteten Servlet-Containers](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container) sind zwar möglich, werden jedoch nicht empfohlen. Ersetzen Sie stattdessen [Servlet-Implementierungen](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html) durch Spring-[REST-Controller](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller). Wenn Ihre Anwendung ein Spring-fremdes MVC-Framework verwendet, ersetzen Sie es durch Spring MVC. Weitere Informationen finden Sie in der [Referenz zu Spring MVC-Controllern mit Anmerkungen](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc-controller).

1. Erstellen Sie alle anderen JNDI-Abhängigkeiten mit [Spring-Beans](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection) neu. Bevorzugen Sie die Verwendung von Spring-idiomatischen Mechanismen, z. B. die Verwendung von [Spring JMS](https://spring.io/guides/gs/messaging-jms/) für Messaging.

1. Ersetzen Sie Tomcat-Bereiche durch [Spring Security](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-filters-review). Erwägen Sie die Verwendung von Azure Active Directory für die Autorisierungsverwaltung über [Spring Boot Starter für Active Directory](/azure/developer/java/spring-framework/spring-boot-starters-for-azure#azure-active-directory).

1. Erstellen Sie in *web.xml* konfigurierte Servlet-Filter mit [Spring-Beans](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-as-spring-bean) oder [Klassenpfadscans](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-using-scanning) neu.

1. Wenn die Anwendung statischen Inhalt wie Bilder oder JavaScript-Dateien enthält oder darauf verweist, müssen diese Dateien im Projektquellcode in *src/main/resources* verschoben werden. Nachdem Sie die Dateien verschoben haben, aktualisieren Sie den Quellcode, um Verweise auf lokale Dateisysteme zu entfernen. Verwenden Sie für den Zugriff auf solche Dateien die `ClassPathResource`-Klasse von Spring.

Testen Sie die Anwendung durch Ausführen von `mvn spring-boot:run`. Stellen Sie sicher, dass die resultierende Anwendung mit voller Funktionalität ausgeführt wird, bevor Sie fortfahren.

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Nach der Migration

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
