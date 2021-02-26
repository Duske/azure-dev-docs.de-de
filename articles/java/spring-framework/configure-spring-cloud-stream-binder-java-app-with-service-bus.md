---
title: Verwenden von Spring Cloud Azure Stream Binder für Azure Service Bus
description: In diesem Artikel wird die Verwendung von Spring Cloud Azure Stream Binder zum Senden und Empfangen von Nachrichten an und von Azure Service Bus veranschaulicht.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 02/04/2021
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 4223927cd99e652e8fb06dd8250c39b191f36a94
ms.sourcegitcommit: bccbab4883e6b6b4926fc194c35ad948b11ccc3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99822703"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Verwenden von Spring Cloud Azure Stream Binder für Azure Service Bus

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

In diesem Artikel wird veranschaulicht, wie Spring Cloud Stream Binder zum Senden und Empfangen von Nachrichten von `queues` und `topics` von Service Bus verwendet wird.

Azure stellt eine asynchrone Nachrichtenplattform namens [Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) (auch „Service Bus“) bereit, die auf dem Standard [AMQP 1.0](http://www.amqp.org/) („Advanced Message Queueing Protocol 1.0“) basiert. Service Bus kann für alle unterstützten Azure-Plattformen verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen für diesen Artikel erfüllt sein:

1. Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) anwenden oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren

1. Sie benötigen ein unterstütztes Java Development Kit (JDK) der Version 8 oder höher. Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.

1. Sie benötigen [Apache Maven](http://maven.apache.org/) Version 3.2 oder höher.

1. Wenn Sie bereits eine Service Bus-Warteschlange oder ein -Thema konfiguriert haben, stellen Sie sicher, dass der Service Bus-Namespace die folgenden Voraussetzungen erfüllt:

    1. Er lässt den Zugriff über alle Netzwerke zu.
    1. Standard (oder höher)
    1. Er verfügt über eine Zugriffsrichtlinie mit Lese-/Schreibberechtigungen für Ihre Warteschlange und Ihr Thema.

1. Wenn Sie noch keine Service Bus-Warteschlange oder -Thema konfiguriert haben, verwenden Sie das Azure-Portal, um [eine Service Bus-Warteschlange zu erstellen](/azure/service-bus-messaging/service-bus-quickstart-portal) oder [ein Service Bus-Thema zu erstellen](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Stellen Sie sicher, dass der Namespace die Anforderungen des vorherigen Schritts erfüllt. Notieren Sie sich auch die Verbindungszeichenfolge im Namespace, da Sie sie für die Test-App dieses Tutorials benötigen.

1. Wenn Sie über keine Spring Boot-Anwendung verfügen, erstellen Sie mit [Spring Initializr](https://start.spring.io/) ein **Maven**-Projekt. Denken Sie daran, dass Sie **Maven-Projekt** auswählen und unter **Abhängigkeiten** die Abhängigkeit **Web** hinzufügen müssen. Wählen Sie unter **Spring Boot** „2.3.8“ und Java-Version **8** aus.


## <a name="use-the-spring-cloud-stream-binder-starter"></a>Verwenden des Spring Cloud Stream Binder-Starters

1. Suchen Sie im übergeordneten Verzeichnis Ihrer App nach der Datei *pom.xml*, z. B.:

    `C:\SpringBoot\servicebus\pom.xml`

    Oder

    `/users/example/home/servicebus/pom.xml`

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor.

1. Fügen Sie den folgenden Codeblock unter **&lt;dependencies>** hinzu, je nachdem, ob Sie eine Service Bus-Warteschlange oder ein -Thema verwenden:

    **Service Bus-Warteschlange**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-queue</artifactId>
        <version>2.1.0</version>
    </dependency>
    ```

    **Service Bus-Topic**

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-cloud-stream-binder-servicebus-topic</artifactId>
        <version>2.1.0</version>
    </dependency>
    ```


1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Konfigurieren der App für Service Bus

Sie können Ihre App entweder basierend auf der Verbindungszeichenfolge oder auf einer Anmeldeinformationsdatei konfigurieren. In diesem Tutorial wird eine Verbindungszeichenfolge verwendet. Weitere Informationen zur Verwendung von Anmeldeinformationsdateien finden Sie im [Codebeispiel für Spring Cloud Azure Stream Binder für Azure Service Bus-Warteschlangen](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-cloud-sample-servicebus-queue-binder
) und im [Codebeispiel für Spring Cloud Azure Stream Binder für Azure Service Bus-Themen](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-cloud-stream-binder-servicebus-topic).

1. Suchen Sie die Datei *application.properties* im *Ressourcen*-Verzeichnis Ihrer App. Beispiel:

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   Oder

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Fügen Sie den entsprechenden Code an das Ende der Datei *application.properties* an, je nachdem, ob Sie eine Service Bus-Warteschlange oder ein -Thema verwenden. Verwenden Sie die Tabelle [Feldbeschreibungen](#fd), um die Beispielwerte durch die entsprechenden Eigenschaften für Ihre Service Bus-Instanz zu ersetzen.

    **Service Bus-Warteschlange**

    ```yaml
    spring:
      cloud:
        azure:
          servicebus:
            connection-string: <ServiceBusNamespaceConnectionString>
        stream:
          bindings:
            consume-in-0:
              destination: examplequeue
            supply-out-0:
              destination: examplequeue
          servicebus:
            queue:
              bindings:
                consume-in-0:
                  consumer:
                    checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            fixed-delay: 1000
            initial-delay: 0
    ```

    **Service Bus-Topic**

    ```yaml
    spring:
      cloud:
        azure:
          servicebus:
            connection-string: <ServiceBusNamespaceConnectionString>
        stream:
          bindings:
            consume-in-0:
              destination: exampletopic
              group: examplesubscription
            supply-out-0:
              destination: exampletopic
          servicebus:
            topic:
              bindings:
                consume-in-0:
                  consumer:
                    checkpoint-mode: MANUAL
          function:
            definition: consume;supply;
          poller:
            fixed-delay: 1000
            initial-delay: 0
    ```

    **<a name="fd">Feldbeschreibungen</a>**

    |                                        Feld                                   |                                                                                   BESCHREIBUNG                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.function.definition`                |                                        Geben Sie an, welches funktionale Bean an die externen Ziele gebunden werden soll, die von den Bindungen verfügbar gemacht werden.                                   |
    |               `spring.cloud.azure.poller.fixed-delay`                |                                        Geben Sie eine feste Verzögerung für den Standard-Poller in Millisekunden an (Standardwert: 1000L).                                   |
    |               `spring.cloud.azure.poller.initial-delay`                |                                       Geben Sie die anfängliche Verzögerung für regelmäßige Trigger an (Standardwert: 0).                                   |
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Geben Sie die Verbindungszeichenfolge an, die Sie im Service Bus-Namespace vom Azure-Portal erhalten haben.                                   |
    |               `spring.cloud.stream.bindings.consume-in-0.destination`                 |                            Geben Sie Service Bus-Warteschlange oder das Service Bus-Thema an, das Sie in diesem Tutorial verwendet haben.                         |
    |                  `spring.cloud.stream.bindings.consume-in-0.group`                    |                                            Wenn Sie ein Service Bus-Thema verwendet haben, geben Sie das Themenabonnement an.                                |
    |               `spring.cloud.stream.bindings.supply-out-0.destination`                |                               Geben Sie denselben Wert an, den Sie für das Eingabeziel verwendet haben.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Geben Sie `MANUAL` an.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.consume-in-0.consumer.checkpoint-mode` |                                                       Geben Sie `MANUAL`an.                                                   |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementieren grundlegender Service Bus-Funktionalität

In diesem Abschnitt erstellen Sie die Java-Klassen, die erforderlich sind, um Nachrichten an Ihre Service Bus-Instanz zu senden.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

    `C:\SpringBoot\servicebus\src\main\java\com\example\ServiceBusBinderApplication.java`

   Oder

   `/users/example/home/servicebus/src/main/java/com/example/ServiceBusBinderApplication.java`

1. Öffnen Sie die Java-Datei für die Hauptanwendung in einem Text-Editor.

1. Fügen Sie der Datei den folgenden Code hinzu:

    ```java
   package com.example;
   
   import com.azure.spring.integration.core.api.Checkpointer;
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.context.annotation.Bean;
   import org.springframework.messaging.Message;
   
   import java.util.function.Consumer;
   
   import static com.azure.spring.integration.core.AzureHeaders.CHECKPOINTER;
   
   @SpringBootApplication
   public class ServiceBusBinderApplication {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceBusBinderApplication.class);
   
       public static void main(String[] args) {
           SpringApplication.run(ServiceBusBinderApplication.class, args);
       }
   
       @Bean
       public Consumer<Message<String>> consume() {
           return message -> {
               Checkpointer checkpointer = (Checkpointer) message.getHeaders().get(CHECKPOINTER);
               LOGGER.info("New message received: '{}'", message);
               checkpointer.success().handle((r, ex) -> {
                   if (ex == null) {
                       LOGGER.info("Message '{}' successfully checkpointed", message);
                   }
                   return null;
               });
           };
       }
   }
    ```

1. Speichern und schließen Sie die Datei.

### <a name="create-a-new-producer-configuration-class"></a>Erstellen einer neuen Producerkonfigurationsklasse

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *ServiceProducerConfiguration.java* im Paketverzeichnis Ihrer App.

1. Fügen Sie den folgenden Code zur neuen Datei hinzu:

    ```java
   package com.example;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.messaging.Message;
   import reactor.core.publisher.EmitterProcessor;
   import reactor.core.publisher.Flux;
   
   import java.util.function.Supplier;
   
   @Configuration
   public class ServiceProducerConfiguration {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceProducerConfiguration.class);
   
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

1. Speichern und schließen Sie die Datei *ServiceProducerConfiguration.java*.

### <a name="create-a-new-controller-class"></a>Erstellen einer neuen Controllerklasse

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *ServiceProducerController.java* im Paketverzeichnis Ihrer App.

1. Fügen Sie die folgenden Codezeilen zur neuen Datei hinzu:

    ```java
   package com.example;
   
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.ResponseEntity;
   import org.springframework.messaging.Message;
   import org.springframework.messaging.support.MessageBuilder;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;
   import reactor.core.publisher.EmitterProcessor;
   
   @RestController
   public class ServiceProducerController {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(ServiceProducerController.class);
   
       @Autowired
       private EmitterProcessor<Message<String>> emitterProcessor;
   
       @PostMapping("/messages")
       public ResponseEntity<String> sendMessage(@RequestParam String message) {
           LOGGER.info("Going to add message {} to emitter", message);
           emitterProcessor.onNext(MessageBuilder.withPayload(message).build());
           return ResponseEntity.ok("Sent!");
       }
   
       @GetMapping("/")
       public String welcome() {
           return "welcome";
       }
   }
    ```

1. Speichern und schließen Sie die Datei *ServiceProducerController.java*.

## <a name="build-and-test-your-application"></a>Erstellen und Testen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung.

1. Ändern Sie das Verzeichnis in den Speicherort Ihrer Datei *pom.xml*, z. B.:

    `cd C:\SpringBoot\servicebus`

    - oder -

    `cd /users/example/home/servicebus`

2. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus:

    ```shell
    mvn clean spring-boot:run
    ```

3. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit *curl* testen:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    In Ihrem Anwendungsprotokoll sollte „hello“ angezeigt werden:

    ```shell
    New message received: 'hello'
    Message 'hello' successfully checkpointed
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/java/azure/spring-framework)