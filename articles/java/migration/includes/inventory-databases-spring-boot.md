---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: 6bf4c54c8f428e3ffce79a7ba9ac49aae028e2fd
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990232"
---
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

Weitere mögliche Konfigurationsszenarien finden Sie in der Dokumentation zu Spring Data:

* [JPA-Repositorys](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories)
* [JDBC-Repositorys](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories)
* [Cassandra-Repositorys](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories)
* [MongoDB-Repositorys](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongodb.repositories)
