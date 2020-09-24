---
title: Verwenden von Spring Data-JPA mit Azure Database for MySQL
description: Erfahren Sie, wie Sie Spring Data-JPA mit einer Azure Database for MySQL-Datenbank verwenden.
documentationcenter: java
ms.date: 06/16/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 6e4371e4d375c663f61f59707c553ac0e2d6e237
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831226"
---
# <a name="use-spring-data-jpa-with-azure-database-for-mysql"></a>Verwenden von Spring Data-JPA mit Azure Database for MySQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data-JPA](https://spring.io/projects/spring-data-jpa) verwendet, um Informationen in [Azure Database for MySQL](/azure/mysql/) zu speichern bzw. daraus abzurufen.

[Die Java-Persistenz-API (JPA)](https://en.wikipedia.org/wiki/Java_Persistence_API) ist die Standard-Java-API für die objektrelationale Zuordnung.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Beispielanwendung

In diesem Artikel wird eine Beispielanwendung codiert. Wenn Sie schneller vorgehen möchten, ist diese Anwendung unter [https://github.com/Azure-Samples/quickstart-spring-data-jpa-mysql](https://github.com/Azure-Samples/quickstart-spring-data-jpa-mysql) bereits programmiert und verfügbar.

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren der Anwendung an der Befehlszeile durch Eingeben von:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for MySQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu. Ersetzen Sie die beiden `$AZ_DATABASE_NAME`-Variablen und die `$AZ_MYSQL_PASSWORD`-Variable durch die Werte, die Sie zu Beginn dieses Artikels konfiguriert haben.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> Die Konfigurationseigenschaft `spring.jpa.hibernate.ddl-auto=create-drop` bedeutet, dass Spring Boot beim Start der Anwendung automatisch ein Datenbankschema erstellt und versucht, dieses beim Herunterfahren zu löschen. Dieses Vorgehen eignet sich hervorragend für Tests, sollte aber nicht in der Produktion verwendet werden!

> [!NOTE]
> Sie fügen `?serverTimezone=UTC` an die Konfigurationseigenschaft `spring.datasource.url` an, um den JDBC-Treiber anzuweisen, beim Herstellen einer Verbindung mit der Datenbank das Datumsformat UTC (Coordinated Universal Time, koordinierte Weltzeit) zu verwenden. Andernfalls verwendet Ihr Java-Server nicht das gleiche Datumsformat wie die Datenbank, was zu einem Fehler führen würde.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png#lightbox)

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie im nächsten Schritt den Java-Code hinzu, der JPA zum Speichern und Abrufen von Daten auf bzw. von Ihrem MySQL-Server verwendet.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png#lightbox)

Glückwunsch! Sie haben eine Spring Boot-Anwendung erstellt, die JPA zum Speichern und Abrufen von Daten in bzw. aus Azure Database for MySQL verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data-JPA finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](../index.yml) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).