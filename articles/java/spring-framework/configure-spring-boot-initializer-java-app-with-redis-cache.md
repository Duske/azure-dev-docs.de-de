---
title: 'Erstellen einer Spring Boot Initializer-App: Redis Cache'
description: Konfigurieren Sie eine mit Spring Initializr erstellte Spring Boot-Anwendung für die Verwendung von Redis in der Cloud mit Azure Redis Cache.
services: redis-cache
documentationcenter: java
ms.date: 10/13/2020
ms.service: cache
ms.tgt_pltfrm: cache-redis
ms.topic: conceptual
ms.custom: devx-track-java
ms.openlocfilehash: 7d8ee875339adb741fbddeba6d4328eb22cd3e46
ms.sourcegitcommit: 5c7f5fef798413b1a304cc9ee31c8518b73f27eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066273"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-redis-in-the-cloud-with-azure-redis-cache"></a>Konfigurieren einer Spring Boot Initializer-App für die Verwendung von Redis in der Cloud mit Azure Redis Cache

Dieser Artikel führt Sie durch das Erstellen einer Redis Cache-Instanz in der Cloud über das Azure-Portal, das anschließende Erstellen einer benutzerdefinierten Anwendung mit **[Spring Initializr]** und schließlich das Erstellen einer Java-Webanwendung, die Daten mithilfe Ihrer Redis Cache-Instanz speichert und abruft.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Erstellen einer benutzerdefinierten Anwendung mit dem Spring Initializr

1. Navigieren Sie zu <https://start.spring.io/>.

1. Geben Sie an, dass Sie ein **Maven** -Projekt mit **Java** generieren möchten, wählen Sie Java-Version **8** aus, und geben Sie die Namen für **Gruppe** und **Artefakt** für Ihre Anwendung ein.

1. Fügen Sie Abhängigkeiten für den Abschnitt **Spring Web** hinzu, und aktivieren Sie das Kontrollkästchen für **Web**. Scrollen Sie dann nach unten zum Abschnitt **NoSQL** , und aktivieren Sie das Kontrollkästchen für **Spring Data Reactive Redis** (Spring Data Redis (reaktiv)). 
1. Klicken Sie am unteren Seitenrand auf die Schaltfläche **Projekt generieren**.

   ![Grundlegende Spring Initializr-Optionen][SI01]

   > [!NOTE]
   >
   > Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für **Gruppe** und **Artefakt** , z. B. *com.contoso.myazuredemo*.
   >

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

   ![Herunterladen eines benutzerdefinierten Spring Boot-Projekts][SI03]

1. Nachdem Sie die Dateien auf dem lokalen System extrahiert haben, kann Ihre benutzerdefinierte Spring Boot-Anwendung bearbeitet werden.

   ![Dateien eines benutzerdefinierten Spring Boot-Projekts][SI04]

## <a name="create-a-redis-cache-on-azure"></a>Erstellen eines Redis-Caches in Azure

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und klicken Sie auf **+Neu**.

1. Klicken Sie auf **Datenbank** , und klicken Sie dann auf **Redis Cache**.

   ![Auswählen von Redis Cache im Azure-Portal][AZ02]

1. Geben Sie auf der Seite **Neuer Redis Cache** Folgendes an:

   * Geben Sie den **DNS-Namen** für den Cache ein.
   * Geben Sie Ihre Informationen zu **Abonnement** , **Ressourcengruppe** , **Standort** und **Tarif** an.
   * Wählen Sie für dieses Tutorial **Unblock port 6379** aus.

   > [!NOTE]
   >
   > Sie können SSL mit Redis Caches verwenden, müssen jedoch einen anderen Redis-Client als Jedis verwenden. Weitere Informationen finden Sie unter [Verwenden von Azure Redis Cache mit Java][Redis Cache with Java].
   >

   Wenn Sie diese Optionen angegeben haben, klicken Sie zum Erstellen des Cache auf **Erstellen**.

   ![Erstellen des Cache im Azure-Portal][AZ03]

1. Nachdem Ihr Cache abgeschlossen wurde, wird er auf Ihrem Azure- **Dashboard** sowie auf den Seiten **Alle Ressourcen** und **Redis-Caches** aufgeführt. Sie können an jedem dieser Orte auf Ihren Cache klicken, um die Seite „Eigenschaften“ für den Cache zu öffnen.

   ![Im Azure-Portal bereitgestellte Ressourcen][AZ04]

1. Wenn die Seite mit der Liste der Eigenschaften für den Cache angezeigt wird, klicken Sie auf **Zugriffsschlüssel** , und kopieren Sie die Zugriffsschlüssel für Ihren Cache.

   ![Kopieren der Zugriffsschlüssel im Abschnitt „Zugriffsschlüssel“][AZ05]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Konfigurieren Ihres benutzerdefinierten Spring Boot für die Verwendung von Redis Cache

1. Suchen Sie die Datei *application.properties* im *Ressourcen* -Verzeichnis Ihrer App, oder erstellen Sie diese Datei, wenn sie noch nicht vorhanden ist.

   ![Suchen der Datei „application.properties“][RE01]

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie der Datei die folgenden Zeilen hinzu. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Eigenschaften aus Ihrem Cache:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![Bearbeiten der Datei „application.properties“][RE02]

   > [!NOTE] 
   > 
   > Wenn Sie einen anderen SSL-fähigen Redis-Client als Jedis verwendet haben, geben Sie in Ihrer Datei *application.properties* an, dass Sie SSL verwenden möchten, und verwenden Sie Port 6380. Beispiel: 
   > 
   > ```yaml
   > # Specify the DNS URI of your Redis cache.
   > spring.redis.host=myspringbootcache.redis.cache.windows.net
   > # Specify the access key for your Redis cache.
   > spring.redis.password=57686f6120447564652c2049495320526f636b73=
   > # Specify that you want to use SSL.
   > spring.redis.ssl=true
   > # Specify the SSL port for your Redis cache.
   > spring.redis.port=6380
   > ```
   > 
   > Weitere Informationen finden Sie unter [Verwenden von Azure Redis Cache mit Java][Redis Cache with Java]. 
   > 

1. Speichern und schließen Sie die Datei *application.properties*.

1. Erstellen Sie einen Ordner mit dem Namen *controller* unter den Quellordner für Ihr Paket, zum Beispiel:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   Oder

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Erstellen Sie eine neue Datei mit dem Namen *HelloController.java* im *controller* -Ordner. Öffnen Sie die Datei in einem Text-Editor, und fügen Sie den folgenden Code hinzu:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Dabei müssen Sie `com.contoso.myazuredemo` durch den Paketnamen für das Projekt ersetzen.

1. Speichern und schließen Sie die Datei *HelloController.java*.

1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Testen Sie die Web-App unter `http://localhost:8080` in einem Webbrowser, oder verwenden Sie die Syntax aus dem folgenden Beispiel (sofern Curl verfügbar ist):

   ```shell
   curl http://localhost:8080
   ```

   Es sollte die Meldung „Hello World!“ von Ihrem Beispielcontroller angezeigt werden, die dynamisch aus dem Redis Cache abgerufen wird.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Bereitstellen einer Spring Boot-Anwendung für Linux in Azure App Service](deploy-spring-boot-java-app-on-linux.md)

* [Ausführen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

Weitere Informationen zu den ersten Schritten mit Redis Cache mit Java in Azure finden Sie unter [Verwenden von Azure Redis Cache mit Java][Redis Cache with Java].

**[Spring Framework]** ist eine Open-Source-Lösung, die Java-Entwicklern beim Erstellen von Anwendungen auf Unternehmensebene hilft. Eines der gängigsten Projekte, das auf dieser Plattform aufbaut, ist [Spring Boot]. Es bietet einen vereinfachten Ansatz für das Erstellen eigenständiger Java-Anwendungen. Um Entwicklern den Einstieg in Spring Boot zu vereinfachen, werden unter <https://github.com/spring-guides/> mehrere Spring Boot-Beispielpakete bereitgestellt. Neben der Auswahl einer Liste grundlegender Spring Boot-Projekte ermöglicht **[Spring Initializr]** Entwicklern einen einfacheren Einstieg bei der Erstellung von benutzerdefinierten Spring Boot-Anwendungen.

<!-- URL List -->

[Azure für Java-Entwickler]: ../index.yml
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/java/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: /azure/redis-cache/cache-java-get-started

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ01.png
[AZ02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ02.png
[AZ03]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ03.png
[AZ04]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ04.png
[AZ05]: media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ05.png

[SI01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI01.png
[SI02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI02.png
[SI03]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI03.png
[SI04]: media/configure-spring-boot-initializer-java-app-with-redis-cache/SI04.png

[RE01]: media/configure-spring-boot-initializer-java-app-with-redis-cache/RE01.png
[RE02]: media/configure-spring-boot-initializer-java-app-with-redis-cache/RE02.png
