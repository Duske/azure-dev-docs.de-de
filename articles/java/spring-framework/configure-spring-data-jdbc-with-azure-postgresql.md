---
title: Verwenden von Spring Data-JDBC mit Azure Database for PostgreSQL
description: Hier erfahren Sie, wie Sie Spring Data-JDBC mit einer Azure Database for PostgreSQL-Datenbank verwenden.
documentationcenter: java
ms.date: 10/13/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: a27b8122b3758e997cf5d7595cfd246084acf071
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96441929"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-postgresql"></a>Verwenden von Spring Data-JDBC mit Azure Database for PostgreSQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data-JDBC](https://spring.io/projects/spring-data-jdbc) verwendet, um Informationen in einer [Azure Database for PostgreSQL](/azure/postgresql/)-Datenbank zu speichern bzw. daraus abzurufen.

[JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) ist die Standard-Java-API, um eine Verbindung mit herkömmlichen relationalen Datenbanken herzustellen.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren Sie die Anwendung mithilfe des folgenden Befehls in der Befehlszeile:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,postgresql -d baseDir=azure-database-workshop -d bootVersion=2.3.4.RELEASE -d javaVersion=8 | tar -xzvf -
``` 
 
### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for PostgreSQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie den folgenden Text hinzu:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_POSTGRESQL_PASSWORD

spring.datasource.initialization-mode=always
```

Ersetzen Sie die beiden `$AZ_DATABASE_NAME`-Variablen und die `$AZ_POSTGRESQL_PASSWORD`-Variable durch die Werte, die Sie zu Beginn dieses Artikels konfiguriert haben.

> [!WARNING]
> Die Konfigurationseigenschaft `spring.datasource.initialization-mode=always` bedeutet, dass Spring Boot mithilfe der Datei *schema.sql*, die Sie später erstellen, bei jedem Start des Servers automatisch ein Datenbankschema generiert. Dieses Vorgehen eignet sich hervorragend für Tests. Denken Sie jedoch daran, dass Ihre Daten bei jedem Neustart gelöscht werden, daher sollte diese Eigenschaft nicht in der Produktion verwendet werden.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers wie folgt starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

Spring Boot führt die Datei *src/main/resources/schema.sql* automatisch aus, um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei, und fügen Sie den folgenden Inhalt hinzu:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie mit dem folgenden Befehl erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten auf bzw. aus Ihrem PostgreSQL-Server verwendet.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

Glückwunsch! Sie haben eine Spring Boot-Anwendung erstellt, die JDBC zum Speichern und Abrufen von Daten in bzw. aus Azure Database for PostgreSQL verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data-JDBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](../index.yml) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).