---
title: Erstellen einer Spring Cloud Stream Binder-Anwendung mit Azure Event Hubs
description: Erfahren Sie, wie Sie eine mit Spring Boot Initializer erstellte Java-basierte Spring Cloud Stream Binder-Anwendung mit Azure Event Hubs konfigurieren.
services: event-hubs
documentationcenter: java
ms.date: 02/08/2021
ms.service: event-hubs
ms.tgt_pltfrm: na
ms.topic: article
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d0c87ce32caddc0100b91abd800a18179ba4101e
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822717"
---
# <a name="how-to-create-a-spring-cloud-stream-binder-application-with-azure-event-hubs"></a>Erstellen einer Spring Cloud Stream Binder-Anwendung mit Azure Event Hubs

In diesem Artikel wird beschrieben, wie Sie eine mit Spring Boot Initializer erstellte Java-basierte Spring Cloud Stream Binder-Anwendung mit Azure Event Hubs konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

> [!IMPORTANT]
> Für die Schritte in diesem Artikel wird die Spring Boot-Version 2.2 oder 2.3 benötigt.

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Erstellen eines Azure Event Hubs über das Azure-Portal

Gehen Sie wie folgt vor, um einen Azure Event Hub zu erstellen:

### <a name="create-an-azure-event-hub-namespace"></a>Erstellen eines Azure Event Hub-Namespaces

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie dann nach *Event Hubs*.

1. Klicken Sie auf **Erstellen**.

   >[!div class="mx-imgBorder"]
   >![Erstellen eines Azure Event Hub-Namespaces][IMG01]

1. Geben Sie auf der Seite **Namespace erstellen** die folgenden Informationen ein:

   * Wählen Sie das **Abonnement** aus, das Sie für Ihren Namespace verwenden möchten.
   * Legen Sie fest, ob eine neue **Ressourcengruppe** für Ihren Namespace erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   * Geben Sie einen eindeutigen **Namespacenamen** ein, der als Teil des URI für Ihren Event Hub-Namespace verwendet wird. Beispiel: Wenn Sie *wingtiptoys-space* unter **Namespacename** eingegeben haben, lautet der URI `wingtiptoys-space.servicebus.windows.net`.
   * Geben Sie den **Speicherort** für Ihren Event Hub-Namespace an.
   * Tarif:
   * Sie können auch die **Durchsatzeinheiten** für den Namespace angeben.
   
   >[!div class="mx-imgBorder"]
   >![Angeben der Optionen für den Azure Event Hub-Namespace][IMG02]

1. Wenn Sie die oben aufgeführten Optionen angegeben haben, wählen Sie **Überprüfen und erstellen** aus, überprüfen Sie die Angaben, und wählen Sie dann **Erstellen** aus, um Ihren Namespace zu erstellen.

## <a name="create-an-azure-event-hub-in-your-namespace"></a>Erstellen eines Azure Event Hubs in Ihrem Namespace

Wählen Sie nach der Bereitstellung des Namespace **Zu Ressource wechseln** aus, um die Seite **Event Hubs-Namespace** zu öffnen. Auf dieser Seite können Sie einen Event Hub im Namespace erstellen.

1. Navigieren Sie zu dem im vorherigen Abschnitt erstellten Namespace.

1. Wählen Sie auf der oberen Menüleiste **+ Event Hubs** aus.

1. Benennen Sie den Event Hub.

1. Klicken Sie auf **Erstellen**.

   >[!div class="mx-imgBorder"]
   >![Erstellen des Event Hubs][IMG05]

### <a name="create-an-azure-storage-account-for-your-event-hub-checkpoints"></a>Erstellen eines Azure-Speicherkontos für Ihre Event Hub-Prüfpunkte

Gehen Sie wie folgt vor, um ein Speicherkonto für Event Hub-Prüfpunkte zu erstellen:

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>.

1. Wählen Sie **+Ressource erstellen** > **Speicher** > **Speicherkonto** aus.

1. Geben Sie auf der Seite **Speicherkonto erstellen** folgende Informationen ein:

   * Wählen Sie das **Abonnement** aus, das Sie für Ihr Speicherkonto verwenden möchten.
   * Legen Sie fest, ob eine neue **Ressourcengruppe** für Ihr Speicherkonto erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   * Geben Sie unter **Name** einen eindeutigen Namen für das Speicherkonto ein.
   * Geben Sie den **Speicherort** für das Speicherkonto an.

   >[!div class="mx-imgBorder"]
   >![Angeben der Optionen für das Azure-Speicherkonto][IMG08]

1. Wählen Sie nach Angabe der obigen Optionen **Überprüfen + erstellen** aus, um Ihr Speicherkonto zu erstellen.

1. Überprüfen Sie die Angaben, und wählen Sie **Erstellen** aus.  Die Bereitstellung dauert mehrere Minuten.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer einfachen Spring Boot-Anwendung mit Spring Initializr

Gehen Sie zum Erstellen einer Spring Boot-Anwendung wie folgt vor:

1. Navigieren Sie zu <https://start.spring.io/>.

1. Verwenden Sie die folgenden Optionen:

   * Generieren Sie ein **Maven**-Projekt mit **Java**.
   * Geben Sie für **Spring Boot** Version **2.3** an.
   * Geben Sie Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung an.
   * Wählen Sie **8** als Java-Version aus.
   * Fügen Sie die *Web*-Abhängigkeit hinzu.

   >[!div class="mx-imgBorder"]
   >![Grundlegende Spring Initializr-Optionen][SI01]

   > [!NOTE]
   > Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für die **Gruppe** und das **Artefakt**, beispielsweise *com.contoso.eventhubs.sample*.

1. Wählen Sie nach Angabe der obigen Optionen **GENERIEREN**  aus.

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

1. Nachdem Sie die Dateien auf Ihrem lokalen System extrahiert haben, kann Ihre einfache Spring Boot-Anwendung bearbeitet werden.

## <a name="configure-your-spring-boot-app-to-use-the-azure-event-hub-starter"></a>Konfigurieren der Spring Boot-App zur Verwendung von Azure Event Hub Starter

1. Suchen Sie im Stammverzeichnis Ihrer App nach der Datei *pom.xml*. Beispiel:

   *C:\SpringBoot\eventhubs-sample\pom.xml*

   - oder -

   */users/example/home/eventhubs-sample/pom.xml*

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor, und fügen Sie Spring Cloud Azure Event Hub Stream Binder Starter der Liste von `<dependencies>` hinzu:

   ```xml
   <dependency>
     <groupId>com.azure.spring</groupId>
     <artifactId>azure-spring-cloud-stream-binder-eventhubs</artifactId>
     <version>2.1.0</version>
   </dependency>
   ```

1. Wenn Sie JDK Version 9 oder höher verwenden, fügen Sie die folgenden Abhängigkeiten hinzu:

   ```xml
   <dependency>
       <groupId>javax.xml.bind</groupId>
       <artifactId>jaxb-api</artifactId>
       <version>2.3.1</version>
   </dependency>
   <dependency>
       <groupId>org.glassfish.jaxb</groupId>
       <artifactId>jaxb-runtime</artifactId>
       <version>2.3.1</version>
       <scope>runtime</scope>
   </dependency>
   ```

1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Konfigurieren der Spring Boot-App zur Verwendung Ihres Azure Event Hubs

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.yaml*. Beispiel:

   *C:\SpringBoot\eventhubs-sample\src\main\resources\application.yaml*

   - oder -

   */users/example/home/eventhubs-sample/src/main/resources/application.yaml*

2. Öffnen Sie die Datei *application.yaml* in einem Text-Editor, fügen Sie die folgenden Zeilen hinzu, und ersetzen Sie dann die Beispielwerte durch die entsprechenden Eigenschaften für Ihren Event Hub:

   ```yaml
    spring:
      cloud:
        azure:
          eventhub:
            connection-string: [eventhub-namespace-connection-string]
            checkpoint-storage-account: wingtiptoysstorage
            checkpoint-access-key: [checkpoint-access-key]
            checkpoint-container: wingtiptoyscontainer
            
        stream:
          bindings:
            consume-in-0:
              destination: wingtiptoyshub
              group: $Default
            supply-out-0:
              destination: wingtiptoyshub
   
          eventhub:
            bindings:
              consume-in-0:
                consumer:
                  checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            initial-delay: 0
            fixed-delay: 1000
   ```

   Hierbei gilt:

   |                          Feld                           |                                                                                   BESCHREIBUNG                                                                                    |
   |----------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |               `spring.cloud.azure.eventhub.connection-string`                |                                        Geben Sie die Verbindungszeichenfolge an, die Sie im Event Hub-Namespace vom Azure-Portal erhalten haben.                                   |
   |               `spring.cloud.azure.function.definition`                |                                        Geben Sie an, welches funktionale Bean an die externen Ziele gebunden werden soll, die von den Bindungen verfügbar gemacht werden.                                   |
   |               `spring.cloud.azure.poller.fixed-delay`                |                                        Geben Sie eine feste Verzögerung für den Standard-Poller in Millisekunden an (Standardwert: 1000L).                                   |
   |               `spring.cloud.azure.poller.initial-delay`                |                                       Geben Sie die anfängliche Verzögerung für regelmäßige Trigger an (Standardwert: 0).                                   |
   |               `spring.cloud.stream.bindings.consume-in-0.destination`                 |                            Geben Sie die in diesem Tutorial verwendete Event Hub-Instanz an.                         |
   |               `spring.cloud.stream.bindings.consume-in-0.group`                    |                               Geben Sie die Consumergruppen in der Event Hubs-Instanz an.                                |
   |               `spring.cloud.stream.bindings.supply-out-0.destination`                |                             Geben Sie die Event Hub-Instanz an, die Sie auch in diesem Tutorial verwendet haben.                        |
   | `spring.cloud.stream.eventhub.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Geben Sie `MANUAL`an.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-access-key` |                                                      Geben Sie den Zugriffsschlüssel Ihres Speicherkontos an.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-container` |                                                       Geben Sie den Container Ihres Speicherkontos an.                                                   |
   |               `spring.cloud.stream.eventhub.checkpoint-storage-account` |                                                 Geben Sie das in diesem Tutorial erstellte Speicherkonto an.                                               |

3. Speichern und schließen Sie die Datei *application.yaml*.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>Hinzufügen von Beispielcode zum Implementieren grundlegender Event Hub-Funktionen

In diesem Abschnitt erstellen Sie die Java-Klassen, die erforderlich sind, um Ereignisse an Ihren Event Hub zu senden.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

   *C:\SpringBoot\eventhubs-sample\src\main\java\com\contoso\eventhubs\sample\EventhubSampleApplication.java*

   - oder -

   */users/example/home/eventhubs-sample/src/main/java/com/contoso/eventhubs/sample/EventhubSampleApplication.java*

1. Öffnen Sie die Java-Hauptanwendungsdatei in einem Text-Editor, und fügen Sie die folgenden Zeilen zur Datei hinzu:

   ```java
    package com.contoso.eventhubs.sample;
    
    import com.azure.spring.integration.core.api.reactor.Checkpointer;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.Bean;
    import org.springframework.messaging.Message;
    
    import java.util.function.Consumer;
    
    import static com.azure.spring.integration.core.AzureHeaders.CHECKPOINTER;
    
    @SpringBootApplication
    public class EventhubSampleApplication {
    
        public static final Logger LOGGER = LoggerFactory.getLogger(EventhubSampleApplication.class);
    
        public static void main(String[] args) {
            SpringApplication.run(EventhubSampleApplication.class, args);
        }
    
        @Bean
        public Consumer<Message<String>> consume() {
            return message -> {
                Checkpointer checkpointer = (Checkpointer) message.getHeaders().get(CHECKPOINTER);
                LOGGER.info("New message received: '{}'", message);
                checkpointer.success()
                            .doOnSuccess(success -> LOGGER.info("Message '{}' successfully checkpointed", message))
                            .doOnError(error -> LOGGER.error("Exception: {}", error.getMessage()))
                            .subscribe();
            };
        }
    
    }
   ```

1. Speichern und schließen Sie die Java-Hauptanwendungsdatei.

### <a name="create-a-new-configuration-class"></a>Erstellen einer neuen Konfigurationsklasse

1. Erstellen Sie eine neue Java-Datei mit dem Namen *EventProducerConfiguration.java* im Paketverzeichnis Ihrer App, öffnen Sie die Datei in einem Text-Editor, und fügen Sie folgende Zeilen hinzu:

    ```java
    package com.contoso.eventhubs.sample;
    
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.messaging.Message;
    import reactor.core.publisher.EmitterProcessor;
    import reactor.core.publisher.Flux;
    
    import java.util.function.Supplier;
    
    @Configuration
    public class EventProducerConfiguration {
    
        private static final Logger LOGGER = LoggerFactory.getLogger(EventProducerConfiguration.class);
    
        @Bean
        public EmitterProcessor<Message<String>> emitter() {
            return EmitterProcessor.create();
        }
    
        @Bean
        public Supplier<Flux<Message<String>>> supply(EmitterProcessor<Message<String>> emitter) {
            return () -> Flux.from(emitter)
                             .doOnNext(m -> LOGGER.info("Manually sending message {}", m))
                             .doOnError(t -> LOGGER.error("Error encountered", t));
        }
    }
    ```
1. Speichern und schließen Sie die Datei *EventProducerConfiguration.java*.

### <a name="create-a-new-controller-class"></a>Erstellen einer neuen Controllerklasse

1. Erstellen Sie eine neue Java-Datei mit dem Namen *EventProducerController.java* im Paketverzeichnis Ihrer App, öffnen Sie die Datei in einem Text-Editor, und fügen Sie folgende Zeilen hinzu:

   ```java
   package com.contoso.eventhubs.sample;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.ResponseEntity;
   import org.springframework.messaging.Message;
   import org.springframework.messaging.support.MessageBuilder;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;
   import reactor.core.publisher.EmitterProcessor;
   
   @RestController
   public class EventProducerController {
   
       public static final Logger LOGGER = LoggerFactory.getLogger(EventProducerController.class);
   
       @Autowired
       private EmitterProcessor<Message<String>> emitterProcessor;
   
       @PostMapping("/messages")
       public ResponseEntity<String> sendMessage(@RequestBody String message) {
           LOGGER.info("Going to add message {} to emitter", message);
           emitterProcessor.onNext(MessageBuilder.withPayload(message).build());
           return ResponseEntity.ok("Sent!");
       }
   }
   ```

1. Speichern und schließen Sie die Datei *EventProducerController.java*.

## <a name="build-and-test-your-application"></a>Erstellen und Testen der Anwendung

Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu erstellen und zu testen:

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie zum Ordnerverzeichnis, in dem sich die Datei *pom.xml* befindet. Beispiel:

   ```cmd
    cd C:\SpringBoot\eventhubs-sample
   ```
   Oder

   ```bash
   cd /users/example/home/eventhubs-sample
   ```

1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```bash
   mvn clean package -Dmaven.test.skip=true
   mvn spring-boot:run
   ```

1. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit `curl` testen. Beispiel:

   ```bash
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   In den Protokollen Ihrer Anwendung sollte „hello“ angezeigt werden. Beispiel:

   ```output
   2020-09-11 15:11:12.138  INFO 7616 --- [      elastic-4] c.contoso.eventhubs.sample.EventhubSampleApplication  : New message received: 'hello'
   2020-09-11 15:11:12.406  INFO 7616 --- [ctor-http-nio-1] c.contoso.eventhubs.sample.EventhubSampleApplication  : Message 'hello' successfully checkpointed
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Azure-Unterstützung für Event Hub Stream Binder finden Sie in den folgenden Artikeln:

* [Was ist Azure Event Hubs?](/azure/event-hubs/event-hubs-about)

* [Erstellen eines Event Hubs-Namespaces und eines Event Hubs mithilfe des Azure-Portals](/azure/event-hubs/event-hubs-create)

* [Verwenden von Spring Boot Starter für Apache Kafka mit Azure Event Hubs](configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Azure für Java-Entwickler]: ../index.yml
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-02.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-05.png
[IMG08]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-event-hub-08.png
[SI01]: media/configure-spring-cloud-stream-binder-java-app-azure-event-hub/create-project-01.png
