---
title: Verwenden von Spring Boot Starter für Azure Storage
description: Hier erfahren Sie, wie Sie eine Spring Boot Initializer-App mit Azure Storage Starter konfigurieren.
services: storage
documentationcenter: java
ms.date: 10/14/2020
ms.service: storage
ms.topic: article
ms.workload: storage
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7e6437859b7b5e67edd9386d0391535dd6537ff7
ms.sourcegitcommit: 709fa38a137b30184a7397e0bfa348822f3ea0a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96442118"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-storage"></a>Verwenden von Spring Boot Starter für Azure Storage

In diesem Artikel wird beschrieben, wie Sie mit **Spring Initializr** eine benutzerdefinierte Anwendung erstellen, Ihrer Anwendung anschließend Azure Storage Starter hinzufügen und dann mithilfe der Anwendung ein Blob in Ihr Azure-Speicherkonto hochladen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) anwenden oder sich für ein [Kostenloses Azure-Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren
* Die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/index)
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

> [!IMPORTANT]
>
> Für die Schritte in diesem Artikel wird mindestens die Spring Boot-Version 2.0 benötigt.
>

## <a name="create-an-azure-storage-account-and-blob-container-for-your-application"></a>Erstellen eines Azure-Speicherkontos und Blobcontainers für Ihre Anwendung

Hier ist die Vorgehensweise zum Erstellen eines Azure-Speicherkontos und eines Containers im Portal beschrieben.

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Wählen Sie **Ressource erstellen** > **Erste Schritte** > **Speicherkonto** aus.

   ![Erstellen eines Azure-Speicherkontos][IMG01]

1. Geben Sie auf der Seite **Speicherkonto erstellen** folgende Informationen ein:

   * Wählen Sie **Abonnement** aus.
   * Wählen Sie **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe.
   * Geben Sie unter **Speicherkontoname** einen eindeutigen Namen ein. Dieser wird als Teil des URI für Ihr Speicherkonto verwendet. Beispiel: Wenn Sie **wingtiptoysstorage** für **Name** eingegeben haben, lautet der URI *wingtiptoysstorage.core.windows.net*.
   * Geben Sie den **Speicherort** für das Speicherkonto an.
1. Wählen Sie nach dem Angeben der obigen Optionen die Option **Überprüfen + erstellen** aus. 
1. Überprüfen Sie die Angaben, und wählen Sie anschließend die Option **Erstellen** aus, um Ihr Speicherkonto zu erstellen.
1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**.
1. Wählen Sie **Container** aus.
1. Wählen Sie **Container** aus.
   * Geben Sie dem Container einen Namen.
   * Wählen Sie in der Dropdownliste die Option *Blob* aus.

   ![Erstellen eines Blobcontainers][IMG02]

1. Ihr Blobcontainer wird im Azure-Portal aufgelistet, nachdem er erstellt wurde.

Sie können auch die Azure CLI verwenden, um mit den folgenden Schritten ein Azure-Speicherkonto und einen Container zu erstellen. Ersetzen Sie hierbei die Platzhalterwerte (in spitzen Klammern) durch Ihre eigenen Werte.

1. Öffnen Sie eine Eingabeaufforderung.
1. Melden Sie sich bei Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```
   
1. Falls Sie nicht über eine Ressourcengruppe verfügen, können Sie mit dem folgenden Befehl eine erstellen:
   
   ```azurecli
   az group create \
      --name <resource-group> \
      --location <location>
   ```
   
1. Erstellen Sie mit dem folgenden Befehl ein Speicherkonto:
  
   ```azurecli
    az storage account create \
      --name <storage-account> \
      --resource-group <resource-group> \
      --location <location> 
   ```

1. Zum Erstellen eines Containers verwenden Sie den folgenden Befehl:
   
   ```azurecli
    az storage container create \
      --account-name <storage-account-name> \
      --name <container-name> \
      --auth-mode login
   ```
## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Erstellen einer einfachen Spring Boot-Anwendung mit Spring Initializr

Gehen Sie zum Erstellen der Spring Boot-Anwendung wie folgt vor:

1. Navigieren Sie zu <https://start.spring.io/>.

1. Verwenden Sie die folgenden Optionen:

   * Generieren Sie ein Projekt vom Typ **Maven**.
   * Geben Sie **Java 11** an.
   * Geben Sie für **Spring Boot** mindestens Version 2.3 an.
   * Geben Sie Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung an.
   * Fügen Sie die Abhängigkeit **Spring Web** hinzu.

      ![Grundlegende Spring Initializr-Optionen][SI01]

   > [!NOTE]
   > Spring Initializr verwendet zur Erstellung des Paketnamens die Namen für die **Gruppe** und das **Artefakt**, beispielsweise *com.wingtiptoys.storage*.

1. Wählen Sie nach Angabe der obigen Optionen **GENERIEREN**  aus.

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

1. Nachdem Sie die Dateien auf Ihrem lokalen System extrahiert haben, kann Ihre einfache Spring Boot-Anwendung bearbeitet werden.

## <a name="configure-your-spring-boot-app-to-use-the-azure-storage-starter"></a>Konfigurieren der Spring Boot-App zur Verwendung von Azure Storage Starter

Gehen Sie wie folgt vor, um die Spring Boot-Anwendung für die Verwendung von Azure Storage zu konfigurieren:

1. Suchen Sie im Stammverzeichnis Ihrer App nach der Datei *pom.xml*. Beispiel:

   `C:\SpringBoot\storage\pom.xml`

   Oder

   `/users/example/home/storage/pom.xml`

1. Öffnen Sie die Datei *pom.xml* in einem Text-Editor, und fügen Sie Spring Cloud Azure Storage Starter der Liste von `<dependencies>` hinzu:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-starter-azure-storage</artifactId>
      <version>1.2.8</version>
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

## <a name="create-an-azure-credential-file"></a>Erstellen einer Azure-Anmeldeinformationsdatei

Gehen Sie zum Erstellen der Azure-Anmeldeinformationsdatei wie folgt vor:

1. Öffnen Sie eine Eingabeaufforderung.

1. Navigieren Sie zum Verzeichnis *resources* Ihrer Spring Boot-App. Beispiel:

   ```cmd
   cd C:\SpringBoot\storage\src\main\resources
   ```

   Oder

   ```bash
   cd /users/example/home/storage/src/main/resources
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

## <a name="configure-your-spring-boot-app-to-use-your-azure-storage-account"></a>Konfigurieren der Spring Boot-App zur Verwendung Ihres Azure-Speicherkontos

Gehen Sie wie folgt vor, um die Spring Boot-Anwendung für die Verwendung Ihres Azure-Speicherkontos zu konfigurieren:

1. Suchen Sie im Verzeichnis *resources* Ihrer App nach der Datei *application.properties*. Beispiel:

   `C:\SpringBoot\storage\src\main\resources\application.properties`

   Oder

   `/users/example/home/storage/src/main/resources/application.properties`

2. Öffnen Sie die Datei *application.properties* in einem Text-Editor, fügen Sie die folgenden Zeilen hinzu, und ersetzen Sie dann die Beispielwerte durch die entsprechenden Eigenschaften für Ihr Speicherkonto:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=westUS
   spring.cloud.azure.storage.account=wingtiptoysstorage
   blob=azure-blob://containerName/blobName
   ```
   Hierbei gilt:

   |                   Feld                   |                                            BESCHREIBUNG                                            |
   |-------------------------------------------|---------------------------------------------------------------------------------------------------|
   | `spring.cloud.azure.credential-file-path` |            Gibt die Azure-Anmeldeinformationsdatei an, die Sie zuvor in diesem Tutorial erstellt haben.             |
   |    `spring.cloud.azure.resource-group`    |           Gibt die Azure-Ressourcengruppe an, die Ihr Azure-Speicherkonto enthält.            |
   |        `spring.cloud.azure.region`        | Gibt die geografische Region an, die Sie beim Erstellen Ihres Azure-Speicherkontos angegeben haben. |
   |   `spring.cloud.azure.storage.account`    |            Gibt das Azure-Speicherkonto an, das Sie zuvor in diesem Tutorial erstellt haben.             |
   |                   `blob`                  |           Gibt die Namen des Containers und des Blobs an, in denen Sie die Daten speichern möchten.         |
    
3. Speichern und schließen Sie die Datei *application.properties*.

## <a name="add-sample-code-to-implement-basic-azure-storage-functionality"></a>Hinzufügen von Beispielcode zum Implementieren grundlegender Azure-Speicherfunktionen

In diesem Abschnitt erstellen Sie die Java-Klassen, die erforderlich sind, um ein Blob in Ihrem Azure-Speicherkonto zu speichern.

### <a name="modify-the-main-application-class"></a>Ändern der Hauptanwendungsklasse

1. Suchen Sie die Java-Hauptanwendungsdatei im Paketverzeichnis Ihrer App. Beispiel:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\StorageApplication.java`

   Oder

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/StorageApplication.java`

1. Öffnen Sie die Java-Hauptanwendungsdatei in einem Text-Editor, und fügen Sie die folgenden Zeilen hinzu. Ersetzen Sie „wingtiptoys“ durch Ihre eigenen Werte:

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class StorageApplication {
      public static void main(String[] args) {
         SpringApplication.run(StorageApplication.class, args);
      }
   }
   ```

1. Speichern und schließen Sie die Java-Hauptanwendungsdatei.

### <a name="add-a-blob-controller-class"></a>Hinzufügen einer Blobcontrollerklasse

1. Erstellen Sie eine neue Java-Datei mit dem Namen *BlobController.java* im Paketverzeichnis Ihrer App. Beispiel:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\BlobController.java`

   Oder

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/BlobController.java`

1. Öffnen Sie die Java-Blobcontrollerdatei in einem Text-Editor, und fügen Sie die folgenden Zeilen hinzu.  Ändern Sie *wingtiptoys* in Ihre Ressourcengruppe und *storage* in Ihren Artefaktnamen.

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.WritableResource;
   import org.springframework.util.StreamUtils;
   import org.springframework.web.bind.annotation.*;

   import java.io.IOException;
   import java.io.OutputStream;
   import java.nio.charset.Charset;

   @RestController
   @RequestMapping("blob")
   public class BlobController {
   
       @Value("${blob}")
       private Resource blobFile;
   
       @GetMapping
       public String readBlobFile() throws IOException {
           return StreamUtils.copyToString(
                   this.blobFile.getInputStream(),
                   Charset.defaultCharset());
       }
   
       @PostMapping
       public String writeBlobFile(@RequestBody String data) throws IOException {
           try (OutputStream os = ((WritableResource) this.blobFile).getOutputStream()) {
               os.write(data.getBytes());
           }
           return "file was updated";
       }
   }
   ```

1. Speichern und schließen Sie die Java-Blobcontrollerdatei.

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie zum Ordnerverzeichnis, in dem sich die Datei *pom.xml* befindet. Beispiel:

   ```cmd
   cd C:\SpringBoot\storage
   ```

   Oder
   
   ```bash
   cd /users/example/home/storage
   ```

1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Sobald Ihre Anwendung ausgeführt wird, können Sie sie mit *curl* testen. Beispiel:

   a. Senden Sie eine POST-Anforderung zum Aktualisieren des Inhalts einer Datei:

      ```shell
      curl -d 'new message' -H 'Content-Type: text/plain' localhost:8080/blob
      ```

      Es sollte eine Antwort angezeigt werden, dass die Datei aktualisiert wurde.

   b. Senden Sie eine GET-Anforderung zum Überprüfen des Inhalts der Datei:

      ```shell
      curl -X GET http://localhost:8080/
      ```

     Der von Ihnen bereitgestellte Text „Hallo Welt“ sollte angezeigt werden.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie eine neue Java-Anwendung mit **Spring Initializr** erstellt, Ihrer Anwendung Azure Storage Starter hinzugefügt und Ihre Anwendung anschließend zum Hochladen eines Blobs in Ihr Azure-Speicherkonto konfiguriert.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zu weiteren verfügbaren Spring Boot Starter-Optionen für Microsoft Azure finden Sie unter [Spring Boot Starter für Azure](spring-boot-starters-for-azure.md).

Ausführliche Informationen zu weiteren Azure Storage-APIs, die Sie über Ihre Spring Boot-Anwendungen aufrufen können, finden Sie in den folgenden Artikeln:
* [Verwenden von Azure Blob Storage mit Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Verwenden des Warteschlangenspeichers mit Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Verwenden von Azure Table Storage mit Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Entwickeln für Azure Files mit Java](/azure/storage/files/storage-java-how-to-use-file-storage)

<!-- IMG List -->

[IMG01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-01.png
[IMG02]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-02.png

[SI01]: media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-01.png
