---
title: Verwenden von Spring Data-JPA mit Azure Database for PostgreSQL
description: Hier erfahren Sie, wie Sie Spring Data-JPA mit einer Azure Database for PostgreSQL-Datenbank verwenden.
documentationcenter: java
ms.date: 06/19/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 1ee76d1a7c699745ff5516240a10e835eac50858
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018555"
---
# <a name="use-spring-data-jpa-with-azure-database-for-postgresql"></a>Verwenden von Spring Data-JPA mit Azure Database for PostgreSQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data-JPA](https://spring.io/projects/spring-data-jpa) verwendet, um Informationen in [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) zu speichern bzw. daraus abzurufen.

[Die Java-Persistenz-API (JPA)](https://en.wikipedia.org/wiki/Java_Persistence_API) ist die Standard-Java-API für die objektrelationale Zuordnung.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-jpa-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-jpa-postgresql) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren der Anwendung an der Befehlszeile durch Eingeben von:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,postgresql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for PostgreSQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu. Ersetzen Sie die beiden `$AZ_DATABASE_NAME`-Variablen und die `$AZ_POSTGRESQL_PASSWORD`-Variable durch die Werte, die Sie zu Beginn dieses Artikels konfiguriert haben.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_POSTGRESQL_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> Die Konfigurationseigenschaft `spring.jpa.hibernate.ddl-auto=create-drop` bedeutet, dass Spring Boot beim Start der Anwendung automatisch ein Datenbankschema erstellt und versucht, dieses beim Herunterfahren zu löschen. Dieses Vorgehen eignet sich hervorragend für Tests, sollte aber nicht in der Produktion verwendet werden!

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-01.png#lightbox)

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der JPA zum Speichern und Abrufen von Daten auf bzw. aus Ihrem PostgreSQL-Server verwendet.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-02.png#lightbox)

Glückwunsch! Sie haben eine Spring Boot-Anwendung erstellt, die JPA zum Speichern und Abrufen von Daten in bzw. aus Azure Database for PostgreSQL verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data-JPA finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/developer/java/) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).
