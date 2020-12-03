---
title: Verwenden von Spring Data-JDBC mit Azure Database for MySQL
description: Hier erfahren Sie, wie Sie Spring Data-JDBC (Java Database Connectivity) mit einer Azure Database for MySQL-Datenbank verwenden.
documentationcenter: java
ms.date: 10/12/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: fe3cd9ba7da4458fbbf4b9f70cea22e33acec232
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96441923"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-mysql"></a>Verwenden von Spring Data-JDBC mit Azure Database for MySQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc) verwendet, um Informationen in bzw. aus [Azure Database for MySQL](/azure/mysql/) zu speichern und abzurufen.

[JDBC](https://jcp.org/en/jsr/detail?id=221) ist die Standard-Java-API, um eine Verbindung mit herkömmlichen relationalen Datenbanken herzustellen.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-mysql) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren der Anwendung an der Befehlszeile durch Eingeben von:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.4.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for MySQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.datasource.initialization-mode=always
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_MYSQL_PASSWORD` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

> [!WARNING]
> Die Konfigurationseigenschaft `spring.datasource.initialization-mode=always` bedeutet, dass Spring Boot mithilfe der Datei `schema.sql`, die Sie später erstellen, bei jedem Start des Servers automatisch ein Datenbankschema generiert. Dies eignet sich hervorragend für Tests. Denken Sie jedoch daran, dass Ihre Daten bei jedem Neustart gelöscht werden, daher sollte diese Eigenschaft nicht in der Produktion verwendet werden.

> [!NOTE]
> Sie fügen `?serverTimezone=UTC` an die Konfigurationseigenschaft `spring.datasource.url` an, um den JDBC-Treiber anzuweisen, beim Herstellen einer Verbindung mit der Datenbank das Datumsformat UTC (Coordinated Universal Time, koordinierte Weltzeit) zu verwenden. Andernfalls verwendet Ihr Java-Server nicht das gleiche Datumsformat wie die Datenbank, was zu einem Fehler führen würde.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

Spring Boot führt automatisch *src/main/resources/`schema.sql`* aus, um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei mit folgendem Inhalt:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten auf Ihrem MySQL-Server verwendet.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png#lightbox)

Glückwunsch! Sie haben eine Spring Boot-Anwendung erstellt, die JDBC zum Speichern und Abrufen von Daten in bzw. aus Azure Database for MySQL verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data-JDBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](../index.yml) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).