---
title: Verwenden von Spring Data R2DBC mit Azure Database for MySQL
description: Hier erfahren Sie, wie Sie Spring Data R2DBC mit einer Azure Database for MySQL-Datenbank verwenden.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: article
ms.author: judubois
ms.openlocfilehash: f5c9f3d0cccad7f6e1a8b5ab199f841925f416cc
ms.sourcegitcommit: a65fa8dbb168bd39e225a293d9ee73d18ece1864
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366750"
---
# <a name="how-to-use-spring-data-r2dbc-with-azure-mysql"></a>Verwenden von Spring Data R2DBC mit Azure MySQL

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) verwendet, um Informationen in einer [Azure Database for MySQL-Instanz](https://docs.microsoft.com/azure/mysql/) mithilfe der R2DBC-Implementierung für MySQL auf [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql) zu speichern und abzurufen.

[R2DBC](https://r2dbc.io/) ermöglicht die Nutzung reaktiver APIs mit herkömmlichen relationalen Datenbanken. R2DBC kann mit Spring WebFlux verwendet werden, um vollständig reaktive Spring Boot-Anwendungen zu erstellen. Dabei werden nicht blockierende APIs genutzt und eine bessere Skalierbarkeit als beim klassischen Ansatz mit einem Thread pro Verbindung erzielt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) oder [Azure CLI](/cli/azure/install-azure-cli). Wir empfehlen Azure Cloud Shell, damit Sie automatisch angemeldet werden und Zugriff auf alle erforderlichen Tools erhalten.
- [Java 8](https://www.azul.com/downloads/zulu/) (enthalten in Azure Cloud Shell)
- [cURL](https://curl.haxx.se) oder ein ähnliches HTTP-Hilfsprogramm zum Testen der Funktionalität

## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Richten Sie zunächst mithilfe der folgenden Befehle einige Umgebungsvariablen ein:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=r2dbc
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Thema verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name Ihrer MySQL Server-Instanz. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können den Standardwert `eastus` verwenden. Es wird jedoch empfohlen, eine Region zu konfigurieren, die in Ihrer Nähe liegt. Die vollständige Liste der verfügbaren Regionen können Sie mit `az account list-locations` abrufen.
- `<YOUR_MYSQL_PASSWORD>`: Das Kennwort Ihres MySQL-Datenbankservers. Das Kennwort muss mindestens acht Zeichen lang sein und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben des englischen Alphabets, Kleinbuchstaben des englischen Alphabets, Ziffern (0–9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Spring Boot-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [http://ipv4.icanhazip.com](http://ipv4.icanhazip.com) navigieren.

Erstellen Sie als Nächstes eine Ressourcengruppe.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Wir verwenden das in [Azure Cloud Shell](https://shell.azure.com/) standardmäßig installierte Dienstprogramm `jq`, um JSON-Daten anzuzeigen und ihre Lesbarkeit zu erhöhen.
> Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-database-for-mysql"></a>Erstellen einer Azure-Datenbank für MySQL

Zunächst wird eine verwaltete MySQL-Serverinstanz erstellt.

> [!NOTE]
> 
> Ausführlichere Informationen zum Erstellen von MySQL-Datenbanken finden Sie unter [Erstellen eines Azure Database for MySQL-Servers im Azure-Portal](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

Führen Sie in Ihrer [Azure Shell](https://shell.azure.com/)-Instanz das folgende Skript aus:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    | jq
```

Mit diesem Befehl wird eine kleine MySQL-Serverinstanz erstellt.

### <a name="configure-a-firewall-rule-for-your-mysql-server-instance"></a>Konfigurieren einer Firewallregel für die MySQL-Serverinstanz

Azure Database for MySQL-Instanzen sind standardmäßig geschützt: Sie verfügen über eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie Folgendes ausführen:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-mysql-database"></a>Konfigurieren einer MySQL-Datenbank

Der zuvor erstellte MySQL-Server ist leer: Er enthält keine Datenbank, die mit der Spring Boot-Anwendung verwendet werden kann. Erstellen Sie eine neue Datenbank mit dem Namen `r2dbc`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name r2dbc \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

## <a name="create-a-reactive-spring-boot-application"></a>Erstellen einer reaktiven Spring Boot-Anwendung

Zum Erstellen einer reaktiven Spring Boot-Anwendung wird [Spring Initializr](https://start.spring.io/) verwendet. Die erstellte Anwendung verwendet Folgendes:

- Spring Boot 2.3.0 M3
- Java 8 (Neuere Versionen wie Java 11 funktionieren ebenfalls.)
- Die folgenden Abhängigkeiten: Spring Reactive Web (auch als „Spring WebFlux“ bezeichnet) und Spring Data R2DBC

### <a name="generate-the-application-using-spring-initializr"></a>Erstellen der Anwendung mit Spring Initializr

Erstellen Sie diese Anwendung mithilfe der Befehlszeile, indem Sie Folgendes eingeben:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-r2dbc-workshop -d bootVersion=2.3.0.M3 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Hinzufügen der reaktiven MySQL-Treiberimplementierung

Öffnen Sie die Datei *pom.xml* des Projekts, um den reaktiven MySQL-Treiber von [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql) hinzuzufügen.

Fügen Sie nach der Abhängigkeit `spring-boot-starter-webflux` den folgenden Codeausschnitt hinzu:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-the-azure-database-for-mysql"></a>Konfigurieren von Spring Boot für die Verwendung von Azure Database for MySQL

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/r2dbc
spring.r2dbc.username=r2dbc@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_USERNAME
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_MYSQL_USERNAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung, die zum ersten Mal ausgeführt wird:

![Ausführen der Anwendung][R2DBC-MYSQL01]

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

Konfigurieren Sie in der `DemoApplication`-Hauptklasse eine neue Spring Bean, mit der das von Ihnen verwendete Datenbankschema erstellt wird:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Diese Spring Bean verwendet eine Datei namens *schema.sql*. Erstellen Sie daher diese Datei im Ordner *src/main/resources*:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Verwenden Sie den folgenden Befehl, um die Anwendung zu beenden und erneut auszuführen. Die Anwendung verwendet nun die Datenbank `r2dbc`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der zu erstellenden Datenbanktabelle:

   ![Erstellen der Datenbanktabelle][R2DBC-MYSQL02]

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der R2DBC zum Speichern und Abrufen von Daten aus der MySQL-Serverinstanz verwendet.

Erstellen Sie nun neben der Klasse `DemoApplication` eine neue `Todo`-Java-Klasse:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Bei dieser Klasse handelt es sich um ein Domänenmodell, das der zuvor erstellten `todo`-Tabelle zugeordnet ist.

Für die Verwaltung dieser Klasse ist ein Repository erforderlich. Definieren Sie eine neue `TodoRepository`-Schnittstelle im gleichen Paket:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

Dieses Repository ist ein reaktives Repository, das von Spring Data R2DBC verwaltet wird.

Stellen Sie als Nächstes die Anwendung fertig, indem Sie einen Controller erstellen, der Daten speichern und abrufen kann. Implementieren Sie eine `TodoController`-Klasse im gleichen Paket, und fügen Sie den folgenden Code hinzu:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Halten Sie schließlich die Anwendung an, und starten Sie sie erneut:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testen der Anwendung

Zum Testen der Anwendung können Sie cURL verwenden.

Erstellen Sie zunächst ein neues todo-Element in der Datenbank:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

Mit diesem Befehl sollte das erstellte Element zurückgegeben werden:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Rufen Sie anschließend die Daten mithilfe einer neuen cURL-Anforderung ab:

```bash
curl http://127.0.0.1:8080
```

Mit dem folgenden Befehl wird die todo-Liste zurückgegeben, einschließlich des von Ihnen erstellten Elements:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

   ![Testen mit cURL][R2DBC-MYSQL03]

Glückwunsch! Sie haben eine vollständig reaktive Spring Boot-Anwendung erstellt, die R2DBC zum Speichern und Abrufen von Daten in bzw. aus Azure Database for MySQL verwendet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zu Spring Data R2DBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/r2dbc/docs/1.0.x/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/java/) und [Arbeiten mit Azure DevOps und Java](/azure/devops/).

<!-- IMG List -->

[R2DBC-MYSQL01]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png
[R2DBC-MYSQL02]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png
[R2DBC-MYSQL03]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png
