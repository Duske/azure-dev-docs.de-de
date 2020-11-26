---
title: Spring Boot Starter für Azure
description: In diesem Artikel werden die verschiedenen verfügbaren Spring Boot Starter-Optionen für Azure beschrieben.
documentationcenter: java
ms.date: 09/29/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 3e73fda380f2743f6a3d4b2fb726ee62173b8563
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035368"
---
# <a name="spring-boot-starters-for-azure"></a>Spring Boot Starter für Azure

In diesem Artikel werden die verschiedenen Spring Boot Starter-Optionen für [Spring Initializr] beschrieben, die Java-Entwicklern Integrationsfeatures für die Arbeit mit Microsoft Azure bieten.

>[!div class="mx-imgBorder"]
![Konfigurieren von Azure Spring Boot Starter-Instanzen mit Initializr][configure-azure-spring-boot-starters-with-initializr]

> [!NOTE]
>
> Spring Initializr nutzt als Standardversion Java 11. Um die in diesem Thema beschriebenen Spring Boot Starter verwenden zu können, müssen Sie stattdessen Java 8 auswählen.
> 

Für Azure stehen aktuell folgende Spring Boot Starter-Optionen zur Verfügung:

* **[Azure-Support](#azure-support)**

   Bietet Unterstützung für die automatische Konfiguration von Azure-Diensten wie Service Bus, Storage und Active Directory.

* **[Azure Active Directory](#azure-active-directory)**

   Bietet Integrationsunterstützung für Spring Security mit Azure Active Directory-Authentifizierung.

* **[Azure-Schlüsseltresor](#azure-key-vault)**

   Bietet Spring-Wertanmerkungsunterstützung für die Integration von Azure Key Vault-Geheimnissen.

* **[Azure Storage](#azure-storage)**

   Bietet Spring Boot-Unterstützung für Azure Storage-Dienste.
   
   > [!NOTE]
   >
   > Die neue Version von Spring Boot Starter für Azure Storage unterstützt derzeit nicht das Hinzufügen einer Azure Storage-Abhängigkeit über Spring Initializr. Sie können jedoch die Abhängigkeit hinzufügen, indem Sie nach dem Generieren des Projekts die Datei *pom.xml* ändern.
   > 

<a name="azure-support"></a>
## <a name="azure-support"></a>Azure-Support

Diese Spring Boot Starter-Option bietet Unterstützung für die automatische Konfiguration von Azure-Diensten wie Service Bus, Storage, Active Directory, Cosmos DB und Key Vault.

Verwendungsbeispiele für die verschiedenen Azure-Features, die von dieser Starter-Option bereitgestellt werden, finden Sie hier:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples>

Wenn Sie diese Starter-Option einem Spring Boot-Projekt hinzufügen, werden an der Datei *pom.xml* folgende Änderungen vorgenommen:

* Dem Element `<properties>` wird folgende Eigenschaft hinzugefügt:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* Die Standardabhängigkeit `spring-boot-starter` wird durch Folgendes ersetzt:

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* Der Datei wird der folgende Abschnitt hinzugefügt:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-active-directory"></a>
## <a name="azure-active-directory"></a>Azure Active Directory

Diese Spring Boot Starter-Option bietet Unterstützung für die automatische Konfiguration von Spring Security zur Integration von Azure Active Directory für die Authentifizierung.

Verwendungsbeispiele für die Azure Active Directory-Features, die von dieser Starter-Option bereitgestellt werden, finden Sie hier:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/>

Wenn Sie diese Starter-Option einem Spring Boot-Projekt hinzufügen, werden an der Datei *pom.xml* folgende Änderungen vorgenommen:

* Dem Element `<properties>` wird folgende Eigenschaft hinzugefügt:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* Die Standardabhängigkeit `spring-boot-starter` wird durch Folgendes ersetzt:

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-active-directory-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* Der Datei wird der folgende Abschnitt hinzugefügt:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-key-vault"></a>
## <a name="azure-key-vault"></a>Azure-Schlüsseltresor

Diese Spring Boot Starter-Option bietet Spring-Wertanmerkungsunterstützung für die Integration von Azure Key Vault-Geheimnissen.

Verwendungsbeispiele für die Azure Key Vault-Features, die von dieser Starter-Option bereitgestellt werden, finden Sie hier:

* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-keyvault-secrets>

Wenn Sie diese Starter-Option einem Spring Boot-Projekt hinzufügen, werden an der Datei *pom.xml* folgende Änderungen vorgenommen:

* Dem Element `<properties>` wird folgende Eigenschaft hinzugefügt:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* Die Standardabhängigkeit `spring-boot-starter` wird durch Folgendes ersetzt:

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
        </dependency>
    
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* Der Datei wird der folgende Abschnitt hinzugefügt:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

<a name="azure-storage"></a>
## <a name="azure-storage"></a>Azure Storage

Diese Spring Boot Starter-Option bietet Spring Boot-Integrationsunterstützung für Azure Storage-Dienste.

Verwendungsbeispiele für die Azure Storage-Features, die von dieser Starter-Option bereitgestellt werden, finden Sie hier:

* [Verwenden von Spring Boot Starter für Azure Storage](configure-spring-boot-starter-java-app-with-azure-storage.md)
* <https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-cloud-sample-storage-queue-operation>

Wenn Sie diese Starter-Option einem Spring Boot-Projekt hinzufügen, werden an der Datei *pom.xml* folgende Änderungen vorgenommen:

* Dem Element `<properties>` wird folgende Eigenschaft hinzugefügt:

   ```xml
   <properties>
      <!-- Other properties will be listed here -->
      <java.version>1.8</java.version>
      <azure.version>2.3.5</azure.version>
   </properties>
   ```

* Die Standardabhängigkeit `spring-boot-starter` wird durch Folgendes ersetzt:

    ```xml
    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-starter-azure-storage</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    ```

* Der Datei wird der folgende Abschnitt hinzugefügt:

   ```xml
   <dependencyManagement>
      <dependencies>
         <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-spring-boot-bom</artifactId>
            <version>${azure.version}</version>
            <type>pom</type>
            <scope>import</scope>
         </dependency>
      </dependencies>
   </dependencyManagement>
   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von [Spring Boot] in Azure finden Sie unter [Spring Framework in Azure].

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

Hilfe zu den ersten Schritten mit eigenen Spring Boot-Anwendungen finden Sie bei **Spring Initializr** unter https://start.spring.io/.

<!-- URL List -->

[Azure für Java-Entwickler]: ../index.yml
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework in Azure]: ./index.yml
[Spring Framework]: https://spring.io/
[Spring Initializr]: https://start.spring.io/

<!-- IMG List -->

[configure-azure-spring-boot-starters-with-initializr]: media/spring-boot-starters-for-azure/configure-azure-spring-boot-starters-with-initializr.png
