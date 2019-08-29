---
title: Verwenden von Spring Boot-Starters für Azure Service Bus JMS
description: In diesem Artikel wird die Verwendung des Spring JMS-Starters zum Senden und Empfangen von Nachrichten an und von Azure Service Bus veranschaulicht.
author: seanli1988
manager: kyliel
ms.author: Sean.Li
ms.date: 08/21/2019
ms.devlang: java
ms.service: azure-java
ms.topic: article
ms.openlocfilehash: f41486c7063a6b0fa26ca4055d5f10e625676e8a
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892374"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Verwenden von Spring Boot-Starters für Azure Service Bus JMS

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

Azure stellt eine asynchrone Nachrichtenplattform namens [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) (auch „Service Bus“) bereit, die auf dem Standard [AMQP 1.0](http://www.amqp.org/) („Advanced Message Queueing Protocol 1.0“) basiert. Service Bus kann für alle unterstützten Azure-Plattformen verwendet werden.

Der Spring Boot-Starter für Azure Service Bus JMS bietet Spring-Integration in Service Bus.

In diesem Artikel wird veranschaulicht, wie Spring Boot-Starter für Azure Service Bus JMS zum Senden und Empfangen von Nachrichten von `queues` und `topics` von Service Bus verwendet wird.

> [!NOTE]
> In diesem Artikel wurde die `SNAPSHOT`-Version des Starters verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen für diesen Artikel erfüllt sein:

1. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.comfree/) registrieren.

1. Sie benötigen ein unterstütztes Java Development Kit (JDK) der Version 8 oder höher. Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.

1. Sie benötigen [Apache Maven](http://maven.apache.org/) Version 3.2 oder höher.

1. Wenn Sie bereits eine Service Bus-Warteschlange oder ein -Thema konfiguriert haben, stellen Sie sicher, dass der Service Bus-Namespace die folgenden Voraussetzungen erfüllt:

    1. Er lässt den Zugriff über alle Netzwerke zu.
    1. Er befindet sich im Premium-Tarif (oder höher).
    1. Er verfügt über eine Zugriffsrichtlinie mit Lese-/Schreibberechtigungen für Ihre Warteschlange und Ihr Thema.

1. Wenn Sie noch keine Service Bus-Warteschlange oder -Thema konfiguriert haben, verwenden Sie das Azure-Portal, um [eine Service Bus-Warteschlange zu erstellen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) oder [ein Service Bus-Thema zu erstellen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Stellen Sie sicher, dass der Namespace die Anforderungen des vorherigen Schritts erfüllt. Notieren Sie sich auch die Verbindungszeichenfolge im Namespace, da Sie sie für die Test-App dieses Tutorials benötigen.

1. Wenn Sie über keine Spring Boot-Anwendung verfügen, [erstellen Sie mit dem Spring-Initialisierer ein **Maven**-Projekt](https://start.spring.io/). Denken Sie daran, dass Sie **Maven-Projekt** auswählen und unter **Abhängigkeiten** die Abhängigkeit **Web** hinzuzufügen müssen.

## <a name="use-the-azure-service-bus-jms-starter"></a>Verwenden des Azure Service Bus JMS-Starters

1. Suchen Sie im übergeordneten Verzeichnis Ihrer App nach der Datei *pom.xml*, z. B.:

    `C:\SpringBoot\servicebus\pom.xml`

    Oder

    `/users/example/home/servicebus/pom.xml`

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor.

1. Fügen Sie den Spring Boot Azure Service Bus JMS-Starter zur Liste `<dependencies>` hinzu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms-spring-boot-starter</artifactId>
        <version>2.1.7-SNAPSHOT</version>
    </dependency>
    ```

    ![Fügen Sie den Abhängigkeitsabschnitt zur Datei „pom.xml“ hinzu.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-dependency-section.png)

1. Fügen Sie die Konfiguration für [Maven-Repositorys](https://maven.apache.org/settings.html#Repositories) hinzu, um die SNAPSHOT-Version zu verwenden:

    ```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>
    ```

    ![Fügen Sie den Repositoryabschnitt zur Datei „pom.xml“ hinzu.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-repository-section.png)

1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Konfigurieren der App für Service Bus 

In diesem Abschnitt erfahren Sie, wie Sie Ihre App so konfigurieren, dass sie entweder eine Service Bus-Warteschlange oder ein -Thema verwendet.

### <a name="use-a-service-bus-queue"></a>Verwenden einer Service Bus-Warteschlange

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

    `C:\SpringBoot\servicebus\application.properties`

    Oder

    `/users/example/home/servicebus/application.properties`

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Fügen Sie den folgenden Code am Ende der Datei *application.properties* ein. Ersetzen Sie die Beispielwerte durch die entsprechenden Werte für Ihre Service Bus-Instanz:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Feldbeschreibungen**

    | Feld                                     | BESCHREIBUNG                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Geben Sie die Verbindungszeichenfolge an, die Sie im Service Bus-Namespace vom Azure-Portal erhalten haben. |
    | `spring.jms.servicebus.idle-timeout`      | Legen Sie das Leerlauftimeout in Millisekunden fest. Für dieses Tutorial wird der Wert 1.800.000 empfohlen.   |

3. Speichern und schließen Sie die Datei *application.properties*.

### <a name="use-service-bus-topic"></a>Verwenden des Service Bus-Themas

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

    `C:\SpringBoot\servicebus\application.properties`

    Oder

    `/users/example/home/servicebus/application.properties`

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Fügen Sie den folgenden Code am Ende der Datei *application.properties* ein. Ersetzen Sie die Beispielwerte durch die entsprechenden Werte für Ihre Service Bus-Instanz:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusTopicClientId>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Feldbeschreibungen**

    | Feld                                     | BESCHREIBUNG                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Geben Sie die Verbindungszeichenfolge an, die Sie im Service Bus-Namespace vom Azure-Portal erhalten haben.   |
    | `spring.jms.servicebus.topic-client-id`   | Geben Sie die JMS-Client-ID an, wenn Sie ein Azure Service Bus-Thema mit einem dauerhaften Abonnement verwenden. |
    | `spring.jms.servicebus.idle-timeout`      | Legen Sie das Leerlauftimeout in Millisekunden fest. Für dieses Tutorial wird der Wert 1.800.000 empfohlen.     |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementieren grundlegender Service Bus-Funktionalität

In diesem Abschnitt erstellen Sie die notwendigen Java-Klassen, um Nachrichten an Ihre Service Bus-Warteschlange oder -Thema zu senden und vom entsprechenden Warteschlangen- oder Themenabonnement zu empfangen.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

    `C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java`

    Oder

    `/users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java`

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

## <a name="build-and-test-your-application"></a>Erstellen und Testen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und ändern Sie das Verzeichnis in den Speicherort Ihrer Datei *pom.xml*, z. B.:

    `cd C:\SpringBoot\servicebus`

    Oder

    `cd cd /users/example/home/servicebus`

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus:

    ```shell
    mvn clean spring-boot:run
    ```

3. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit *curl* testen:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    In Ihrem Anwendungsprotokoll sollten „Sending message“ und „hello“ angezeigt werden:

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](http://ms.portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwenden der JMS-API mit Service Bus und AMQP 1.0](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)