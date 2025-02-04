---
title: 'Tutorial: Einlesen eines Geheimnisses aus Azure Key Vault in eine Spring Boot-Anwendung'
description: In diesem Tutorial erstellen Sie eine Spring Boot-App, mit der ein Wert aus Azure Key Vault eingelesen wird, und stellen die App dann in Azure App Service und Azure Spring Cloud bereit.
ms.date: 03/11/2021
ms.service: key-vault
ms.topic: tutorial
ms.custom: devx-track-java, devx-track-azurecli
ms.author: edburns
author: edburns
ms.openlocfilehash: 7d951994a0c563460b2086d0138ce710ef494771
ms.sourcegitcommit: 3536f174735cd3bb7da7e4b266fbf43349a22b67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103193544"
---
# <a name="tutorial-read-a-secret-from-azure-key-vault-in-a-spring-boot-application"></a>Tutorial: Einlesen eines Geheimnisses aus Azure Key Vault in eine Spring Boot-Anwendung

In diesem Tutorial wird veranschaulicht, wie Sie eine Spring Boot-App erstellen, mit der ein Wert aus Azure Key Vault eingelesen wird. Nach der Erstellung der App stellen Sie sie in Azure App Service und Azure Spring Cloud bereit.

Für Spring Boot-Anwendungen werden vertrauliche Informationen, z. B. Benutzernamen und Kennwörter, externalisiert. Die Externalisierung von vertraulichen Informationen ermöglicht eine bessere Wartbarkeit und Prüfbarkeit und eine höhere Sicherheit. Das Speichern von Geheimnissen außerhalb des Codes ist besser als das Hartcodieren der Informationen oder die Inlineverwendung während des Buildvorgangs.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Azure Key Vault-Instanz und Speichern eines Geheimnisses
> * Erstellen einer App mit Spring Initializr
> * Hinzufügen der Key Vault-Integration zur App
> * Bereitstellen in Azure App Service
> * Durchführen der erneuten Bereitstellung in Azure App Service mit verwalteten Identitäten für Azure-Ressourcen
> * In Azure Spring Cloud bereitstellen

## <a name="prerequisites"></a>Voraussetzungen

- [!INCLUDE [free subscription](includes/quickstarts-free-trial-note.md)]
[!INCLUDE [curl](includes/prerequisites-curl.md)]
[!INCLUDE [jq](includes/prerequisites-jq.md)]
[!INCLUDE [Azure CLI](includes/prerequisites-azure-cli.md)]
[!INCLUDE [JDK](includes/prerequisites-java.md)]
[!INCLUDE [Maven](includes/prerequisites-maven.md)]

## <a name="create-a-new-azure-key-vault"></a>Erstellen eines neuen Azure Key Vault-Schlüsseltresors

In den folgenden Abschnitten wird veranschaulicht, wie Sie sich bei Azure anmelden und eine Azure Key Vault-Instanz erstellen.

### <a name="sign-into-azure-and-set-your-subscription"></a>Anmelden bei Azure und Festlegen Ihres Abonnements

Führen Sie zunächst die folgenden Schritte aus, um die Authentifizierung mit der Azure CLI durchzuführen.

1. Optional können Sie sich abmelden und einige Authentifizierungsdateien löschen, um ggf. noch vorhandene Anmeldeinformationen zu entfernen:

   ```azurecli
   az logout
   rm ~/.azure/accessTokens.json
   rm ~/.azure/azureProfile.json
   ```

1. Melden Sie sich mithilfe der Azure CLI bei Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

   Folgen Sie den Anweisungen, um den Anmeldevorgang abzuschließen.

1. Listen Sie Ihre Abonnements auf:

   ```azurecli
   az account list
   ```

   Azure gibt eine Liste mit Ihren Abonnements zurück. Kopieren Sie den Wert von `id` für das gewünschte Abonnement, z. B.:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
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

1. Geben Sie die GUID für das Abonnement an, das Sie mit Azure verwenden möchten. Beispiel:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

### <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

*Dienstprinzipale* von Azure AD ermöglichen den Zugriff auf Azure-Ressourcen innerhalb Ihres Abonnements. Sie können sich einen Dienstprinzipal wie eine Benutzeridentität für einen Dienst vorstellen. Ein „Dienst“ kann hierbei eine beliebige Anwendung, ein Dienst oder eine Plattform sein, für die bzw. den Zugriff auf Azure-Ressourcen erforderlich ist. Sie haben die Möglichkeit, einen Dienstprinzipal mit Zugriffsrechten so zu konfigurieren, dass diese nur für die von Ihnen angegebenen Ressourcen gelten. Anschließend legen Sie für Ihre Anwendung bzw. Ihren Dienst fest, dass diese bzw. dieser die Anmeldeinformationen des Dienstprinzipals verwendet, um auf diese Ressourcen zuzugreifen.

Verwenden Sie den folgenden Befehl, um einen Dienstprinzipal zu erstellen.

```azurecli
az ad sp create-for-rbac --name contososp
```

Der Wert der Option `name` muss in Ihrem Abonnement eindeutig sein. Speichern Sie die Werte, die vom Befehl zurückgegeben werden, damit Sie sie zu einem späteren Zeitpunkt dieses Tutorials verwenden können. Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:

```output
{
  "appId": "sample-app-id",
  "displayName": "ejbcontososp",
  "name": "http://ejbcontososp",
  "password": "sample-password",
  "tenant": "sample-tenant"
}
```

### <a name="create-the-key-vault-instance"></a>Erstellen der Key Vault-Instanz

Verwenden Sie die folgenden Schritte, um die Azure Key Vault-Instanz zu erstellen und zu initialisieren:

1. Bestimmen Sie, in welcher Azure-Region Ihre Ressourcen enthalten sein sollen.
   1. Eine Liste mit den Regionen und den zugehörigen Standorten finden Sie unter [Azure-Geografien](https://azure.microsoft.com/regions/).
   1. Verwenden Sie den Befehl `az account list-locations`, um den richtigen Namen (`Name`) für Ihre gewählte Region zu ermitteln.

      ```azurecli
      az account list-locations --output table
      ```

      In diesem Tutorial wird `eastus` verwendet.

1. Erstellen Sie eine Ressourcengruppe, in der die Key Vault-Instanz und die App Service-App enthalten sein sollen. Der Wert muss innerhalb des Abonnements eindeutig sein. In diesem Tutorial wird `contosorg` verwendet.

   ```azurecli
   az group create --name contosorg --location eastus
   ```

1. Erstellen Sie in der Ressourcengruppe eine neue Key Vault-Instanz.

   ```azurecli
   az keyvault create \
       --resource-group contosorg \
       --name contosokv \
       --enabled-for-deployment true \
       --enabled-for-disk-encryption true \
       --enabled-for-template-deployment true \
       --location eastus
       --query properties.vaultUri \
       --sku standard
   ```

   > [!NOTE]
   > Der Wert der Option `--name` muss im Azure-Abonnement eindeutig sein.

   Diese Tabelle enthält die Beschreibungen für die oben verwendeten Optionen.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `enabled-for-deployment` | Gibt die [Option für die Key Vault-Bereitstellung](/cli/azure/keyvault) an. |
   | `enabled-for-disk-encryption` | Gibt die [Option für die Key Vault-Verschlüsselung](/cli/azure/keyvault) an. |
   | `enabled-for-template-deployment` | Gibt die [Option für die Key Vault-Verschlüsselung](/cli/azure/keyvault) an. |
   | `location` | Gibt die [Azure-Region](https://azure.microsoft.com/regions/) an, in der Ihre Ressourcengruppe gehostet wird. |
   | `name` | Gibt einen eindeutigen Namen für Ihre Key Vault-Instanz an. |
   | `query` | Entnehmen Sie den Key Vault-URI aus der Antwort. Sie benötigen den URI zum Durcharbeiten dieses Tutorials. |
   | `sku` | Gibt die [Option für die Key Vault-SKU](/cli/azure/keyvault) an. |

   Die Azure CLI zeigt den URI für die Key Vault-Instanz an, die Sie später verwenden, z. B.:

   ```output
   "https://contosokv.vault.azure.net/"
   ```

1. Konfigurieren Sie die Key Vault-Instanz, um `get`- und `list`-Vorgänge für diese verwaltete Identität zuzulassen. Der Wert der `object-id` ist die `appId` aus dem obigen Befehl `az ad sp create-for-rbac`.

   ```azurecli
   az keyvault set-policy --name contosokv --spn http://ejbcontososp --secret-permissions get list
   ```

   Die Ausgabe ist ein JSON-Objekt mit vielen Informationen zur Key Vault-Instanz. Es verfügt über den Eintrag `type` mit dem Wert `Microsoft.KeyVault/vaults`.

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | Beschreibung |
   |---|---|
   | name | Der Name der Key Vault-Instanz. |
   | spn | Der Name (`name`) aus der Ausgabe des obigen Befehls `az ad sp create-for-rbac`. |
   | secret-permissions | Die Liste mit den Vorgängen, die für den benannten Prinzipal zulässig sein sollen. |

   > [!NOTE]
   > Das Prinzip der geringsten Rechte empfiehlt zwar, einer Ressource möglichst wenig Berechtigungen zu gewähren, für die Key Vault-Integration sind jedoch mindestens `get` und `list` erforderlich.

1. Speichern Sie ein Geheimnis in Ihrer neuen Key Vault-Instanz. Ein häufiger Anwendungsfall ist die Speicherung einer JDBC-Verbindungszeichenfolge. Beispiel:

   ```azurecli
   az keyvault secret set --name "connectionString" \
       --vault-name "contosokv" \
       --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Diese Tabelle enthält die Beschreibungen für die oben verwendeten Optionen.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `name` | Gibt den Namen Ihres Geheimnisses an. |
   | `value` | Gibt den Wert Ihres Geheimnisses an. |
   | `vault-name` | Gibt den oben verwendeten Namen Ihrer Key Vault-Instanz an. |

   Die Azure-Befehlszeilenschnittstelle zeigt die Ergebnisse der Geheimniserstellung an. Beispiel:

   ```output
   {
     "attributes": {
       "created": "2020-08-24T21:48:09+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2020-08-24T21:48:09+00:00"
     },
     "contentType": null,
     "id": "https://contosokv.vault.azure.net/secrets/connectionString/sample-id",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

Nachdem Sie nun eine Key Vault-Instanz erstellt und ein Geheimnis gespeichert haben, wird im nächsten Abschnitt veranschaulicht, wie Sie eine App mit Spring Initializr erstellen.

## <a name="create-the-app-with-spring-initializr"></a>Erstellen der App mit Spring Initializr

In diesem Abschnitt wird veranschaulicht, wie Sie Spring Initializr und `RestController` verwenden, um eine Spring Boot-Anwendung lokal zu erstellen und auszuführen.

1. Navigieren Sie zu <https://start.spring.io/>.
1. Wählen Sie die Optionen so aus, wie dies in der Abbildung unterhalb dieser Liste dargestellt ist.
   * **Project** (Projekt): **Maven Project** (Maven-Projekt)
   * **Language** (Sprache): **Java**
   * **Spring Boot**: **2.3.3**
   * **Group** (Gruppe): *com.contoso* (Sie können hier einen beliebigen gültigen Java-Paketnamen eingeben.)
   * **Artifact** (Artefakt): *keyvault* (Sie können hier einen beliebigen gültigen Java-Klassennamen eingeben.)
   * **Packaging** (Paketerstellung): **Jar**
   * **Java**: **11** (Sie können auch „8“ auswählen, aber für dieses Tutorial wurde „11“ verwendet.)
1. Wählen Sie **Add Dependencies...** (Abhängigkeiten hinzufügen...) aus.
1. Geben Sie im Textfeld den Text *Spring Web* ein, und drücken Sie STRG+EINGABE.
1. Geben Sie im Textfeld den Text *Azure Key Vault* ein, und drücken Sie die EINGABETASTE. Auf Ihrem Bildschirm sollte nun Folgendes angezeigt werden.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault/spring-initializr-choices.png" alt-text="Spring Initializr mit Auswahl der richtigen Optionen":::
1. Wählen Sie am unteren Rand der Seite die Option **Generate** (Generieren) aus.
1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter. In diesem Tutorial wird das Verzeichnis *keyvault* im aktuellen Basisverzeichnis des Benutzers verwendet. Die obigen Werte führen zur Datei *keyvault.zip* in diesem Verzeichnis.

Verwenden Sie die folgenden Schritte, um die Anwendung zu erkunden und lokal auszuführen.

1. Entzippen Sie die Datei *keyvault.zip*. Die Datei enthält das unten angegebene Layout. In diesem Tutorial werden das Verzeichnis *test* und sein Inhalt ignoriert.

   ```
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── contoso
       │   │           └── keyvault
       │   │               └── KeyvaultApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
   ```

1. Öffnen Sie die Datei *KeyvaultApplication.java* in einem Text-Editor. Bearbeiten Sie die Datei so, dass sie Folgendes enthält:

   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @SpringBootApplication
   @RestController
   public class KeyvaultApplication {

       public static void main(String[] args) {
           SpringApplication.run(KeyvaultApplication.class, args);
       }

       @GetMapping("get")
       public String get() {
           return connectionString;
       }

       private String connectionString = "defaultValue\n";

       public void run(String... varl) throws Exception {
           System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
       }

   }
   ```

   Die folgende Liste enthält einige Details zu diesem Code:

   * Die Klasse verfügt über die Anmerkung `@RestController`. Mit `@RestController` wird Spring Boot mitgeteilt, dass die Klasse auf RESTful-HTTP-Anforderungen antworten kann.
   * Die Klasse verfügt über eine Methode mit der Anmerkung `@GetMapping(get)`. Mit `@GetMapping` wird Spring Boot mitgeteilt, dass HTTP-Anforderungen über den Pfad `/get` an diese Methode gesendet werden sollen, damit die Antwort dieser Methode an den HTTP-Client zurückgegeben werden kann.
   * Die Klasse verfügt über die private Instanzvariable `connectionString`. Der Wert dieser Instanzvariablen wird von der Methode `get()` zurückgegeben.

1. Öffnen Sie ein Bash-Fenster, und navigieren Sie zum Verzeichnis *keyvault* auf der obersten Ebene, in dem die Datei *pom.xml* enthalten ist.

1. Geben Sie folgenden Befehl ein:

   ```bash
   mvn spring-boot:run
   ```

   Vom Befehl wird `Completed initialization` ausgegeben. Dies bedeutet, dass der Server bereit ist.

1. Geben Sie in einem separaten Bash-Fenster den folgenden Befehl ein:

   ```bash
   curl http://localhost:8080/get
   ```

   In der Ausgabe wird `defaultValue` angezeigt.

1. Beenden Sie den Prozess, der über `mvn spring-boot:run` ausgeführt wird. Sie können STRG+C eingeben oder den Befehl `jps` verwenden, um die PID des `Launcher`-Prozesses abzurufen und den Prozess zu beenden.

## <a name="add-key-vault-integration-to-the-app"></a>Hinzufügen der Key Vault-Integration zur App

In diesem Abschnitt wird veranschaulicht, wie Sie die Key Vault-Integration Ihrer lokal ausgeführten Anwendung hinzufügen, indem Sie die Spring Boot-Anwendung `KeyvaultApplication` ändern.

Ebenso wie für die Key Vault-Instanz das Externalisieren von Geheimnissen möglich ist, können Sie bei der Spring-Konfiguration die Konfigurationsdaten ebenfalls für den Code externalisieren. Die einfachste Form der Spring-Konfiguration ist die Datei *application.properties*. In einem Maven-Projekt befindet sich diese Datei unter *src/main/resources/application.properties*. Als Hilfe wird von Spring Initializr an diesem Speicherort eine Datei mit Nulllänge eingefügt. Verwenden Sie die folgenden Schritte, um dieser Datei die erforderliche Konfiguration hinzuzufügen.

1. Bearbeiten Sie die Datei *src/main/resources/application.properties* so, dass sie den folgenden Inhalt aufweist. Passen Sie hierbei die Werte so an, dass sie Ihrem Azure-Abonnement entsprechen.

   ```txt
   azure.keyvault.client-id=<your client ID>
   azure.keyvault.client-key=<your client key>
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=<your tenant ID>
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | azure.keyvault.client-id | Die `appId` im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.client-key | Das Kennwort (`password`) im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.enabled | Diese Konfiguration kann nützlich sein, wenn `enabled` oder `disabled` zur Bereitstellungszeit festgelegt werden sollen. Weitere Informationen zur Spring-Konfiguration finden Sie in der Spring-Dokumentation im Artikel zum Thema [Externalisierte Konfiguration](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config).
   | azure.keyvault.tenant-id | Der Mandant (`tenant`) im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.uri | Der für den obigen Befehl `az keyvault create` ausgegebene Wert. |

   Die vollständige Liste mit den Eigenschaften finden Sie unter [Azure Key Vault-Geheimnisse: Spring Boot Starter-Clientbibliothek für Java](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. Speichern Sie die Datei, und schließen Sie sie.

1. Öffnen Sie *src/main/java/com/contoso/keyvault/KeyvaultApplication.java* in einem Editor.

1. Fügen Sie die folgende `import` -Anweisung hinzu.

   ```java
   import org.springframework.beans.factory.annotation.Value;
   ```

1. Fügen Sie der Instanzvariablen `connectionString` die folgende Anmerkung hinzu:

   ```java
   @Value("${connectionString}")
   private String connectionString;
   ```

   Von der Key Vault-Integration wird das Spring-Element `PropertySource` bereitgestellt, in das die Werte der Key Vault-Instanz eingefügt werden. Weitere Details zur Implementierung finden Sie unter [Azure Key Vault-Geheimnisse: Spring Boot Starter-Clientbibliothek für Java](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. Öffnen Sie ein Bash-Fenster, und navigieren Sie zum Verzeichnis *keyvault* auf der obersten Ebene, in dem die Datei *pom.xml* enthalten ist.

1. Geben Sie folgenden Befehl ein:

   ```bash
   mvn clean package spring-boot:run
   ```

   Vom Befehl wird `initialization completed` ausgegeben. Dies bedeutet, dass der Server bereit ist.

1. Geben Sie in einem separaten Bash-Fenster den folgenden Befehl ein:

   ```bash
   curl http://localhost:8080/get
   ```

   In der Ausgabe wird `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` anstelle von `defaultValue` angezeigt.

1. Beenden Sie den Prozess, der über `mvn spring-boot:run` ausgeführt wird. Sie können STRG+C eingeben oder den Befehl `jps` verwenden, um die PID des `Launcher`-Prozesses abzurufen und den Prozess zu beenden.

## <a name="deploy-to-azure-app-service"></a>Bereitstellen in Azure App Service

Mit den folgenden Schritten wird veranschaulicht, wie Sie `KeyvaultApplication` in Azure App Service bereitstellen.

1. Öffnen Sie im Verzeichnis *keyvault* der obersten Ebene die Datei *pom.xml*.
1. Fügen Sie im Abschnitt `<build><plugins>` das Plug-In `azure-webapp-maven-plugin` hinzu, indem Sie den folgenden XML-Code einfügen.

   ```xml
   <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
     <version>1.12.0</version>
   </plugin>
   ```

   > [!NOTE]
   > Die Formatierung müssen Sie hierbei nicht beachten. Während dieses Prozesses wird per `azure-webapp-maven-plugin` die gesamte POM-Datei neu formatiert.

1. Speichern und schließen Sie die Datei *pom.xml*.
1. Verwenden Sie in der Befehlszeile den folgenden Befehl, um das Ziel `config` des neu hinzugefügten Plug-Ins aufzurufen.

   ```bash
   mvn azure-webapp:config
   ```

   Vom Maven-Plug-In werden einige Fragen gestellt, und die Datei *pom.xml* wird anhand der Antworten entsprechend geändert. Verwenden Sie die folgenden Werte:

   * Vergewissern Sie sich, dass Sie für **Subscription** (Abonnement) die gleiche Abonnement-ID mit der von Ihnen erstellten Key Vault-Instanz ausgewählt haben.
   * Unter **Web App** (Web-App) können Sie entweder eine vorhandene Web-App auswählen oder `<create>` angeben, um eine neue zu erstellen. Bei Verwendung einer vorhandenen Web-App gelangen Sie direkt zum letzten **Bestätigungsschritt**.
   * Stellen Sie sicher, dass unter **OS** die Option **linux** ausgewählt ist.
   * Vergewissern Sie sich, dass unter **javaVersion** die Java-Version angegeben ist, die Sie in Spring Initializr ausgewählt haben. In diesem Tutorial wird Version 11 verwendet.
   * Übernehmen Sie für die restlichen Fragen jeweils die Standardwerte.
   * Wenn Sie zur Bestätigung aufgefordert werden, können Sie „Y“ auswählen, um fortzufahren. (Bei Auswahl von „N“ beginnt die Beantwortung der Fragen erneut.) Nachdem die Ausführung des Plug-Ins abgeschlossen ist, können Sie die POM-Datei bearbeiten.

1. Öffnen Sie als Nächstes die geänderte Datei *pom.xml* in einem Editor. Der Inhalt der Datei sollte dem folgenden XML-Code ähneln. Ersetzen Sie die folgenden Platzhalter durch die angegebenen Werte, falls Sie die Werte nicht bereits im vorherigen Schritt eingefügt haben.

   * `YOUR_SUBSCRIPTION_ID`: Dieser Platzhalter steht für die weiter oben angegebene ID.
   * `YOUR_RESOURCE_GROUP_NAME`: Ersetzen Sie diesen Platzhalter durch den Wert, den Sie beim Erstellen der Key Vault-Instanz angegeben haben.
   * `YOUR_APP_NAME`: Ersetzen Sie diesen Platzhalter durch einen vertraulichen Wert, der für Ihr Abonnement eindeutig ist.
   * `YOUR_REGION`: Ersetzen Sie diesen Platzhalter durch den Wert, den Sie beim Erstellen der Key Vault-Instanz angegeben haben.
   * `APP_SETTINGS`: Kopieren Sie das angegebene `<appSettings>`-Element aus dem Beispiel, und fügen Sie es an diesem Ort in Ihrer Datei *pom.xml* ein. Diese Einstellung bewirkt, dass der Server an TCP-Port 80 lauscht.

   ```xml
   <plugins>
     <plugin>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-maven-plugin</artifactId>
     </plugin>
     <plugin>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-webapp-maven-plugin</artifactId>
       <version>1.12.0</version>
       <configuration>
         <schemaVersion>V2</schemaVersion>
         <subscriptionId>YOUR_SUBSCRIPTION_ID</subscriptionId>
         <resourceGroup>YOUR_RESOURCE_GROUP_NAME</resourceGroup>
         <appName>YOUR_APP_NAME</appName>
         <pricingTier>P1v2</pricingTier>
         <region>YOUR_REGION</region>
         <runtime>
           <os>linux</os>
           <javaVersion>java 11</javaVersion>
           <webContainer>Java SE</webContainer>
         </runtime>
         <!-- start of APP_SETTINGS -->
         <appSettings>
           <property>
             <name>JAVA_OPTS</name>
             <value>-Dserver.port=80</value>
           </property>
         </appSettings>
         <!-- end of APP_SETTINGS -->
         <deployment>
           <resources>
             <resource>
               <directory>${project.basedir}/target</directory>
               <includes>
                 <include>*.jar</include>
               </includes>
             </resource>
           </resources>
         </deployment>
       </configuration>
     </plugin>
   </plugins>
   ```

1. Speichern und schließen Sie die POM-Datei.
1. Verwenden Sie den folgenden Befehl, um die App in Azure App Service bereitzustellen.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

   Die Ausführung dieses Befehls kann mehrere Minuten dauern. Der Grund hierfür können viele Faktoren sein, die außerhalb Ihrer Kontrolle liegen. Wenn eine Ausgabe angezeigt wird, die dem folgenden Beispiel ähnelt, wissen Sie, dass die Bereitstellung Ihrer App erfolgreich war.

   ```output
   [INFO] Deploying the zip package contosokeyvault-22b7c1a3-b41b-4082-a9f0-9339723fa36a11893059035499017844.zip...
   [INFO] Successfully deployed the artifact to https://contosokeyvault.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:45 min
   [INFO] Finished at: 2020-08-16T22:47:48-04:00
   [INFO] ------------------------------------------------------------------------
   ```

1. Warten Sie drei bis fünf Minuten, bis die Bereitstellung vollständig abgeschlossen ist. Anschließend können Sie mit einem ähnlichen `curl`-Befehl wie oben auf die Bereitstellung zugreifen. Verwenden Sie nun aber den Hostnamen, der in der Ausgabe von `BUILD SUCCESS` angezeigt wird. Im folgenden Beispiel wird `contosokeyvault` wie in der obigen Ausgabe verwendet.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   ```

   Mit der folgenden Ausgabe wird die erfolgreiche Durchführung angezeigt.

   ```output
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Sie haben Ihre App nun in Azure App Service bereitgestellt.

## <a name="redeploy-to-azure-app-service-and-use-managed-identities-for-azure-resources"></a>Erneutes Bereitstellen in Azure App Service und Verwenden von verwalteten Identitäten für Azure-Ressourcen

In diesem Abschnitt wird beschrieben, wie Sie eine Identität der Azure-Ressource für die App zuordnen. Diese Zuordnung ist erforderlich, damit Azure Sicherheitsmaßnahmen anwenden und den Zugriff nachverfolgen kann.

Eines der grundlegenden Cloud Computing-Prinzipien lautet, dass Sie nur für die Ressourcen zahlen, die Sie nutzen. Diese präzise Nachverfolgung von Ressourcen ist nur möglich, wenn jeder Ressource eine Identität zugeordnet wird. Azure App Service und Azure Key Vault sind zwei von vielen Azure-Diensten, für die verwaltete Identitäten für Azure-Ressourcen genutzt werden. Weitere Informationen zu dieser wichtigen Technologie finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name des Diensts, der bisher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

Verwenden Sie die folgenden Schritte, um die verwaltete Identität für die Azure App Service-App zu erstellen und dann zuzulassen, dass diese Identität auf die Key Vault-Instanz zugreift.

1. Erstellen Sie eine verwaltete Identität für die App Service-App. Ersetzen Sie die Platzhalter `<your resource group name>` und `<your app name>` durch die Werte der Elemente `<resourceGroup>` und `<appName>` aus Ihrer Datei *pom.xml*.

   ```azurecli
   az webapp identity assign --resource-group <your resource group name> --name <your app name>
   ```

   Die Ausgabe entspricht etwa folgendem Beispiel: Notieren Sie sich den Wert von `principalId` für den nächsten Schritt.

   ```json
   {
     "principalId": "2r7s6r00-92o9-4sq7-n10r-popq294ssr8s",
     "tenantId": "72s988os-86s1-41ns-91no-2q7pq011qo47",
     "type": "SystemAssigned",
     "userAssignedIdentities": null
   }
   ```

1. Ändern Sie die Datei *application.properties* so, dass sie die Namen der verwalteten Identität für die im vorherigen Schritt erstellten Azure-Ressourcen enthält.

   1. Entfernen Sie den `azure.keyvault.client-key`.
   1. Aktualisieren Sie die `azure.keyvault.client-id` so, dass sie den Wert der `principalId` aus dem vorherigen Schritt enthält. Die fertige Datei sollte jetzt wie im folgenden Beispiel aussehen.

   ```properties
   azure.keyvault.client-id=56rqs994-0o66-43o3-9roo-8e3534d0cb23
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91ab-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

1. Konfigurieren Sie die Key Vault-Instanz, um `get`- und `list`-Vorgänge für die verwaltete Identität zuzulassen. Der Wert der `object-id` ist die `principalId` aus der vorherigen Ausgabe.

   ```azurecli
   az keyvault set-policy \
       --name <your Key Vault name> \
       --object-id <your principal ID> \
       --secret-permissions get list
   ```

   Die Ausgabe ist ein JSON-Objekt mit vielen Informationen zur Key Vault-Instanz. Es verfügt über den Eintrag `type` mit dem Wert `Microsoft.KeyVault/vaults`.

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | Beschreibung |
   |---|---|
   | name | Der Name der Key Vault-Instanz. |
   | object-id | Die `principalId` aus dem vorherigen Befehl. |
   | secret-permissions | Die Liste mit den Vorgängen, die für den benannten Prinzipal zulässig sein sollen. |

1. Verpacken Sie die Anwendung, und stellen Sie sie erneut bereit.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Warten Sie noch einige Minuten, damit die Bereitstellung fertig eingerichtet werden kann. Anschließend können Sie mit einem ähnlichen `curl`-Befehl wie oben mit der Bereitstellung Kontakt aufnehmen. Verwenden Sie nun aber den Hostnamen, der in der Ausgabe von `BUILD SUCCESS` angezeigt wird. Im folgenden Beispiel wird `contosokeyvault` wie in der Ausgabe von `BUILD SUCCESS` aus dem vorherigen Abschnitt verwendet.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   ```

   Mit der folgenden Ausgabe wird die erfolgreiche Durchführung angezeigt.

   ```output
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Anstatt `defaultValue` zurückzugeben, ruft die App `connectionString` aus der Key Vault-Instanz ab.

## <a name="deploy-to-azure-spring-cloud"></a>In Azure Spring Cloud bereitstellen

In diesem Abschnitt stellen Sie die App in Azure Spring Cloud bereit.

Azure Spring Cloud ist eine vollständig verwaltete Plattform für die Bereitstellung und Ausführung Ihrer Spring Boot-Anwendungen in Azure. Eine Übersicht über Azure Spring Cloud finden Sie unter [Was ist Azure Spring Cloud?](/azure/spring-cloud/spring-cloud-overview).

In diesem Abschnitt werden die Spring Boot-App und Ihre zuvor erstellte Key Vault-Instanz mit einer neuen Instanz von Azure Spring Cloud verwendet.

In den folgenden Schritten wird veranschaulicht, wie Sie eine Azure Spring Cloud-Ressource erstellen und die App dafür bereitstellen. Stellen Sie sicher, dass Sie die Azure CLI-Erweiterung für Azure Spring Cloud installiert haben, wie dies unter [Voraussetzungen](#prerequisites) beschrieben ist.

1. Wählen Sie einen Namen für die Dienstinstanz aus. Für die Nutzung von Azure Spring Cloud in Ihrem Azure-Abonnement müssen Sie eine Azure-Ressource vom Typ „Azure Spring Cloud“ erstellen. Wie bei allen anderen Azure-Ressourcen auch, muss die Dienstinstanz innerhalb einer Ressourcengruppe bleiben. Verwenden Sie die bereits erstellte Ressourcengruppe zum Speichern der Dienstinstanz, und wählen Sie einen Namen für Ihre Azure Spring Cloud-Instanz aus. Erstellen Sie die Dienstinstanz mit dem folgenden Befehl.

   ```bash
   az spring-cloud create --resource-group <your resource group name> --name <your Azure Spring Cloud instance name>
   ```

   Es dauert mehrere Minuten, bis dieser Befehl abgeschlossen ist.

1. Erstellen Sie innerhalb des Diensts eine Spring Cloud-App.

   ```bash
   az spring-cloud app create \
       --resource-group <your resource group name> \
       --service <your Azure Spring Cloud instance name> \
       --name <your app name> \
       --assign-identity \
       --is-public true \
       --runtime-version Java_11 \
   ```

   Diese Tabelle enthält die Beschreibungen für die oben verwendeten Optionen.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | resource-group | Der Name der Ressourcengruppe, in der Sie die vorhandene Dienstinstanz erstellt haben. |
   | Dienst | Der Name des vorhandenen Diensts. |
   | Name | Der Name der App. |
   | assign-identity | Bewirkt, dass der Dienst eine Identität für verwaltete Identitäten für Azure-Ressourcen erstellt. |
   | is-public | Weist dem Dienst den Namen einer öffentlichen DNS-Domäne zu. |
   | runtime-version | Die Java Runtime-Version. Der Wert muss mit dem Wert übereinstimmen, den Sie oben in Spring Initializr ausgewählt haben. |

   Informationen zum Unterschied zwischen einem *Dienst* und einer *App* finden Sie unter [Grundlegendes zu Apps und Bereitstellungen in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

1. Verwenden Sie den folgenden Befehl, um die verwaltete Identität für die Azure-Ressource abzurufen. Nutzen Sie diese dann, um die vorhandene Key Vault-Instanz so zu konfigurieren, dass der Zugriff aus dieser App zugelassen ist.

   ```bash
   SERVICE_IDENTITY=$(az spring-cloud app show --resource-group "contosorg" --name "contosoascsapp" --service "contososvc" | jq -r '.identity.principalId')
   az keyvault set-policy \
       --name <your Key Vault name> \
       --object-id <the value of the environment variable SERVICE_IDENTITY> \
       --secret-permissions set get list
   ```

1. Da die vorhandene Spring Boot-App bereits über die Datei *application.properties* mit der erforderlichen Konfiguration verfügt, können wir diese App mit dem folgenden Befehl direkt in Spring Cloud bereitstellen. Führen Sie den Befehl in dem Verzeichnis aus, in dem die POM-Datei enthalten ist.

   ```bash
   az spring-cloud app deploy \
       --resource-group <your resource group name> \
       --name <your Spring Cloud app name> \
       --jar-path target/keyvault-0.0.1-SNAPSHOT.jar \
       --service <your Azure Spring Cloud instance name>
   ```

   Mit diesem Befehl wird unter dem Dienst innerhalb der App eine *Bereitstellung* erstellt. Weitere Informationen zu den Grundlagen von Dienstinstanzen, Apps und Bereitstellungen finden Sie unter [Grundlegendes zu Apps und Bereitstellungen in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

   Wenn die Bereitstellung nicht erfolgreich ist, sollten Sie die Protokolle für die Problembehandlung wie unter [Konfigurieren von Anwendungsprotokollen](https://aka.ms/azure-spring-cloud-configure-logs) beschrieben konfigurieren. Die Protokolle verfügen wahrscheinlich über nützliche Informationen zum Diagnostizieren und Beheben des Problems.

1. Nachdem die App erfolgreich bereitgestellt wurde, können Sie `curl` verwenden, um die korrekte Funktionsweise der Key Vault-Integration zu überprüfen. Da Sie `--is-public` angegeben haben, lautet die Standard-URL für Ihren Dienst `https://<your Azure Spring Cloud instance name>-<your app name>.azuremicroservices.io/`. Mit dem folgenden Befehl wird ein Beispiel veranschaulicht, bei dem der Name der Dienstinstanz `contososvc` und der Name der App `contosoascsapp` lautet. In der URL wird der Wert der Anmerkung `@GetMapping` angefügt.

   ```bash
   curl https://contososvc-contosoascsapp.azuremicroservices.io/get
   ```

   In der Ausgabe wird `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` angezeigt.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie mit Spring Initializr eine neue Java-Webanwendung erstellt. Sie haben eine Azure Key Vault-Instanz für die Speicherung von vertraulichen Informationen erstellt und Ihre Anwendung dann so konfiguriert, dass Informationen aus Ihrer Key Vault-Instanz abgerufen werden. Nach einem lokalen Test haben Sie die App für Azure App Service und Azure Spring Cloud bereitgestellt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie mithilfe des folgenden Befehls löschen:

```azurecli
az group delete --name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im nächsten Artikel der Dokumentation zu Spring in Azure.

> [!div class="nextstepaction"]
> [Verwenden von Spring Boot-Starters für Azure Service Bus JMS](configure-spring-boot-starter-java-app-with-azure-service-bus.md)
