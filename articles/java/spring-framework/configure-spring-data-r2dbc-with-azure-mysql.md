---
title: Verwenden von Spring Data R2DBC mit Azure Database for MySQL
description: Hier erfahren Sie, wie Sie Spring Data R2DBC mit einer Azure Database for MySQL-Datenbank verwenden.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 159f009725d803341e46513bd699d7e3826d6199
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2020
ms.locfileid: "86378764"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-mysql"></a>Verwenden von Spring Data R2DBC mit Azure Database for MySQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) verwendet, um Informationen in einer [Azure Database for MySQL-Instanz](https://docs.microsoft.com/azure/mysql/) mithilfe der R2DBC-Implementierung für MySQL aus dem [r2dbc-mysql GitHub-Repository](https://github.com/mirromutth/r2dbc-mysql) zu speichern und abzurufen.

[R2DBC](https://r2dbc.io/) ermöglicht die Nutzung reaktiver APIs mit herkömmlichen relationalen Datenbanken. Sie können es zusammen mit Spring WebFlux verwenden, um vollständig reaktive Spring Boot-Anwendungen zu erstellen, die nicht blockierende APIs verwenden. Dies bietet bessere Skalierbarkeit als der klassische „Ein-Thread-pro-Verbindung“-Ansatz.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-mysql](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-mysql) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren der Anwendung an der Befehlszeile durch Eingeben von:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Hinzufügen der reaktiven MySQL-Treiberimplementierung

Öffnen Sie die *pom.xml*-Datei des generierten Projekts, um den reaktiven MySQL-Treiber aus dem [r2dbc-mysql-Repository auf GitHub](https://github.com/mirromutth/r2dbc-mysql) hinzuzufügen.

Fügen Sie nach der Abhängigkeit `spring-boot-starter-webflux` den folgenden Codeausschnitt hinzu:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for MySQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_PASSWORD
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_MYSQL_PASSWORD` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

> [!NOTE]
> Zur Verbesserung der Leistung ist die `spring.r2dbc.url`-Eigenschaft so konfiguriert, dass ein Verbindungspool mit [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool) verwendet wird.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Datenbanktabelle während der Erstellung:

[![Erstellung der Datenbanktabelle](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png#lightbox)

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der R2DBC zum Speichern und Abrufen von Daten aus Ihrem MySQL-Server verwendet.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png#lightbox)

Glückwunsch! Sie haben eine vollständig reaktive Spring Boot-Anwendung erstellt, die R2DBC zum Speichern und Abrufen von Daten in bzw. aus Azure Database for MySQL verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data R2DBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/developer/java/) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).
