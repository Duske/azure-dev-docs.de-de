---
title: Verwendung von Spring Boot Starter mit der SQL-API von Azure Cosmos DB
description: Hier erfahren Sie, wie eine mit dem Spring Boot-Initialisierer erstellte Anwendung mit der SQL-API von Azure Cosmos DB konfiguriert wird.
services: cosmos-db
documentationcenter: java
author: KarlErickson
ms.author: karler
ms.date: 10/13/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 10f85c7d1208ff77f2c85ec14e77ced450d5fcc8
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561302"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Verwendung von Spring Boot Starter mit der SQL-API von Azure Cosmos DB

Azure Cosmos DB ist ein global verteilter Datenbankdienst, der Entwicklern durch eine Vielzahl von Standard-APIs wie der SQL-, MongoDB-, Graph- und Tabellen-API die Arbeit mit Daten ermöglicht. Mit Spring Boot Starter von Microsoft können Entwickler Spring Boot-Anwendungen verwenden, die sich mithilfe der SQL-APIs mühelos in Azure Cosmos DB integrieren lassen.

Dieser Artikel zeigt, wie zuerst über das Azure-Portal eine Azure Cosmos DB-Instanz und anschließend mit **[Spring Initializr]** eine benutzerdefinierte Spring Boot-Anwendung erstellt wird. Anschließend wird veranschaulicht, wie [Azure Spring Boot Starter für Azure Cosmos DB] zu Ihrer benutzerdefinierten Anwendung hinzugefügt wird, um Daten mithilfe von Spring Data und der Cosmos DB-SQL-API in Ihrer Azure Cosmos DB-Instanz zu speichern und daraus abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Erstellen einer Azure Cosmos DB-Instanz mithilfe des Azure-Portals

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und wählen Sie **Ressource erstellen** aus.

1. Wählen Sie **Datenbanken** und anschließend **Azure Cosmos DB** aus.

    ![Auswählen von Azure Cosmos DB im Azure-Portal][AZ02]

1. Geben Sie auf der Seite **Azure Cosmos DB** die folgenden Informationen ein:

    * Wählen Sie das **Abonnement**, das Sie für Ihre Datenbank verwenden möchten.
    * Legen Sie fest, ob eine neue **Ressourcengruppe** für Ihre Datenbank erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
    * Geben Sie einen eindeutigen **Kontonamen** ein, der als URI für Ihre Datenbank verwendet werden soll. Beispiel: *contosoaccount*.
    * Wählen Sie für die API **Core (SQL)** aus.
    * Geben Sie den **Speicherort** für Ihre Datenbank an.

    Wählen Sie nach Angabe dieser Optionen **Überprüfen und erstellen** aus, überprüfen Sie Ihre Angaben, und wählen Sie anschließend **Erstellen** aus.

    ![Auswählen von „Überprüfen und erstellen“ zum Fortsetzen des Vorgangs][AZ03]

1. Wenn Ihre Datenbank erstellt wurde, wird sie auf Ihrem Azure-**Dashboard** und auf den Seiten **Alle Ressourcen** und **Azure Cosmos DB** aufgeführt. Sie können Ihre Datenbank für all diese Orte auswählen, um die Eigenschaftenseite für Ihren Cache zu öffnen.

1. Gehen Sie wie folgt vor, wenn die Eigenschaftenseite für Ihre Datenbank angezeigt wird: Wählen Sie **Schlüssel** aus, und kopieren Sie Ihren URI sowie Ihre Zugriffsschlüssel für die Datenbank. Diese Werte benötigen Sie in Ihrer Spring Boot-Anwendung.

    ![Kopieren des URI und der Zugriffsschlüssel im Abschnitt „Schlüssel“][AZ05]

## <a name="create-a-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer Spring Boot-Anwendung mit Spring Initializr

Führen Sie die folgenden Schritte aus, um ein neues Spring Boot-Anwendungsprojekt mit Azure-Unterstützung zu erstellen. Alternativ können Sie das Beispiel [azure-spring-boot-sample-cosmosdb](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-cosmos) im Repository [azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) verwenden. Anschließend können Sie direkt mit dem [Erstellen und Testen der App](#build-and-test-your-app) fortfahren.

1. Navigieren Sie zu <https://start.spring.io/>.

1. Verwenden Sie die folgenden Optionen:

   * Generieren Sie ein **Maven**-Projekt mit **Java**.
   * Geben Sie Ihre **Spring Boot**-Version an.
   * Geben Sie Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung an.
   * Wählen Sie **11** als Java-Version aus.
   * Fügen Sie **Azure-Support** in den Abhängigkeiten hinzu.

   >[!div class="mx-imgBorder"]
   >![Grundlegende Spring Initializr-Optionen][SI01]

   > [!NOTE]
   > 1. Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für **Gruppe** und **Artefakt** (also beispielsweise *com.example.wintiptoysdata*).
   > 1. Die Spring Boot-Version ist möglicherweise höher als die vom Azure-Support unterstützte Version. Nachdem das Projekt automatisch generiert wurde, können Sie die Spring Boot-Version manuell in die höchste von Azure unterstützte Version ändern. Diese Version finden Sie [hier][azure-spring-boot-version-matrix].

1. Wählen Sie nach Angabe der obigen Optionen **GENERIEREN**  aus.

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
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-boot-starter-cosmos</artifactId>
        <version>3.0.0</version>
    </dependency>
    ```

1. Vergewissern Sie sich, dass das Element *properties* die erforderlichen Versionen von Java und Azure angibt:

    ```xml
    <properties>
        <java.version>11</java.version>
        <azure.version>3.0.0</azure.version>
    </properties>
    ```

1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Konfigurieren der Spring Boot-Anwendung für die Verwendung von Azure Cosmos DB

1. Suchen Sie die Datei *application.properties* im *Ressourcen*-Verzeichnis Ihrer App. Beispiel:

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    Oder

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie die folgenden Zeilen zur Datei hinzu. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Eigenschaften für Ihre Datenbank:

    ```properties
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmos.uri=https://contosoaccount.documents.azure.com:443/
    
    # Specify the access key for your database.
    azure.cosmos.key=replace-your-access-key-here
    
    # Specify the name of your database. 
    azure.cosmos.database=contosoaccount
    azure.cosmos.populateQueryMetrics=true
    ```

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Hinzufügen eines Beispielcodes zur Implementierung von grundlegenden Datenbankfunktionen

In diesem Abschnitt erstellen Sie zwei Java-Klassen zum Speichern von Benutzerdaten und ändern dann die Hauptanwendungsklasse, um eine Instanz der *Benutzerklasse* zu erstellen und in der Datenbank zu speichern.

### <a name="define-a-base-class-for-storing-user-data"></a>Definieren einer Basisklasse zum Speichern von Benutzerdaten

1. Erstellen Sie im selben Verzeichnis wie die Java-Hauptanwendungsdatei eine neue Datei mit dem Namen *User.java*.

1. Öffnen Sie in einem Text-Editor die Datei *User.java*, und fügen Sie die folgenden Zeilen zur Datei hinzu, um eine generische Benutzerklasse zu definieren, die Werte speichert und diese von Ihrer Datenbank abruft:

    ```java
    package com.example.wingtiptoysdata;
    
    import com.azure.spring.data.cosmos.core.mapping.Container;
    import com.azure.spring.data.cosmos.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;
    
    @Container(containerName = "mycollection")
    public class User {
        @Id
        private String id;
        private String firstName;
        @PartitionKey
        private String lastName;
        private String address;
    
        public User() {
    
        }
    
        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
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
    
    import com.azure.spring.data.cosmos.repository.ReactiveCosmosRepository;
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
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;
    
    import java.util.Optional;
    
    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);
    
        @Autowired
        private UserRepository repository;
    
        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }
    
        public void run(String... var1) {
            this.repository.deleteAll().block();
            LOGGER.info("Deleted all data in container.");
    
            final User testUser = new User("testId", "testFirstName", "testLastName", "test address line one");
    
            // Save the User class to Azure Cosmos DB database.
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
    
            firstNameUserFlux.collectList().block();
    
            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");
    
            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");
    
            LOGGER.info("findOne in User collection get result: {}", result.toString());
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
    mvnw clean
    ```

    Dieser Befehl führt die Anwendung automatisch im Rahmen der Testphase aus. Sie können auch Folgendes verwenden:

    ```console
    mvnw spring-boot:run
    ```

    Nach einigen Erstellungs- und Testausgaben wird im Konsolenfenster etwa folgende Meldung angezeigt:

    ```console
    INFO 1365 --- [           main] c.e.w.WingtiptoysdataApplication         : Deleted all data in container.
    
    ... (omitting connection and diagnostics output) ...
    
    INFO 1365 --- [           main] c.e.w.WingtiptoysdataApplication         : findOne in User collection get result: testFirstName testLastName, test address line one
    ```

    Die obigen Ausgabemeldungen geben an, dass die Daten erfolgreich in Cosmos DB gespeichert und dann erneut abgerufen wurden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden möchten, löschen Sie unbedingt die Ressourcengruppe mit der zuvor erstellten Cosmos DB-Instanz. Dies können Sie über das Azure-Portal erledigen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="more-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure Cosmos DB und Java finden Sie in den folgenden Artikeln:

* [Dokumentation für Azure Cosmos DB]

* [Azure Cosmos DB: Erstellen einer Dokumentdatenbank mit Java und dem Azure-Portal][Build a SQL API app with Java]

* [Spring-Daten für SQL-API von Azure Cosmos DB]

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Azure Spring Boot Starter für Azure Cosmos DB]

* [Bereitstellen einer Spring Boot-Anwendung für Linux in Azure App Service](deploy-spring-boot-java-app-on-linux.md)

* [Ausführen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[Dokumentation für Azure Cosmos DB]: /azure/cosmos-db/
[Azure für Java-Entwickler]: ../index.yml
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java
[Spring-Daten für SQL-API von Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Azure Spring Boot Starter für Azure Cosmos DB]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmos
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: https://azure.microsoft.com/services/devops/java/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[azure-spring-boot-version-matrix]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring#azure-spring-boot

<!-- IMG List -->
[AZ02]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png
