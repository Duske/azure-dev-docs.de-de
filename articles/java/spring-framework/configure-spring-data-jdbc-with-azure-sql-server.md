---
title: Verwenden von Spring Data-JDBC mit Azure SQL-Datenbank
description: Erfahren Sie, wie Sie Spring Data-JDBC (Java Database Connectivity) mit einer Azure SQL-Datenbank verwenden.
documentationcenter: java
ms.date: 05/18/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: cd0823246482f37822fab6326628e9aab01ebfbd
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018512"
---
# <a name="use-spring-data-jdbc-with-azure-sql-database"></a>Verwenden von Spring Data-JDBC mit Azure SQL-Datenbank

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data-JDBC](https://spring.io/projects/spring-data-jdbc) verwendet, um Informationen in [Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/) zu speichern bzw. daraus abzurufen.

[JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) ist die Standard-Java-API, um eine Verbindung mit herkömmlichen relationalen Datenbanken herzustellen.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-sql-server) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren Sie die Anwendung durch Ausführen des folgenden Befehls in der Befehlszeile:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,sqlserver -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Konfigurieren von Spring Boot für die Verwendung von Azure SQL-Datenbank

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie den folgenden Text hinzu:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_SQL_SERVER_PASSWORD

spring.datasource.initialization-mode=always
```

Ersetzen Sie die beiden `$AZ_DATABASE_NAME`-Variablen und die `$AZ_SQL_SERVER_PASSWORD`-Variable durch die Werte, die Sie zu Beginn dieses Artikels konfiguriert haben.

> [!WARNING]
> Die Konfigurationseigenschaft `spring.datasource.initialization-mode=always` bedeutet, dass Spring Boot mithilfe der Datei `schema.sql`, die Sie später erstellen, bei jedem Start des Servers automatisch ein Datenbankschema generiert. Dieses Vorgehen eignet sich hervorragend für Tests. Denken Sie jedoch daran, dass Ihre Daten bei jedem Neustart gelöscht werden, daher sollte diese Eigenschaft nicht in der Produktion verwendet werden.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers wie folgt starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-01.png)](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

Spring Boot führt automatisch *src/main/resources/schema.sql* aus, um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei, und fügen Sie den folgenden Inhalt hinzu:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie mit dem folgenden Befehl erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie im nächsten Schritt den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten auf bzw. aus Ihrem Azure SQL-Datenbank-Server verwendet.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-02.png)](media/configure-spring-data-jdbc-with-azure-sql-server/create-sql-server-02.png#lightbox)

Glückwunsch! Sie haben eine Spring Boot-Anwendung erstellt, die JDBC zum Speichern und Abrufen von Daten in bzw. aus Azure SQL-Datenbank verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data-JDBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/developer/java/) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).
