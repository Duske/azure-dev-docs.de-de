---
title: Verwendung von Spring Boot Starter mit der SQL-API von Azure Cosmos DB
description: Hier erfahren Sie, wie eine mit dem Spring Boot-Initialisierer erstellte Anwendung mit der SQL-API von Azure Cosmos DB konfiguriert wird.
services: cosmos-db
documentationcenter: java
author: KarlErickson
ms.author: karler
ms.date: 10/02/2019
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: eb13be08f7fc5eb7b907360571fbc1db1abf0d26
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81669166"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Verwendung von Spring Boot Starter mit der SQL-API von Azure Cosmos DB

Azure Cosmos DB ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs wie der SQL-, MongoDB-, Graph- und Tabellen-API die Arbeit mit Daten ermöglicht. Mit Spring Boot Starter von Microsoft können Entwickler Spring Boot-Anwendungen verwenden, die sich mithilfe der SQL-APIs mühelos in Azure Cosmos DB integrieren lassen.

Dieser Artikel zeigt, wie zuerst über das Azure-Portal eine Azure Cosmos DB-Instanz und anschließend mit **[Spring Initializr]** eine benutzerdefinierte Spring Boot-Anwendung erstellt wird. Anschließend wird veranschaulicht, wie [Spring Boot Cosmos DB Starter für Azure] zu Ihrer benutzerdefinierten Anwendung hinzugefügt wird, um Daten mithilfe von Spring Data und der Cosmos DB-SQL-API in Ihrer Azure Cosmos DB-Instanz zu speichern und daraus abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Erstellen einer Azure Cosmos DB-Instanz mithilfe des Azure-Portals

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und klicken Sie auf **Ressource erstellen**.

1. Klicken Sie auf **Datenbanken** und anschließend auf **Azure Cosmos DB**.

    ![Azure-Portal][AZ02]

1. Geben Sie auf der Seite **Azure Cosmos DB** die folgenden Informationen ein:

    * Wählen Sie das **Abonnement**, das Sie für Ihre Datenbank verwenden möchten.
    * Legen Sie fest, ob eine neue **Ressourcengruppe** für Ihre Datenbank erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
    * Geben Sie einen eindeutigen **Kontonamen** ein, der als URI für Ihre Datenbank verwendet werden soll. Beispiel: *wingtiptoysdata*.
    * Wählen Sie für die API **Core (SQL)** aus.
    * Geben Sie den **Speicherort** für Ihre Datenbank an.

    Klicken Sie nach Angabe dieser Optionen auf **Überprüfen und erstellen**, überprüfen Sie Ihre Angaben, und klicken Sie anschließend auf **Erstellen**.

    ![Azure-Portal][AZ03]

1. Wenn Ihre Datenbank erstellt wurde, wird Sie in Ihrem Azure-**Dashboard** sowie auf den Seiten **Alle Ressourcen** und **Azure Cosmos DB** aufgeführt. Sie können in Ihrer Datenbank auf eine beliebige Stelle klicken, um die Eigenschaftenseite für Ihren Cache zu öffnen.

1. Wenn die Eigenschaftenseite für Ihre Datenbank angezeigt wird, klicken Sie auf **Schlüssel**, und kopieren Sie Ihren URI sowie Ihre Zugriffsschlüssel für Ihre Datenbank. Diese Werte verwenden Sie in Ihrer Spring Boot-Anwendung.

    ![Azure-Portal][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer einfachen Spring Boot-Anwendung mit Spring Initializr

Führen Sie die folgenden Schritte aus, um ein neues Spring Boot-Anwendungsprojekt mit Azure-Unterstützung zu erstellen. Alternativ können Sie das Beispiel [azure-cosmosdb-spring-boot-sample](https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-cosmosdb-spring-boot-sample) im Repository [azure-spring-boot](https://github.com/microsoft/azure-spring-boot) verwenden. Anschließend können Sie direkt mit dem [Erstellen und Testen der App](#build-and-test-your-app) fortfahren.

1. Navigieren Sie zu <https://start.spring.io/>.

1. Geben Sie an, dass Sie ein **Maven-Projekt** mit **Java** generieren möchten, und geben Sie Ihre **Spring Boot**-Version sowie die Namen für **Gruppe** und **Artefakt** für Ihre Anwendung ein. Fügen Sie in den Abhängigkeiten **Azure-Support** hinzu, und klicken Sie anschließend auf die Schaltfläche **Projekt generieren**.

    ![Grundlegende Spring Initializr-Optionen][SI01]

    > [!NOTE]
    >
    > Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für **Gruppe** und **Artefakt**, z.B. *com.example.wintiptoysdata*.

1. Laden Sie das Projekt nach entsprechender Aufforderung in einen Pfad auf dem lokalen Computer herunter, und extrahieren Sie die Dateien.

Ihre einfache Spring Boot-Anwendung ist nun bereit für die Bearbeitung.

## <a name="configure-your-spring-boot-application-to-use-the-azure-spring-boot-starter"></a>Konfigurieren der Spring Boot-Anwendung für die Verwendung von Azure Spring Boot Starter

1. Suchen Sie die Datei *pom.xml* im Verzeichnis Ihrer App. Beispiel:

    `C:\SpringBoot\wingtiptoysdata\pom.xml`

    Oder

    `/users/example/home/wingtiptoysdata/pom.xml`

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor, und fügen Sie Folgendes zum Element `<dependencies>` hinzu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
    </dependency>
    ```

1. Vergewissern Sie sich, dass das Element *properties* die erforderlichen Versionen von Java und Azure angibt:

    ```xml
    <properties>
       <java.version>1.8</java.version>
       <azure.version>2.2.0</azure.version>
    </properties>
    ```

1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Konfigurieren der Spring Boot-Anwendung für die Verwendung von Azure Cosmos DB

1. Suchen Sie die Datei *application.properties* im *Ressourcen*-Verzeichnis Ihrer App. Beispiel:

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    Oder

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie die folgenden Zeilen zur Datei hinzu. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Eigenschaften für Ihre Datenbank:

    ```text
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmosdb.uri=https://wingtiptoys.documents.azure.com:443/

    # Specify the access key for your database.
    azure.cosmosdb.key=57686f6120447564652c20426f6220526f636b73==

    # Specify the name of your database.
    azure.cosmosdb.database=wingtiptoysdata
    ```

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Hinzufügen eines Beispielcodes zur Implementierung von grundlegenden Datenbankfunktionen

In diesem Abschnitt erstellen Sie zwei Java-Klassen zum Speichern von Benutzerdaten und ändern dann die Hauptanwendungsklasse, um eine Instanz der *Benutzerklasse* zu erstellen und in der Datenbank zu speichern.

### <a name="define-a-base-class-for-storing-user-data"></a>Definieren einer Basisklasse zum Speichern von Benutzerdaten

1. Erstellen Sie im selben Verzeichnis wie die Java-Hauptanwendungsdatei eine neue Datei mit dem Namen *User.java*.

1. Öffnen Sie in einem Text-Editor die Datei *User.java*, und fügen Sie die folgenden Zeilen zur Datei hinzu, um eine generische Benutzerklasse zu definieren, die Werte speichert und diese von Ihrer Datenbank abruft:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document;
    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;

    @Document(collection = "mycollection")
    public class User {

        @Id
        private String id;
        private String firstName;

        @PartitionKey
        private String lastName;
        private String address;

        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }

        public User() {
        }

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getFirstName() {
            return firstName;
        }

        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }

        public String getLastName() {
            return lastName;
        }

        public void setLastName(String lastName) {
            this.lastName = lastName;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return String.format("%s %s, %s", firstName, lastName, address);
        }
    }
    ```

1. Speichern und schließen Sie die Datei *User.java*.

### <a name="define-a-data-repository-interface"></a>Definieren einer Datenrepositoryschnittstelle

1. Erstellen Sie im selben Verzeichnis wie die Java-Hauptanwendungsdatei eine neue Datei mit dem Namen *UserRepository.java*.

1. Öffnen Sie in einem Text-Editor die Datei *UserRepository.java*, und fügen Sie die folgenden Zeilen zur Datei hinzu, um zur Erweiterung der standardmäßigen `ReactiveCosmosRepository`-Schnittstelle eine Benutzerrepositoryschnittstelle zu definieren:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.repository.ReactiveCosmosRepository;
    import org.springframework.stereotype.Repository;
    import reactor.core.publisher.Flux;

    @Repository
    public interface UserRepository extends ReactiveCosmosRepository<User, String> {
        Flux<User> findByFirstName(String firstName);
    }
    ```

    Die `ReactiveCosmosRepository`-Schnittstelle ersetzt die `DocumentDbRepository`-Schnittstelle aus der vorherigen Starter-Version. Die neue Schnittstelle stellt synchrone und reaktive APIs für grundlegende Speicher-, Lösch- und Suchvorgänge bereit.

1. Speichern und schließen Sie die Datei *UserRepository.java*.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer Anwendung. Beispiel:

    `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

    Oder

    `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

1. Öffnen Sie die Java-Hauptanwendungsdatei in einem Text-Editor, und fügen Sie die folgenden Zeilen zur Datei hinzu:

    ```java
    package com.example.wingtiptoysdata;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
    import java.util.Optional;

    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {

        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);

        @Autowired
        private UserRepository repository;

        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }

        public void run(String... var1) throws Exception {
            final User testUser = new User("1", "Tasha", "Calderon", "4567 Main St Buffalo, NY 98052");

            LOGGER.info("Saving user: {}", testUser);

            // Save the User class to Azure CosmosDB database.
            final Mono<User> saveUserMono = repository.save(testUser);

            final Flux<User> firstNameUserFlux = repository.findByFirstName("testFirstName");

            //  Nothing happens until we subscribe to these Monos.
            //  findById will not return the user as user is not present.
            final Mono<User> findByIdMono = repository.findById(testUser.getId());
            final User findByIdUser = findByIdMono.block();
            Assert.isNull(findByIdUser, "User must be null");

            final User savedUser = saveUserMono.block();
            Assert.state(savedUser != null, "Saved user must not be null");
            Assert.state(savedUser.getFirstName().equals(testUser.getFirstName()), "Saved user first name doesn't match");

            LOGGER.info("Saved user");

            firstNameUserFlux.collectList().block();

            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");

            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");

            LOGGER.info("Found user by findById : {}", result);
        }

        @PostConstruct
        public void setup() {
            LOGGER.info("Clear the database");
            this.repository.deleteAll().block();
        }

        @PreDestroy
        public void cleanup() {
            LOGGER.info("Cleaning up users");
            this.repository.deleteAll().block();
        }
    }
    ```

1. Speichern und schließen Sie die Java-Hauptanwendungsdatei.

## <a name="build-and-test-your-app"></a>Erstellen und Testen der App

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Ordner, in dem sich die Datei *pom.xml* befindet. Beispiel:

    `cd C:\SpringBoot\wingtiptoysdata`

    Oder

    `cd /users/example/home/wingtiptoysdata`

1. Verwenden Sie den folgenden Befehl, um Ihre Anwendung zu erstellen und auszuführen:

    ```console
    mvnw clean test
    ```

    Dieser Befehl führt die Anwendung automatisch im Rahmen der Testphase aus. Sie können auch Folgendes verwenden:

    ```console
    mvnw clean spring-boot:run
    ```

    Nach einigen Erstellungs- und Testausgaben wird im Konsolenfenster etwa folgende Meldung angezeigt:

    ```console
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::            (v2.2.0.RC1)
    >
    > 2019-10-04 15:19:06.817  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Starting WingtiptoysdataApplicationTests on devmachine03 with PID 30013 (started by <user> in /d/source/repos/wingtiptoysdata)
    > 2019-10-04 15:19:06.818  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : No active profile set, falling back to default profiles: default
    > 2019-10-04 15:19:08.329  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.720  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1369ms. Found 1 repository interfaces.
    > 2019-10-04 15:19:09.734  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.748  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 13ms. Found 0 repository interfaces.

    ... (omitting Cosmos DB connection output) ...

    > 2019-10-04 15:19:46.584  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Started WingtiptoysdataApplicationTests in 40.702 seconds (JVM running for 44.647)
    > 2019-10-04 15:19:46.587  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saving user: Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > 2019-10-04 15:19:47.122  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saved user
    > 2019-10-04 15:19:47.289  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Found user by findById : Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 44.003 s - in com.example.wingtiptoysdata.WingtiptoysdataApplicationTests
    > 2019-10-04 15:19:48.124  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.194  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.200  INFO 30013 --- [extShutdownHook] c.e.w.WingtiptoysdataApplication         : Cleaning up users
    > [INFO]
    > [INFO] Results:
    > [INFO]
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    > [INFO]
    > [INFO]
    > [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ wingtiptoysdata ---
    > [INFO] Building jar: /d/source/repos/wingtiptoysdata/target/wingtiptoysdata-0.0.1-SNAPSHOT.jar
    > [INFO]
    > [INFO] --- spring-boot-maven-plugin:2.2.0.RC1:repackage (repackage) @ wingtiptoysdata ---
    > [INFO] Replacing main artifact with repackaged archive
    > [INFO] ------------------------------------------------------------------------
    > [INFO] BUILD SUCCESS
    > [INFO] ------------------------------------------------------------------------
    > [INFO] Total time:  02:18 min
    > [INFO] Finished at: 2019-10-04T15:20:05-07:00
    > [INFO] ------------------------------------------------------------------------
    ```

    ![Erfolgreiche Ausgabe aus der Anwendung][JV02]

    Die Meldungen `Saved user` und `Found user` geben an, dass die Daten erfolgreich in Cosmos DB gespeichert und dann erneut abgerufen wurden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden möchten, löschen Sie unbedingt die Ressourcengruppe mit der zuvor erstellten Cosmos DB-Instanz. Dies können Sie über das Azure-Portal erledigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure Cosmos DB und Java finden Sie in den folgenden Artikeln:

* [Dokumentation für Azure Cosmos DB]

* [Azure Cosmos DB: Erstellen einer Dokumentdatenbank mit Java und dem Azure-Portal][Build a SQL API app with Java]

* [Spring-Daten für SQL-API von Azure Cosmos DB]

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Spring Boot Cosmos DB Starter für Azure]

* [Bereitstellen von Spring Boot-Anwendungen in Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Ausführen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[Dokumentation für Azure Cosmos DB]: /azure/cosmos-db/
[Azure für Java-Entwickler]: /azure/developer/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring-Daten für SQL-API von Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Boot Cosmos DB Starter für Azure]: https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-starters/azure-cosmosdb-spring-boot-starter
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: https://azure.microsoft.com/services/devops/java/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ01.png
[AZ02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ04]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ04.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png

[JV02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/JV02.png
