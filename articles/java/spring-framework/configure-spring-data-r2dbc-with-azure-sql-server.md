---
title: Verwenden von Spring Data R2DBC mit Azure SQL-Datenbank
description: Hier erfahren Sie, wie Sie Spring Data R2DBC mit einer Azure SQL-Datenbank verwenden.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 64316322d509d588f94185452d5c21fdee6e9ef1
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018648"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Verwenden von Spring Data R2DBC mit Azure SQL-Datenbank

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) verwendet, um Informationen in [Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/) mithilfe der R2DBC-Implementierung für Microsoft SQL Server aus dem [GitHub-Repository „r2dbc-mssql“](https://github.com/r2dbc/r2dbc-mssql) zu speichern und abzurufen.

[R2DBC](https://r2dbc.io/) ermöglicht die Nutzung reaktiver APIs mit herkömmlichen relationalen Datenbanken. Sie können es zusammen mit Spring WebFlux verwenden, um vollständig reaktive Spring Boot-Anwendungen zu erstellen, die nicht blockierende APIs verwenden. Dies bietet bessere Skalierbarkeit als der klassische „Ein-Thread-pro-Verbindung“-Ansatz.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren Sie die Anwendung durch Ausführen des folgenden Befehls in der Befehlszeile:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Hinzufügen der reaktiven Treiberimplementierung für Azure SQL-Datenbank

Öffnen Sie die Datei *pom.xml* des generierten Projekts, um den reaktiven Azure SQL-Datenbank-Treiber aus dem [GitHub-Repository „r2dbc-mssql“](https://github.com/r2dbc/r2dbc-mssql) hinzuzufügen.

Fügen Sie nach der Abhängigkeit `spring-boot-starter-webflux` den folgenden Text hinzu:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Konfigurieren von Spring Boot für die Verwendung von Azure SQL-Datenbank

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie den folgenden Text hinzu:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

Ersetzen Sie die beiden `$AZ_DATABASE_NAME`-Variablen und die `$AZ_SQL_SERVER_PASSWORD`-Variable durch die Werte, die Sie zu Beginn dieses Artikels konfiguriert haben.

> [!NOTE]
> Zur Verbesserung der Leistung ist die `spring.r2dbc.url`-Eigenschaft so konfiguriert, dass ein Verbindungspool mit [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool) verwendet wird.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers wie folgt starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie mit dem folgenden Befehl erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Datenbanktabelle während der Erstellung:

[![Erstellung der Datenbanktabelle](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png#lightbox)

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der R2DBC zum Speichern und Abrufen von Daten auf bzw. aus Ihrem Azure SQL-Datenbank-Server verwendet.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png#lightbox)

Glückwunsch! Sie haben eine vollständig reaktive Spring Boot-Anwendung erstellt, die R2DBC zum Speichern und Abrufen von Daten in bzw. aus Azure SQL-Datenbank verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data R2DBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/developer/java/) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).
