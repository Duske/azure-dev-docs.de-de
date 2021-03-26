---
title: Verwenden von Spring Boot-Starters für Azure Service Bus JMS
description: In diesem Artikel wird die Verwendung des Spring JMS-Starters zum Senden und Empfangen von Nachrichten an und von Azure Service Bus veranschaulicht.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 10/13/2019
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 6c87add44d1573d0df91432c934b3fdc3eba0a17
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118562"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Verwenden von Spring Boot-Starters für Azure Service Bus JMS

[!INCLUDE [spring-boot-20-note.md](includes/spring-boot-20-note.md)]

Azure stellt eine asynchrone Nachrichtenplattform namens [Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) (auch „Service Bus“) bereit, die auf dem Standard [AMQP 1.0](http://www.amqp.org/) („Advanced Message Queueing Protocol 1.0“) basiert. Service Bus kann für alle unterstützten Azure-Plattformen verwendet werden.

Der Spring Boot-Starter für Azure Service Bus JMS bietet Spring-Integration in Service Bus.

In diesem Artikel wird veranschaulicht, wie Spring Boot-Starter für Azure Service Bus JMS zum Senden und Empfangen von Nachrichten von `queues` und `topics` von Service Bus verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen für diesen Artikel erfüllt sein:

1. Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) anwenden oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/) registrieren

1. Sie benötigen ein unterstütztes Java Development Kit (JDK) der Version 8 oder höher. Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.

1. [Apache Maven](http://maven.apache.org/) ab Version 3.2.

1. Wenn Sie bereits eine Service Bus-Warteschlange oder ein -Thema konfiguriert haben, stellen Sie sicher, dass der Service Bus-Namespace die folgenden Voraussetzungen erfüllt:

    1. Er lässt den Zugriff über alle Netzwerke zu.
    1. Er befindet sich im Premium-Tarif (oder höher).
    1. Er verfügt über eine Zugriffsrichtlinie mit Lese-/Schreibberechtigungen für Ihre Warteschlange und Ihr Thema.

1. Wenn Sie noch keine Service Bus-Warteschlange oder -Thema konfiguriert haben, verwenden Sie das Azure-Portal, um [eine Service Bus-Warteschlange zu erstellen](/azure/service-bus-messaging/service-bus-quickstart-portal) oder [ein Service Bus-Thema zu erstellen](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Stellen Sie sicher, dass der Namespace die Anforderungen des vorherigen Schritts erfüllt. Notieren Sie sich auch die Verbindungszeichenfolge im Namespace, da Sie sie für die Test-App dieses Tutorials benötigen.

1. Wenn Sie über keine Spring Boot-Anwendung verfügen, erstellen Sie mit [Spring Initializr](https://start.spring.io/) ein **Maven**-Projekt. Denken Sie daran, dass Sie **Maven-Projekt** auswählen und unter **Abhängigkeiten** die Abhängigkeit **Web** hinzuzufügen müssen.


## <a name="use-the-azure-service-bus-jms-starter"></a>Verwenden des Azure Service Bus JMS-Starters

1. Suchen Sie im übergeordneten Verzeichnis Ihrer App nach der Datei *pom.xml*, z. B.:

    *C:\SpringBoot\servicebus\pom.xml*

    - oder -

    */users/example/home/servicebus/pom.xml*

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor.

1. Fügen Sie den Spring Boot Azure Service Bus JMS-Starter zur Liste `<dependencies>` hinzu:

    ```xml
    <dependency>
        <groupId>com.azure.spring</groupId>
        <artifactId>azure-spring-boot-starter-servicebus-jms</artifactId>
        <version>3.1.0</version>
    </dependency>
    ```


1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Konfigurieren der App für Service Bus 

In diesem Abschnitt erfahren Sie, wie Sie Ihre App so konfigurieren, dass sie entweder eine Service Bus-Warteschlange oder ein -Thema verwendet.

### <a name="use-a-service-bus-queue"></a>Verwenden einer Service Bus-Warteschlange

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

    *C:\SpringBoot\servicebus\application.properties*

    - oder -

    */users/example/home/servicebus/application.properties*

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Fügen Sie den folgenden Code am Ende der Datei *application.properties* ein. Ersetzen Sie die Platzhalterwerte durch die entsprechenden Werte für Ihren Service Bus, und setzen Sie sie nicht in Anführungszeichen.

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    spring.jms.servicebus.pricing-tier=<ServiceBusPricingTier> 
    ```

    **Feldbeschreibungen**

    | Feld                                     | BESCHREIBUNG                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Geben Sie die Verbindungszeichenfolge an, die Sie im Service Bus-Namespace vom Azure-Portal erhalten haben. |
    | `spring.jms.servicebus.idle-timeout`      | Legen Sie das Leerlauftimeout in Millisekunden fest. Für dieses Tutorial wird der Wert 1.800.000 empfohlen.   |
    | `spring.jms.servicebus.pricing-tie`       | Geben Sie den Tarif für Ihre Service Bus-Instanz an. Die unterstützten Werte lauten *premium*, *standard* und *basic*. |

1. Speichern und schließen Sie die Datei *application.properties*.

### <a name="use-service-bus-topic"></a>Verwenden des Service Bus-Themas

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

    *C:\SpringBoot\servicebus\application.properties*

    - oder -

    */users/example/home/servicebus/application.properties*

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Fügen Sie den folgenden Code am Ende der Datei *application.properties* ein. Ersetzen Sie die Platzhalterwerte durch die entsprechenden Werte für Ihren Service Bus, und setzen Sie sie nicht in Anführungszeichen.

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusSubscriptionID>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    spring.jms.servicebus.pricing-tier=<ServiceBusPricingTier> 
    ```

    **Feldbeschreibungen**

    | Feld                                     | BESCHREIBUNG                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Geben Sie die Verbindungszeichenfolge an, die Sie im Service Bus-Namespace vom Azure-Portal erhalten haben.   |
    | `spring.jms.servicebus.topic-client-id`   | Geben Sie die JMS-Client-ID an. Dies ist Ihre Service Bus-Abonnement-ID im Azure-Portal.                | 
    | `spring.jms.servicebus.idle-timeout`      | Legen Sie das Leerlauftimeout in Millisekunden fest. Für dieses Tutorial wird der Wert 1.800.000 empfohlen.     |
    | `spring.jms.servicebus.pricing-tie`       | Geben Sie den Tarif für Ihre Service Bus-Instanz an. Die unterstützten Werte lauten *premium*, *standard* und *basic*. |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementieren grundlegender Service Bus-Funktionalität

In diesem Abschnitt erstellen Sie die notwendigen Java-Klassen, um Nachrichten an Ihre Service Bus-Warteschlange oder -Thema zu senden und vom entsprechenden Warteschlangen- oder Themenabonnement zu empfangen.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

    *C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java*

    - oder -

    */users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java*

1. Öffnen Sie die Java-Datei für die Hauptanwendung in einem Text-Editor.

1. Fügen Sie der Datei den folgenden Code hinzu:

   ```java
    package com.wingtiptoys.servicebus;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusJmsStarterApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusJmsStarterApplication.class, args);
        }
    }
    ```

1. Speichern und schließen Sie die Datei.

### <a name="define-a-test-java-class"></a>Definieren einer Java-Testklasse

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *User.java* im Paketverzeichnis Ihrer App.

1. Definieren Sie eine generische Benutzerklasse, die den Namen des Benutzers speichert und abruft:

    ```java
    package com.wingtiptoys.servicebus;

    import java.io.Serializable;

    // Define a generic User class.
    public class User implements Serializable {

        private static final long serialVersionUID = -295422703255886286L;

        private String name;

        public User() {
        }

        public User(String name) {
            setName(name);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }
    ```

    `Serializable` wird implementiert, um die `send`-Methode in `JmsTemplate` im Spring-Framework zu verwenden. Andernfalls sollte ein benutzerdefiniertes `MessageConverter`-Bean definiert werden, um den Inhalt im Textformat in JSON zu serialisieren. Weitere Informationen über `MessageConverter` finden Sie in der offiziellen Dokumentation des [Spring JMS-Starterprojekts](https://spring.io/guides/gs/messaging-jms/).

1. Speichern und schließen Sie die Datei *User.java*.

### <a name="create-a-new-class-for-the-message-send-controller"></a>Erstellen einer neuen Klasse für den Controller zum Senden von Nachrichten

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *SendController.java* im Paketverzeichnis Ihrer App.

1. Fügen Sie den folgenden Code zur neuen Datei hinzu:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jms.core.JmsTemplate;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SendController {

        private static final String DESTINATION_NAME = "<DestinationName>";

        private static final Logger logger = LoggerFactory.getLogger(SendController.class);

        @Autowired
        private JmsTemplate jmsTemplate;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            logger.info("Sending message");
            jmsTemplate.convertAndSend(DESTINATION_NAME, new User(message));
            return message;
        }
    }
    ```

    > [!NOTE]
    > Ersetzen Sie `<DestinationName>` durch den Namen Ihrer eigenen Warteschlange oder Ihres Themas, der in Ihrem Service Bus-Namespace konfiguriert ist.

1. Speichern und schließen Sie die Datei *SendController.java*.

### <a name="create-a-class-for-the-message-receive-controller"></a>Erstellen einer Klasse für den Controller zum Empfangen von Nachrichten

#### <a name="receive-messages-from-a-service-bus-queue"></a>Empfangen von Nachrichten aus einer Service Bus-Warteschlange

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *QueueReceiveController.java* im Paketverzeichnis Ihrer App.

1. Fügen Sie den folgenden Code zur neuen Datei hinzu:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class QueueReceiveController {

        private static final String QUEUE_NAME = "<ServiceBusQueueName>";

        private final Logger logger = LoggerFactory.getLogger(QueueReceiveController.class);

        @JmsListener(destination = QUEUE_NAME, containerFactory = "jmsListenerContainerFactory")
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

    > [!NOTE]
    > Ersetzen Sie `<ServiceBusQueueName>` durch den Namen Ihrer eigenen Warteschlange, der in Ihrem Service Bus-Namespace konfiguriert ist.

1. Speichern und schließen Sie die Datei *QueueReceiveController.java*.

#### <a name="receive-messages-from-a-service-bus-subscription"></a>Empfangen von Nachrichten aus einem Service Bus-Abonnement

1. Erstellen Sie mithilfe eines Text-Editors eine Java-Datei namens *TopicReceiveController.java* im Paketverzeichnis Ihrer App. 

1. Fügen Sie den folgenden Code zur neuen Datei hinzu. Ersetzen Sie den Platzhalter `<ServiceBusTopicName>` durch den Namen Ihres eigenen Themas, der in Ihrem Service Bus-Namespace konfiguriert ist. Ersetzen Sie den Platzhalter `<ServiceBusSubscriptionName>` durch den Namen Ihres eigenen Abonnements für Ihr Service Bus-Thema.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class TopicReceiveController {

        private static final String TOPIC_NAME = "<ServiceBusTopicName>";

        private static final String SUBSCRIPTION_NAME = "<ServiceBusSubscriptionName>";

        private final Logger logger = LoggerFactory.getLogger(TopicReceiveController.class);

        @JmsListener(destination = TOPIC_NAME, containerFactory = "topicJmsListenerContainerFactory",
                subscription = SUBSCRIPTION_NAME)
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

1. Speichern und schließen Sie die Datei *TopicReceiveController.java*.

## <a name="optional-service-bus-functionality"></a>Optionale Service Bus-Funktionalität

Sie können ein angepasstes `MessageConverter`-Bean verwenden, um die Konvertierung für Java-Objekte und JMS-Nachrichten durchzuführen.

### <a name="set-the-content-type-of-messages"></a>Festlegen des Inhaltstyps von Nachrichten

Im folgenden Codebeispiel wird der Inhaltstyp `BytesMessage` auf `application/json` festgelegt. Weitere Informationen finden Sie unter [Nachrichten, Nutzlasten und Serialisierung](/azure/service-bus-messaging/service-bus-messages-payloads).

```java
package com.wingtiptoys.servicebus;

import com.fasterxml.jackson.databind.ObjectWriter;
import org.apache.qpid.jms.message.JmsBytesMessage;
import org.apache.qpid.jms.provider.amqp.message.AmqpJmsMessageFacade;
import org.apache.qpid.proton.amqp.Symbol;
import org.springframework.jms.support.converter.MappingJackson2MessageConverter;
import org.springframework.jms.support.converter.MessageType;
import org.springframework.stereotype.Component;

import javax.jms.BytesMessage;
import javax.jms.JMSException;
import javax.jms.Session;
import java.io.IOException;

@Component
public class CustomMessageConverter extends MappingJackson2MessageConverter {

    private static final String TYPE_ID_PROPERTY = "_type";
    private static final Symbol CONTENT_TYPE = Symbol.valueOf("application/json");

    public CustomMessageConverter() {
        this.setTargetType(MessageType.BYTES);
        this.setTypeIdPropertyName(TYPE_ID_PROPERTY);
    }

    @Override
    protected BytesMessage mapToBytesMessage(Object object, Session session, ObjectWriter objectWriter)
        throws JMSException, IOException {
        final BytesMessage bytesMessage = super.mapToBytesMessage(object, session, objectWriter);
        JmsBytesMessage jmsBytesMessage = (JmsBytesMessage) bytesMessage;
        AmqpJmsMessageFacade facade = (AmqpJmsMessageFacade) jmsBytesMessage.getFacade();
        facade.setContentType(CONTENT_TYPE);
        return jmsBytesMessage;
    }
}
```

Weitere Informationen zu `MessageConverter` finden Sie im offiziellen [Spring JMS-Leitfaden](https://spring.io/guides/gs/messaging-jms/).

## <a name="build-and-test-your-application"></a>Erstellen und Testen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in den Speicherort Ihrer Datei *pom.xml*, z. B.:

    ```cmd
    cd C:\SpringBoot\servicebus 
    ```

    Oder

    ```bash
    cd /users/example/home/servicebus 
    ```

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus:

    ```shell
    mvn clean spring-boot:run
    ```

1. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit *curl* testen:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    In Ihrem Anwendungsprotokoll sollten „Sending message“ und „hello“ angezeigt werden:

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden der JMS-API mit Service Bus und AMQP 1.0](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)
