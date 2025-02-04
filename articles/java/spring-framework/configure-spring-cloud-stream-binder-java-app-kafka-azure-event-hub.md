---
title: Verwenden von Spring Boot Starter für Apache Kafka mit Azure Event Hubs
description: Erfahren Sie, wie Sie eine mit Spring Boot Initializer erstellte Anwendung zur Verwendung von Apache Kafka mit Azure Event Hubs konfigurieren.
services: event-hubs
documentationcenter: java
ms.date: 10/13/2018
ms.service: event-hubs
ms.topic: article
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 75ca8b04bd935e71b51c8d0c71eb189f89f2a512
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442079"
---
# <a name="how-to-use-the-spring-boot-starter-for-apache-kafka-with-azure-event-hubs"></a>Verwenden von Spring Boot Starter für Apache Kafka mit Azure Event Hubs

In diesem Artikel wird beschrieben, wie Sie eine mit Spring Boot Initializer erstellte Java-basierte Spring Cloud Stream Binder-Anwendung zur Verwendung von [Apache Kafka] mit Azure Event Hubs konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

> [!NOTE]
> * Für die Schritte in diesem Artikel wird mindestens die Spring Boot-Version 2.0 benötigt.

## <a name="create-an-azure-event-hub-using-the-azure-portal"></a>Erstellen eines Azure Event Hubs über das Azure-Portal

### <a name="create-an-azure-event-hub-namespace"></a>Erstellen eines Azure Event Hub-Namespaces

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **Ressource erstellen** > **Marketplace durchsuchen** aus, und suchen Sie nach *Event Hubs*.

1. Klicken Sie auf **Erstellen**.

   ![Erstellen eines Azure Event Hub-Namespaces][IMG01]

1. Geben Sie auf der Seite **Namespace erstellen** die folgenden Informationen ein:

   * Wählen Sie das **Abonnement** aus, das Sie für Ihren Namespace verwenden möchten.
   * Legen Sie fest, ob eine neue **Ressourcengruppe** für Ihren Namespace erstellt werden soll, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   * Geben Sie einen eindeutigen **Namespacenamen** ein, der als Teil des URI für Ihren Event Hub-Namespace verwendet wird. Wenn Sie unter **Name** also beispielsweise *wingtiptoys-space* eingegeben haben, lautet der URI `wingtiptoys-space.servicebus.windows.net`.
   * Geben Sie den **Speicherort** für Ihren Event Hub-Namespace an.
   * Geben Sie den **Tarif** an. Dadurch werden Ihre Verwendungsszenarien eingeschränkt.
   * Sie können auch die **Durchsatzeinheiten** für den Namespace angeben.

   ![Angeben der Optionen für den Azure Event Hub-Namespace][IMG02]

1. Wählen Sie nach Angabe der obigen Optionen die Option **Überprüfen + erstellen** aus.

1. Überprüfen Sie die Angaben, und wählen Sie anschließend **Erstellen** aus, um Ihren Namespace zu erstellen.

### <a name="create-an-azure-event-hub-in-your-namespace"></a>Erstellen eines Azure Event Hubs in Ihrem Namespace

Nachdem Sie Ihren Namespace bereitgestellt haben, können Sie darin einen Event Hub erstellen.

1. Navigieren Sie zum im vorherigen Schritt erstellten Namespace.

1. Wählen Sie auf der oberen Menüleiste **Event Hub** aus.

1. Benennen Sie den Event Hub.

1. Klicken Sie auf **Erstellen**.

   ![Erstellen des Event Hubs][IMG05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer einfachen Spring Boot-Anwendung mit Spring Initializr

1. Navigieren Sie zu <https://start.spring.io/>.

1. Verwenden Sie die folgenden Optionen:

   * Generieren Sie ein **Maven**-Projekt mit **Java**.
   * Geben Sie eine **Spring Boot**-Version ab 2.0 an.
   * Geben Sie Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung an.
   * Fügen Sie die **Web**-Abhängigkeit hinzu.

      ![Grundlegende Spring Initializr-Optionen][SI01]

   > [!NOTE]
   > 1. Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für die **Gruppe** und das **Artefakt**, beispielsweise *com.wingtiptoys.kafka*.

1. Nachdem Sie die oben genannten Optionen angegeben haben, klicken Sie auf **Generate Project** (Projekt generieren).

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

1. Nachdem Sie die Dateien auf Ihrem lokalen System extrahiert haben, kann Ihre einfache Spring Boot-Anwendung bearbeitet werden.

## <a name="configure-your-spring-boot-app-to-use-the-spring-cloud-kafka-stream-and-azure-event-hub-starters"></a>Konfigurieren der Spring Boot-App zur Verwendung von Spring Cloud Kafka Stream und Azure Event Hub Starter

1. Suchen Sie im Stammverzeichnis Ihrer App nach der Datei *pom.xml*. Beispiel:

   *C:\SpringBoot\kafka\pom.xml*

   - oder -

   */users/example/home/kafka/pom.xml*

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor, und fügen Sie der Abhängigkeitenliste (`<dependencies>`) die Event Hubs-Kafka-Starter hinzu:

   ```xml
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spring-cloud-starter-azure-eventhubs-kafka</artifactId>
     <version>1.2.8</version>
   </dependency>
   ```

1. Speichern und schließen Sie die Datei *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Erstellen einer Azure-Anmeldeinformationsdatei

1. Öffnen Sie eine Eingabeaufforderung.

1. Navigieren Sie zum Verzeichnis *resources* Ihrer Spring Boot-App. Beispiel:

   ```cmd
   cd C:\SpringBoot\kafka\src\main\resources
   ```

   Oder

   ```bash
   cd /users/example/home/kafka/src/main/resources
   ```

1. Melden Sie sich bei Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

1. Listen Sie Ihre Abonnements auf:

   ```azurecli
   az account list
   ```
   Azure gibt eine Liste mit Ihren Abonnements zurück. Kopieren Sie die GUID für das Abonnement, das Sie verwenden möchten. Beispiel:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```
   
1. Geben Sie die GUID für das Abonnement an, das Sie mit Azure verwenden möchten. Beispiel:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Erstellen Sie die Azure-Anmeldeinformationsdatei:

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   Dieser Befehl erstellt im Verzeichnis *resources* eine Datei *my.azureauth*, deren Inhalt in etwa wie folgt aussieht:

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-event-hub"></a>Konfigurieren der Spring Boot-App zur Verwendung Ihres Azure Event Hubs

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

   *C:\SpringBoot\kafka\src\main\resources\application.properties*

   - oder -

   */users/example/home/kafka/src/main/resources/application.properties*

2. Öffnen Sie die Datei *application.properties* in einem Text-Editor, fügen Sie die folgenden Zeilen hinzu, und ersetzen Sie dann die Beispielwerte durch die entsprechenden Eigenschaften für Ihren Event Hub:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.eventhub.namespace=wingtiptoys

   spring.cloud.stream.bindings.input.destination=wingtiptoyshub
   spring.cloud.stream.bindings.input.group=$Default
   spring.cloud.stream.bindings.output.destination=wingtiptoyshub
   ```
   Hierbei gilt:

   |                       Feld                       |                                                                                   BESCHREIBUNG                                                                                    |
   |---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `spring.cloud.azure.credential-file-path`     |                                                    Gibt die Azure-Anmeldeinformationsdatei an, die Sie zuvor in diesem Tutorial erstellt haben.                                                    |
   |        `spring.cloud.azure.resource-group`        |                                                      Gibt die Azure-Ressourcengruppe an, die Ihren Azure Event Hub enthält.                                                      |
   |            `spring.cloud.azure.region`            |                                           Gibt die geografische Region an, die Sie beim Erstellen Ihres Azure Event Hubs angegeben haben.                                            |
   |      `spring.cloud.azure.eventhub.namespace`      |                                          Gibt den eindeutigen Namen an, den Sie beim Erstellen Ihres Azure Event Hub-Namespaces angegeben haben.                                           |
   | `spring.cloud.stream.bindings.input.destination`  |                            Gibt den Azure Event Hub an, der das Eingabeziel ist (in diesem Fall ist dies der Hub, den Sie zuvor in diesem Tutorial erstellt haben).                            |
   |    `spring.cloud.stream.bindings.input.group `    | Gibt eine Consumergruppe von Azure Event Hub an, die auf „$Default“ festgelegt werden kann, um die grundlegende Consumergruppe zu verwenden, die bei der Erstellung Ihres Azure Event Hubs erstellt wurde. |
   | `spring.cloud.stream.bindings.output.destination` |                               Gibt den Azure Event Hub an, der das Ausgabeziel ist (in diesem Tutorial ist das Ausgabeziel mit dem Eingabeziel identisch).                               |


3. Speichern und schließen Sie die Datei *application.properties*.

## <a name="add-sample-code-to-implement-basic-event-hub-functionality"></a>Hinzufügen von Beispielcode zum Implementieren grundlegender Event Hub-Funktionen

In diesem Abschnitt erstellen Sie die Java-Klassen, die erforderlich sind, um Ereignisse an Ihren Event Hub zu senden.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

   *C:\SpringBoot\kafka\src\main\java\com\wingtiptoys\kafka\EventhubApplication.java*
   
   - oder -

   */users/example/home/kafka/src/main/java/com/wingtiptoys/kafka/EventhubApplication.java*

1. Öffnen Sie die Java-Hauptanwendungsdatei in einem Text-Editor, und fügen Sie die folgenden Zeilen zur Datei hinzu:

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class EventhubApplication {
      public static void main(String[] args) {
         SpringApplication.run(EventhubApplication.class, args);
      }
   }
   ```

1. Speichern und schließen Sie die Java-Hauptanwendungsdatei.


### <a name="create-a-new-class-for-the-source-connector"></a>Erstellen einer neuen Klasse für den Quellconnector

1. Erstellen Sie eine neue Java-Datei mit dem Namen *KafkaSource.java* im Paketverzeichnis Ihrer App, öffnen Sie die Datei in einem Text-Editor, und fügen Sie folgende Zeilen hinzu:

   ```java
   package com.wingtiptoys.kafka;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.messaging.Source;
   import org.springframework.messaging.support.GenericMessage;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;

   @EnableBinding(Source.class)
   @RestController
   public class KafkaSource {
      @Autowired
      private Source source;

      @PostMapping("/messages")
      public String sendMessage(@RequestBody String message) {
         this.source.output().send(new GenericMessage<>(message));
         return message;
      }
   }
   ```

1. Speichern und schließen Sie die Datei *KafkaSource.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Erstellen einer neuen Klasse für den Senkenconnector

1. Erstellen Sie eine neue Java-Datei mit dem Namen *KafkaSink.java* im Paketverzeichnis Ihrer App, öffnen Sie die Datei in einem Text-Editor, und fügen Sie folgende Zeilen hinzu:

   ```java
   package com.wingtiptoys.kafka;

   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.cloud.stream.annotation.EnableBinding;
   import org.springframework.cloud.stream.annotation.StreamListener;
   import org.springframework.cloud.stream.messaging.Sink;

   @EnableBinding(Sink.class)
   public class KafkaSink {
      private static final Logger LOGGER = LoggerFactory.getLogger(KafkaSink.class);

      @StreamListener(Sink.INPUT)
      public void handleMessage(String message) {
         LOGGER.info("New message received: " + message);
      }
   }
   ```

1. Speichern und schließen Sie die Datei *KafkaSink.java*.

## <a name="build-and-test-your-application"></a>Erstellen und Testen der Anwendung

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie zum Ordnerverzeichnis, in dem sich die Datei *pom.xml* befindet. Beispiel:

   ```cmd
   cd C:\SpringBoot\kafka
   ```
   
   Oder

   ```bash
   cd /users/example/home/kafka
   ```
   
1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```shell
   mvn clean package -Dmaven.test.skip=true
   mvn spring-boot:run
   ```

1. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit *curl* testen. Beispiel:

   ```shell
   curl -X POST -H "Content-Type: text/plain" -d "hello" http://localhost:8080/messages
   ```
   In den Protokollen Ihrer Anwendung sollte „hello“ angezeigt werden. Beispiel:

   ```output
   2020-10-12 16:56:19.827  INFO 13272 --- [nio-8080-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka version: 2.5.1
   2020-10-12 16:56:19.828  INFO 13272 --- [nio-8080-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId: 0efa8fb0f4c73d92
   2020-10-12 16:56:19.830  INFO 13272 --- [nio-8080-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka startTimeMs: 1602492979827
   2020-10-12 16:56:22.277  INFO 13272 --- [container-0-C-1] com.wingtiptoys.kafka.KafkaSink          : New message received: hello
   ```


> [!NOTE]
> 
> Sie können die Datei *KafkaSource.java* zu Testzwecken ändern, sodass sie wie im folgenden Beispiel gezeigt ein einfaches HTML-Formular enthält:
> 
> ```java
> package com.wingtiptoys.kafka;
>    
> import org.springframework.beans.factory.annotation.Autowired;
> import org.springframework.cloud.stream.annotation.EnableBinding;
> import org.springframework.cloud.stream.messaging.Source;
> import org.springframework.messaging.support.GenericMessage;
> import org.springframework.web.bind.annotation.GetMapping;
> import org.springframework.web.bind.annotation.PostMapping;
> import org.springframework.web.bind.annotation.RequestBody;
> import org.springframework.web.bind.annotation.RequestParam;
> import org.springframework.web.bind.annotation.RestController;
> 
> @EnableBinding(Source.class)
> @RestController
> public class KafkaSource {
>   @Autowired
>   private Source source;
> 
>   @GetMapping("/")
>   public String sendForm() {
>     return "<html><body>" +
>       "<form action=\"/messages\" method=\"post\">" +
>       "<input type=\"text\" name=\"text\">" +
>       "<input type=\"submit\">" +
>       "</form></body><html>";
>     }
> 
>   @PostMapping("/messages")
>   public String sendMessage(@RequestBody String message) {
>     this.source.output().send(new GenericMessage<>(message));
>     return message;
>   }
> }
> ```
> 
> Dies ermöglicht es Ihnen, einen Webbrowser zum Testen der Anwendung zu verwenden:
> 
> ![Testen der Anwendung mithilfe eines Webbrowsers][TB01]
> 
> Wenn Sie das Formular senden, zeigt die Anwendung die Ergebnisse an:
> 
> ![Antwort der Anwendung in einem Webbrowser][TB02]
> 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Azure-Unterstützung für Event Hub Stream Binder und Apache Kafka finden Sie in den folgenden Artikeln:

* [Was ist Azure Event Hubs?](/azure/event-hubs/event-hubs-about)

* [Azure Event Hubs für Apache Kafka](/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)

* [Erstellen eines Event Hubs-Namespaces und eines Event Hubs mithilfe des Azure-Portals](/azure/event-hubs/event-hubs-create)

* [Erstellen von Apache Kafka-fähigen Event Hubs](/azure/event-hubs/event-hubs-create-kafka-enabled)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter „Azure für Java-Entwickler“ und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[Apache Kafka]: http://kafka.apache.org
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[IMG01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-01.png
[IMG02]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-02.png
[IMG05]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-kafka-event-hub-05.png

[SI01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/create-project-01.png

[TB01]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-01.png
[TB02]: media/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub/test-browser-02.png
