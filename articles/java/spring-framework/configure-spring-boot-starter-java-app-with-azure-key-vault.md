---
title: Verwenden von Spring Boot Starter für Azure Key Vault
description: Hier erfahren Sie, wie Sie eine Spring Boot Initializer-App mit Azure Key Vault Starter konfigurieren.
services: key-vault
documentationcenter: java
ms.date: 10/29/2019
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 2df574104376ec1900c7dc5cbd4f0a49ef1f4732
ms.sourcegitcommit: e6cdb0ce11a8272195a0072c7c91cc9b7e89b0b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138736"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Verwenden von Spring Boot Starter für Azure Key Vault

In diesem Artikel erfahren Sie, wie Sie eine App mit **[Spring Initializr]** erstellen. Spring Initializr verwendet Spring Boot Starter für Azure Key Vault, um eine Verbindungszeichenfolge abzurufen, die als Geheimnis in einem Schlüsseltresor gespeichert ist.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

## <a name="create-an-app-using-spring-initializr"></a>Erstellen einer App mithilfe von Spring Initializr

Gehen Sie wie folgt vor, um die Anwendung mithilfe von Spring Initializr zu erstellen:

1. Navigieren Sie zu <https://start.spring.io/>.

1. Geben Sie an, dass Sie ein Projekt vom Typ **Maven** mit **Java** generieren möchten.  

1. Geben Sie einen Namen für die **Gruppe** und das **Artefakt** für Ihre Anwendung ein.

1. Geben Sie im Abschnitt **Abhängigkeiten** die Zeichenfolge **Azure Key Vault** ein.

1. Scrollen Sie auf der Seite nach unten, und klicken Sie auf **Generieren**.

   ![Generieren eines Spring Boot-Projekts][secrets-01]

1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

## <a name="sign-into-azure"></a>Anmelden bei Azure

Mit dem folgenden Verfahren wird der Benutzer über die Azure CLI authentifiziert:

1. Öffnen Sie eine Eingabeaufforderung.

1. Melden Sie sich mithilfe der Azure CLI bei Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

Folgen Sie den Anweisungen, um den Anmeldevorgang abzuschließen.

1. Listen Sie Ihre Abonnements auf:

   ```azurecli
   az account list
   ```

   Azure gibt eine Liste mit Ihren Abonnements zurück. Kopieren Sie die GUID für das Abonnement, das Sie verwenden möchten. Beispiel:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Geben Sie die GUID für das Konto an, das Sie mit Azure verwenden möchten. Beispiel:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-a-new-azure-key-vault"></a>Erstellen einer neuen Azure Key Vault-Instanz

Mit dem folgenden Verfahren wird der Schlüsseltresor erstellt und initialisiert:

1. Erstellen Sie eine Ressourcengruppe für die Azure-Ressourcen, die Sie für Ihren Schlüsseltresor verwenden. Beispiel:

   ```azurecli
   az group create --name vged-rg2 --location westus
   ```

   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `name` | Gibt einen eindeutigen Namen für Ihre Ressourcengruppe an. |
   | `location` | Gibt die [Azure-Region](https://azure.microsoft.com/regions/) an, in der Ihre Ressourcengruppe gehostet wird. |

   Die Azure CLI zeigt die Ergebnisse der Erstellung Ihrer Ressourcengruppe an, Beispiel:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/vged-rg2",
     "location": "westus",
     "managedBy": null,
     "name": "vged-rg2",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Erstellen Sie in der Ressourcengruppe einen neuen Schlüsseltresor. Beispiel:

   ```azurecli
   az keyvault create --resource-group vged-rg2 \
       --name vgedkeyvault \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --sku standard --query properties.vaultUri \
       --location westus
   ```

   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `name` | Gibt einen eindeutigen Namen für Ihren Schlüsseltresor an. |
   | `enabled-for-deployment` | Gibt die [Bereitstellungsoption für den Schlüsseltresor](/cli/azure/keyvault) an. |
   | `enabled-for-disk-encryption` | Gibt die [Verschlüsselungsoption für den Schlüsseltresor](/cli/azure/keyvault) an. |
   | `enabled-for-template-deployment` | Gibt die [Verschlüsselungsoption für den Schlüsseltresor](/cli/azure/keyvault) an. |
   | `sku` | Gibt die [SKU-Option für den Schlüsseltresor](/cli/azure/keyvault) an. |
   | `query` | Gibt einen Wert an, der aus der Antwort abgerufen werden soll. Hierbei handelt es sich um den Schlüsseltresor-URI, den Sie im Rahmen dieses Tutorials benötigen. |
   | `location` | Gibt die [Azure-Region](https://azure.microsoft.com/regions/) an, in der Ihre Ressourcengruppe gehostet wird. |

   Die Azure-Befehlszeilenschnittstelle zeigt den URI für den Schlüsseltresor an. Dieser wird später verwendet. Beispiel:  

   ```output
   "https://vgedkeyvault.vault.azure.net"
   ```

3. Speichern Sie ein Geheimnis in Ihrem neuen Schlüsseltresor. Beispiel:

   ```azurecli
   az keyvault secret set --vault-name "vgedkeyvault" \
       --name "connectionString" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `vault-name` | Gibt den Schlüsseltresornamen von vorhin an. |
   | `name` | Gibt den Namen Ihres Geheimnisses an. |
   | `value` | Gibt den Wert Ihres Geheimnisses an. |

   Die Azure-Befehlszeilenschnittstelle zeigt die Ergebnisse der Geheimniserstellung an. Beispiel:  

   ```json
   {
     "attributes": {
       "created": "2017-12-01T09:00:16+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2017-12-01T09:00:16+00:00"
     },
     "contentType": null,
     "id": "https://vgedkeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Konfigurieren und Kompilieren Ihrer App

Gehen Sie wie folgt vor, um Ihre Anwendung zu konfigurieren und zu kompilieren:

1. Extrahieren Sie die Dateien aus den zuvor heruntergeladenen Spring Boot-Projektarchivdateien in ein Verzeichnis.

2. Navigieren Sie in Ihrem Projekt zum Ordner *src/main/resources*, und öffnen Sie die Datei *application.properties* in einem Text-Editor.

3. Fügen Sie die Werte für Ihren Schlüsseltresor hinzu. Verwenden Sie dabei Werte aus den vorherigen Schritten dieses Tutorials. Beispiel:

   ```yaml
   azure.keyvault.uri=https://vgedkeyvault.vault.azure.net/
   azure.keyvault.enabled=true
   ```

   Hierbei gilt:

   |          Parameter          |                                 BESCHREIBUNG                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Gibt den URI von der Erstellung Ihres Schlüsseltresors an.           |
    
    
4. Navigieren Sie zur Quellcode-Hauptdatei Ihres Projekts. Beispiel: */src/main/java/com/vged/secrets*.

5. Öffnen Sie die Java-Hauptdatei der Anwendung in einem Text-Editor (z. B. *SecretsApplication.java*), und fügen Sie in der Datei die folgenden Zeilen hinzu:

   ```java
   package com.vged.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;
   
   @SpringBootApplication
   @RestController
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }
   
      @GetMapping("get")
      public String get() {
         return connectionString;
      }
   
      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Dieses Codebeispiel ruft die Verbindungszeichenfolge aus dem Schlüsseltresor ab und zeigt sie unter der URL `https://{your-appservice-name}.azurewebsites.net/get` an.

6. Speichern und schließen Sie die Java-Datei.

7. Deaktivieren Sie den Test, und erstellen Sie die JAR-Datei mit Maven.
    
   ```bash
   mvn clean package -Dmaven.test.skip=true
   ```

## <a name="configure-maven-plugin-for-azure-app-service"></a>Konfigurieren des Maven-Plug-Ins für Azure App Service

In diesem Abschnitt wird beschrieben, wie Sie Ihr Spring Boot-Projekt so konfigurieren, dass Ihre App unter Azure App Service bereitgestellt werden kann.

1.  Folgen Sie dem Link zu [Konfigurieren des Maven-Plug-Ins für Azure App Service].
    
    Mit diesem Link wird eine neue Azure App Service-Instanz erstellt. Wenn Sie Ihre App auf einer vorhandenen Instanz bereitstellen möchten, können Sie die Bereitstellung mit dem Befehl `mvn azure-webapp:config` erneut konfigurieren und dabei die entsprechenden Informationen angeben.
    
    ```bash
    [INFO] Scanning for projects...                                                     
    [INFO]                                                                              
    [INFO] ----------------------< com.wingtiptoys:secrets >-----------------------     
    [INFO] Building secrets 0.0.1-SNAPSHOT                                              
    [INFO] --------------------------------[ jar ]---------------------------------     
    [INFO]                                                                              
    [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ secrets ---       
    Please choose which part to config                                                  
    1. Application                                                                      
    2. Runtime                                                                          
    3. DeploymentSlot                                                                   
    Enter index to use: 1                                                              
    Define value for appName(Default: ********):                                      
    Define value for resourceGroup(Default: ********):                                 
    Define value for region(Default: ********):                                           
    Define value for pricingTier(Default: P1v2):                                        
    1. b1                                                                               
    2. b2                                                                               
    3. b3                                                                               
    4. d1                                                                               
    5. f1                                                                               
    6. p1v2 [*]                                                                         
    7. p2v2                                                                             
    8. p3v2                                                                             
    9. s1                                                                               
    10. s2                                                                              
    11. s3                                                                              
    Enter index to use:                                                                 
    Please confirm webapp properties                                                                                                          
    ```
    
    Es ist auch möglich, den Abschnitt `<configuration>` von `<azure-webapp-maven-plugin>` direkt in *pom.xml* zu bearbeiten. Ändern Sie die Werte `<resourceGroup>`,`<appName>` und `<region>` gemäß Ihrer jeweiligen App Service-Instanz.

2. Weisen Sie App Service die Identität zu, und notieren Sie sich die `principalId` für den nächsten Schritt.

   ```bash
   az webapp identity assign --name your-appservice-name \
      --resource-group vged-rg2
   ```
   
3. Gewähren Sie die Berechtigung für MSI.

   ```bash
   az keyvault set-policy --name vgedkeyvault \
       --object-id your-managed-identity-objectId \
       --secret-permissions get list
   ```

## <a name="deploy-the-app-to-azure-and-run-app-service"></a>Bereitstellen der App in Azure und Ausführen von App Service

Sie können Ihre Web-App nun in Azure bereitstellen. Führen Sie dazu die folgenden Schritte aus:

1. Erstellen Sie die JAR-Datei mit Maven neu, falls Sie Änderungen an der Datei *pom.xml* vorgenommen haben.

   ```bash
   mvn clean package
   ```
   
2. Stellen Sie Ihre App mit Maven unter Azure bereit.

   ```bash
   mvn azure-webapp:deploy
   ```
   
3. Starten Sie Ihre App Service-Instanz neu.

4. Navigieren Sie im Browser zur URL `https://{your-appservice-name}.azurewebsites.net/get`, um Ihre Verbindungszeichenfolge (`connectionString`) zu erhalten.
   

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie mit **[Spring Initializr]** eine neue Java-Web-App und eine Azure Key Vault-Instanz zum Speichern vertraulicher Informationen erstellt und anschließend Ihre Anwendung zum Abrufen von Informationen aus dem Schlüsseltresor konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure Key Vault-Instanzen finden Sie in den folgenden Artikeln:

* [Dokumentation zu Key Vault]

* [Erste Schritte mit dem Azure-Schlüsseltresor]

Weitere Informationen zur Verwendung von Spring Boot-Anwendungen in Azure finden Sie in den folgenden Artikeln:

* [Bereitstellen von Spring Boot-Anwendungen in Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Ausführen einer Spring Boot-Anwendung in einem Kubernetes-Cluster in Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

Weitere Informationen zur Verwendung von verwalteten Identitäten für App Service finden Sie unter [Verwenden von verwalteten Identitäten für App Service].

<!-- URL List -->

[Dokumentation zu Key Vault]: /azure/key-vault/
[Erste Schritte mit dem Azure-Schlüsseltresor]: /azure/key-vault/key-vault-get-started
[Azure für Java-Entwickler]: /azure/developer/java/
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Verwenden von verwalteten Identitäten für App Service]: /azure/app-service/overview-managed-identity
[Konfigurieren des Maven-Plug-Ins für Azure App Service]: /azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
