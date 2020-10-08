---
title: 'Tutorial: Einlesen eines Geheimnisses aus Azure Key Vault in eine Spring Boot-Anwendung'
description: 'Tutorial: Einlesen eines Geheimnisses aus Azure Key Vault in eine Spring Boot-Anwendung'
services: key-vault
documentationcenter: java
ms.date: 08/15/2020
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: e06d09d4f44366ba995ecaa401df901dc6270c6d
ms.sourcegitcommit: f80537193d3e22eb24cce4a0a5464a996d1e63eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409972"
---
# <a name="tutorial-reading-a-secret-from-azure-key-vault-in-a-spring-boot-application"></a>Tutorial: Einlesen eines Geheimnisses aus Azure Key Vault in eine Spring Boot-Anwendung

Für Spring Boot-Anwendungen werden vertrauliche Informationen, z. B. Benutzernamen und Kennwörter, externalisiert.  Die Externalisierung von vertraulichen Informationen ermöglicht eine bessere Wartbarkeit und Prüfbarkeit und eine höhere Sicherheit.  Das Speichern von Geheimnissen außerhalb des Codes ist besser als das Hartcodieren der Informationen oder die Inlineverwendung während des Buildvorgangs.

In diesem Tutorial wird beschrieben, wie Sie eine Spring Boot-App erstellen, mit der ein Wert aus Azure Key Vault eingelesen wird, und wie Sie die App dann in Azure App Service und Azure Spring Cloud bereitstellen.

> [!div class="checklist"]
> * Erstellen der Azure Key Vault-Instanz und Speichern eines Geheimnisses
> * Erstellen der App mit Spring Initializr
> * Hinzufügen der Key Vault-Integration zur App
> * Bereitstellen in Azure App Service
> * Erneutes Bereitstellen in Azure App Service mit verwalteten Identitäten für Azure-Ressourcen
> * In Azure Spring Cloud bereitstellen

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Abonnement.
  * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
* [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) und der Azure Spring Cloud-Erweiterung mit dem folgenden Befehl: `az extension add --name spring-cloud`
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher
* `curl`-Befehl.  Auf den meisten UNIX-ähnlichen Betriebssystemen ist dieser Befehl vorinstalliert.  Betriebssystemspezifische Clients sind auf der [offiziellen cURL-Website](https://curl.haxx.se/) verfügbar.
* `jq`-Befehl. Auf den meisten UNIX-ähnlichen Betriebssystemen ist dieser Befehl vorinstalliert.  Betriebssystemspezifische Clients sind auf der [offiziellen jq-Website](https://stedolan.github.io/jq/) verfügbar.

## <a name="create-a-new-azure-key-vault"></a>Erstellen eines neuen Azure Key Vault-Schlüsseltresors

In den folgenden Abschnitten wird veranschaulicht, wie Sie sich bei Azure anmelden und eine Azure Key Vault-Instanz erstellen.

### <a name="sign-into-azure-and-set-your-subscription"></a>Anmelden bei Azure und Festlegen Ihres Abonnements

Führen Sie zunächst die folgenden Schritte aus, um die Authentifizierung mit der Azure CLI durchzuführen.

1. Optional können Sie sich anmelden und einige Authentifizierungsdateien löschen, um vorhandene Anmeldeinformationen zu entfernen:

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

   Azure gibt eine Liste mit Ihren Abonnements zurück. Kopieren Sie die `id` für das gewünschte Abonnement, z. B.:

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

### <a name="create-a-service-principal-for-use-in-by-your-app"></a>Erstellen eines Dienstprinzipals für die Verwendung in Ihrer App

*Dienstprinzipale* von Azure AD ermöglichen den Zugriff auf Azure-Ressourcen innerhalb Ihres Abonnements. Sie können sich einen Dienstprinzipal wie eine Benutzeridentität für einen Dienst vorstellen.  Ein „Dienst“ ist eine beliebige Anwendung, ein Dienst oder eine Plattform (oder auch die in diesem Tutorial erstellte Beispiel-App), mit der bzw. dem auf Azure-Ressourcen zugegriffen werden muss. Sie haben die Möglichkeit, einen Dienstprinzipal mit Zugriffsrechten so zu konfigurieren, dass diese nur für die von Ihnen angegebenen Ressourcen gelten. Anschließend legen Sie für Ihre Anwendung bzw. Ihren Dienst fest, dass diese bzw. dieser die Anmeldeinformationen des Dienstprinzipals verwendet, um auf diese Ressourcen zuzugreifen.

Erstellen Sie mit diesem Befehl einen Dienstprinzipal.

```azurecli
az ad sp create-for-rbac --name contososp
```

Der Wert der Option `name` muss in Ihrem Abonnement eindeutig sein.  Speichern Sie die Werte, die vom Befehl zurückgegeben werden, damit Sie sie zu einem späteren Zeitpunkt dieses Tutorials verwenden können.  Der zurückgegebene JSON-Code sieht in etwa wie folgt aus:

```json
{
  "appId": "8r7o486s-o5q9-450s-8457-pr26p86n0497",
  "displayName": "ejbcontososp",
  "name": "http://ejbcontososp",
  "password": "4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2",
  "tenant": "72s988os-86s1-41ns-91no-2d7cd011db47"
}
```

### <a name="create-the-key-vault-instance"></a>Erstellen der Key Vault-Instanz

Mit dem folgenden Verfahren wird die Key Vault-Instanz erstellt und initialisiert.

1. Bestimmen Sie, in welcher Azure-Region Ihre Ressourcen enthalten sein sollen.
   1. Sie können die [Liste der Regionen und Standorte](https://azure.microsoft.com/regions/)überprüfen.
   1. Sie können den Befehl `az account list-locations` verwenden, um den richtigen Namen (`Name`) für Ihre gewählte Region zu ermitteln.

      ```azurecli
      az account list-locations -o table
      ```

      In diesem Tutorial verwenden wir `eastus`.
1. Erstellen Sie eine Ressourcengruppe, in der die Key Vault-Instanz und die App Service-App enthalten sein sollen.  Der Wert muss innerhalb des Abonnements eindeutig sein.  In diesem Tutorial verwenden wir `contosorg`.

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
   | `query` | Entnehmen Sie den Key Vault-URI aus der Antwort.  Sie benötigen den URI zum Durcharbeiten dieses Tutorials. |
   | `sku` | Gibt die [Option für die Key Vault-SKU](/cli/azure/keyvault) an. |

   Die Azure CLI zeigt den URI für die Key Vault-Instanz an, die Sie später verwenden, z. B.:

   ```output
   "https://contosokv.vault.azure.net/"
   ```

1. Konfigurieren Sie die Key Vault-Instanz, um `get`- und `list`-Vorgänge für diese verwaltete Identität zuzulassen.  Der Wert der `object-id` ist die `appId` aus dem obigen Befehl `az ad sp create-for-rbac`.

   ```azurecli
   az keyvault set-policy --name contosokv --spn http://ejbcontososp --secret-permissions get list
   ```

   Die Ausgabe ist ein JSON-Objekt mit vielen Informationen zur Key Vault-Instanz.  Es verfügt über den Eintrag `type` mit dem Wert `Microsoft.KeyVault/vaults`.

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | name | Der Name der Key Vault-Instanz. |
   | spn | Der Name (`name`) aus der Ausgabe des obigen Befehls `az ad sp create-for-rbac`. |
   | secret-permissions | Die Liste mit den Vorgängen, die für den benannten Prinzipal zulässig sein sollen. |

    > [!NOTE]
    > Das Prinzip der geringsten Rechte empfiehlt zwar, einer Ressource möglichst wenig Berechtigungen zu gewähren, für die Key Vault-Integration sind jedoch mindestens `get` und `list` erforderlich.

1. Speichern Sie ein Geheimnis in Ihrer neuen Key Vault-Instanz.  Ein häufiger Anwendungsfall ist die Speicherung einer JDBC-Verbindungszeichenfolge.  Beispiel:

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

   ```json
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
     "id": "https://contosokv.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
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
   1. **Project** (Projekt): `Maven Project`
   1. **Language** (Sprache): `Java`
   1. **Spring Boot**: `2.3.3`
   1. **Gruppe**: `com.contoso` (Sie können hier einen beliebigen gültigen Java-Paketnamen eingeben.)
   1. **Artifact** (Artefakt): *keyvault* (Sie können hier einen beliebigen gültigen Java-Klassennamen eingeben.)
   1. **Packaging** (Verpackung): `Jar`
   1. **Java**: `11` (Sie können auch „8“ auswählen, aber für dieses Tutorial wurde „11“ verwendet.)
1. Wählen Sie **Add Dependencies...** (Abhängigkeiten hinzufügen...) aus.
1. Geben Sie im Textfeld `Spring Web` ein, und drücken Sie STRG+EINGABE.
1. Geben Sie im Textfeld `Azure Key Vault` ein, und drücken Sie die EINGABETASTE.  Auf Ihrem Bildschirm sollte nun Folgendes angezeigt werden.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault/spring-initializr-choices.png" alt-text="Spring Initializr mit Auswahl der richtigen Optionen":::
1. Wählen Sie am unteren Rand der Seite die Option **Generate** (Generieren) aus.
1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.  Für diese Beschreibung verwenden wir das Verzeichnis *keyvault* im Basisverzeichnis des aktuellen Benutzers.  Die obigen Werte führen zur Datei *keyvault.zip* in diesem Verzeichnis.

Führen Sie die folgenden Schritte aus, um die Anwendung zu untersuchen und lokal auszuführen.

1. Entzippen Sie die Datei *keyvault.zip*.  Die Datei enthält das unten angegebene Layout.  In diesem Tutorial ignorieren wir das Verzeichnis *test* und dessen Inhalt.

   ```bash
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── contoso
       │   │           └── keyvault
       │   │               └── KeyvaultApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
   ```

1. Öffnen Sie die Datei *KeyvaultApplication.java* in einem Text-Editor.  Ändern Sie die Datei so, dass Sie wie folgt aussieht.

   * Die Klasse verfügt über die Anmerkung `@RestController`.  Mit `@RestController` wird Spring Boot mitgeteilt, dass die Klasse auf RESTful-HTTP-Anforderungen antworten kann.
   * Die Klasse verfügt über eine Methode mit der Anmerkung `@GetMapping(get)`.  Mit `@GetMapping` wird Spring Boot mitgeteilt, dass HTTP-Anforderungen über den Pfad `/get` an diese Methode gesendet werden sollen, damit die Antwort dieser Methode an den HTTP-Client zurückgegeben werden kann.
   * Die Klasse verfügt über die private Instanzvariable `connectionString`.  Der Wert dieser Instanzvariablen wird von der Methode `get()` zurückgegeben.

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

1. Geben Sie im Verzeichnis *keyvault* der obersten Ebene, in dem die Datei *pom.xml* enthalten ist, `mvn spring-boot:run` ein.  
1. Die Meldung **Completed initialization** (Initialisierung abgeschlossen) in der Befehlsausgabe bedeutet, dass der Server betriebsbereit ist.  Geben Sie in einem separaten Shellfenster den folgenden Befehl ein.

   ```bash
   $ curl http://localhost:8080/get
   ```

   In der Ausgabe wird `defaultValue` angezeigt.

1. Beenden Sie den Prozess, der über `mvn spring-boot:run` ausgeführt wird.  Sie können STRG+C eingeben oder den Befehl `jps` verwenden, um die PID des `Launcher`-Prozesses zu erhalten und den Prozess zu beenden.

Im nächsten Abschnitt wird veranschaulicht, wie Sie die Key Vault-Integration Ihrer lokal ausgeführten Anwendung hinzufügen.

## <a name="add-key-vault-integration-to-the-app"></a>Hinzufügen der Key Vault-Integration zur App

Die folgenden Schritte verdeutlichen die erforderlichen Änderungen, die an der Spring Boot-Anwendung `KeyvaultApplication` vorgenommen werden müssen.

Ebenso wie für die Key Vault-Instanz das Externalisieren von Geheimnissen möglich ist, können Sie bei der Spring-Konfiguration die Konfigurationsdaten ebenfalls für den Code externalisieren.  Die einfachste Form der Spring-Konfiguration ist die Datei *application.properties*.  In einem Maven-Projekt befindet sich diese Datei unter *src/main/resources/application.properties*.  Als Hilfe wird von Spring Initializr an diesem Speicherort eine Datei mit Nulllänge eingefügt.

Führen Sie die folgenden Schritte aus, um dieser Datei die erforderliche Konfiguration hinzuzufügen.

1. Öffnen Sie die Datei *src/main/resources/application.properties* in einem Editor, und sorgen Sie dafür, dass sie den folgenden Inhalt aufweist. Passen Sie hierbei die Werte für Ihr Azure-Abonnement entsprechend an.

   ```txt
   azure.keyvault.client-id=685on005-ns8q-4o04-8s16-n7os38o2ro5n
   azure.keyvault.client-key=4bt.lCKJKlbYLn_3XF~wWtUwyHU0jKggu2
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91no-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/
   ```

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | azure.keyvault.client-id | Die `appId` im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.client-key | Das Kennwort (`password`) im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.enabled | Diese Konfiguration kann nützlich sein, wenn `enabled` oder `disabled` zur Bereitstellungszeit festgelegt werden sollen.  Weitere Informationen zur Spring-Konfiguration finden Sie in der [Spring-Dokumentation](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/#boot-features-external-config).
   | azure.keyvault.tenant-id | Der Mandant (`tenant`) im zurückgegebenen JSON-Code von `az ad sp create-for-rbac`.|
   | azure.keyvault.uri | Der für den obigen Befehl `az keyvault create` ausgegebene Wert. |

   Die vollständige Liste mit den verfügbaren Eigenschaften ist in der [Eigenschaftsreferenz](https://aka.ms/azure-spring-boot-starter-keyvault-secrets) dokumentiert.

1. Speichern Sie die Datei, und schließen Sie sie.

Nehmen Sie eine einfache Änderung an der Datei mit dem Namen *KeyvaultApplication.java* (bzw. je nach Ihrem Klassennamen) vor.

1. Öffnen Sie *src/main/java/com/contoso/keyvault/KeyvaultApplication.java* in einem Text-Editor.
1. Fügen Sie diesen Import hinzu.

   ```java
   import org.springframework.beans.factory.annotation.Value;
   ```

1. Fügen Sie der Instanzvariablen `connectionString` eine Anmerkung hinzu.

   ```java
   @Value("${connectionString}")
   private String connectionString;  
   ```

   Von der Key Vault-Integration wird das Spring-Element `PropertySource` bereitgestellt, in das die Werte der Key Vault-Instanz eingefügt werden.  Weitere Informationen zur Implementierung finden Sie in der [Referenzdokumentation](https://aka.ms/azure-spring-boot-starter-keyvault-secrets).

1. Geben Sie im Verzeichnis *keyvault* der obersten Ebene, in dem die Datei *pom.xml* enthalten ist, `mvn clean package spring-boot:run` ein.  
1. Die Meldung **initialization completed** (Initialisierung abgeschlossen) in der Befehlsausgabe bedeutet, dass der Server betriebsbereit ist.  Geben Sie in einem separaten Shellfenster den folgenden Befehl ein.

   ```bash
   $ curl http://localhost:8080/get
   ```

   In der Ausgabe wird `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` anstelle von `defaultValue` angezeigt.

1. Beenden Sie den Prozess, der über `mvn spring-boot:run` ausgeführt wird.  Sie können STRG+C eingeben oder den Befehl `jps` verwenden, um die PID des `Launcher`-Prozesses zu erhalten und den Prozess zu beenden.


Im nächsten Abschnitt wird veranschaulicht, wie Sie diese App in Azure App Service bereitstellen.

## <a name="deploy-to-azure-app-service"></a>Bereitstellen in Azure App Service

Führen Sie die Schritte in diesem Abschnitt aus, um `KeyvaultApplication` für Azure App Service bereitzustellen.

### <a name="use-the-azure-maven-web-app-plugin-to-deploy-the-application-to-azure-app-service"></a>Verwenden des Azure-Maven-Web-App-Plug-Ins zum Bereitstellen der Anwendung in Azure App Service

Führen Sie die folgenden Schritte aus, um Ihre POM-Instanz für die Bereitstellung von `KeyvaultApplication` in Azure App Service vorzubereiten.

1. Öffnen Sie im Verzeichnis *keyvault* der obersten Ebene die Datei *pom.xml*.
1. Fügen Sie im Abschnitt `<build><plugins>` das `azure-webapp-maven-plugin` hinzu, indem Sie den folgenden XML-Code einfügen.

   ```xml
    <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
     <version>1.11.0</version>
    </plugin>
   ```

   > [!NOTE]
   > Die Formatierung müssen Sie hierbei nicht beachten.  Während dieses Prozesses wird per `azure-webapp-maven-plugin` die gesamte POM-Datei neu formatiert.

1. Speichern und schließen Sie die Datei *pom.xml*.
1. Rufen Sie in der Befehlszeile das Ziel `config` des neu hinzugefügten Plug-Ins auf.  Vom Maven-Plug-In werden einige Fragen gestellt, und die Datei *pom.xml* wird anhand der Antworten geändert.  Sie nehmen für die POM-Datei noch weitere Änderungen vor.

   ```bash
   mvn azure-webapp:config
   ```

1. Vergewissern Sie sich, dass Sie für das Abonnement (`Subscription`) die gleiche Abonnement-ID mit der von Ihnen erstellten Key Vault-Instanz ausgewählt haben.
1. Für die Web-App (`Web App`) können Sie entweder eine vorhandene Web-App oder aber `<create>` auswählen, um eine neue Web-App zu erstellen. Bei Verwendung einer vorhandenen Web-App gelangen Sie direkt zum letzten **Bestätigungsschritt**.
1. Für das Betriebssystem (`OS`) muss `linux` ausgewählt werden.
1. Stellen Sie für `javaVersion` sicher, dass die Java-Version angegeben ist, die Sie in Spring Initializr ausgewählt haben.  Da wir oben `11` ausgewählt haben, wählen wir hier ebenfalls „11“ aus.
1. Übernehmen Sie für die restlichen Fragen jeweils die Standardwerte.
1. Wenn Sie zur Bestätigung aufgefordert werden, können Sie „Y“ auswählen, um fortzufahren. (Bei Auswahl von „N“ beginnt die Beantwortung der Fragen erneut.)  Nachdem die Ausführung des Plug-Ins abgeschlossen ist, können Sie die POM-Datei bearbeiten.

Führen Sie die folgenden Schritte aus, um die weiteren erforderlichen Änderungen an der POM-Datei vorzunehmen.

1. Öffnen Sie im Verzeichnis *keyvault* der obersten Ebene die Datei *pom.xml*.
1. Suchen Sie im Abschnitt <plugins> nach dem Eintrag `azure-webapp-maven-plugin`.
1. Ändern Sie jeweils den Wert für `<resourceGroup>`, `<appName>` und `<region>`.  
   1. Legen Sie den Wert für `<resourceGroup>` auf den Wert fest, den Sie beim Erstellen der Key Vault-Instanz angegeben haben.
   1. Wählen Sie für `<appName>` einen sinnvollen Wert aus, der innerhalb Ihres Abonnements eindeutig ist.
   1. Legen Sie den Wert für `<region>` auf den Wert fest, den Sie beim Erstellen der Key Vault-Instanz angegeben haben.
1. Fügen Sie das Element `<appSettings>` ein, mit dem erreicht wird, dass der Server an TCP-Port 80 lauscht.
1. Als Nächstes wird das vollständige geänderte `<plugin>` für `azure-webapp-maven-plugin` angezeigt.  Die Werte, die Sie ändern müssen, sind mit `*` gekennzeichnet.

   ```xml
   <plugins> 
     <plugin> 
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-maven-plugin</artifactId> 
     </plugin>  
     <plugin> 
       <groupId>com.microsoft.azure</groupId>  
       <artifactId>azure-webapp-maven-plugin</artifactId>  
       <version>1.11.0</version>  
       <configuration>
         <schemaVersion>V2</schemaVersion>
         *<subscriptionId>********-****-****-****-************</subscriptionId>
         *<resourceGroup>contosorg</resourceGroup>
         *<appName>contosokeyvault</appName>
         <pricingTier>P1v2</pricingTier>
         *<region>eastus</region>
         <runtime>
           <os>linux</os>
           <javaVersion>java11</javaVersion>
           <webContainer>java11</webContainer>
         </runtime>
         *<!-- Begin of App Settings  -->
         *<appSettings>
         *  <property>
         *    <name>JAVA_OPTS</name>
         *    <value>-Dserver.port=80</value>
         *  </property>
         *</appSettings>
         *<!-- End of App Settings  -->          
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
1. Stellen Sie die App in Azure App Service bereit.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Die Ausführung des Befehls kann mehrere Minuten dauern. Der Grund hierfür können viele Faktoren sein, die außerhalb Ihrer Kontrolle liegen.  Wenn eine Ausgabe dieser Art angezeigt wird, wissen Sie, dass die Bereitstellung Ihrer App erfolgreich war.

   ```bash
   [INFO] Deploying the zip package contosokeyvault-22b7c1a3-b41b-4082-a9f0-9339723fa36a11893059035499017844.zip...
   [INFO] Successfully deployed the artifact to https://contosokeyvault.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:45 min
   [INFO] Finished at: 2020-08-16T22:47:48-04:00
   [INFO] ------------------------------------------------------------------------
   ```

1. Warten Sie drei bis fünf Minuten, bis die Bereitstellung vollständig abgeschlossen ist.  Anschließend können Sie auf die Bereitstellung mit einem `curl`-Befehl wie oben gezeigt zugreifen. Hierbei verwenden Sie nun aber den Hostnamen, der in der Ausgabe von `BUILD SUCCESS` angezeigt wird.  Die folgende Ausgabe dieses `curl`-Befehls weist beispielsweise auf einen erfolgreichen Abschluss hin.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Sie haben Ihre App nun in Azure App Service bereitgestellt.

## <a name="redeploy-to-azure-app-service-and-use-managed-identities-for-azure-resources"></a>Erneutes Bereitstellen in Azure App Service und Verwenden von verwalteten Identitäten für Azure-Ressourcen

In diesem Abschnitt wird beschrieben, wie Sie eine Identität der Azure-Ressource für die App zuordnen.  Dies ist erforderlich, damit Azure Sicherheitsmaßnahmen anwenden und den Zugriff nachverfolgen kann.  Ein grundlegendes Prinzip von Cloud Computing besteht darin, dass Sie nur für die Ressourcen bezahlen, die Sie auch tatsächlich nutzen.  Diese präzise Nachverfolgung von Ressourcen ist nur möglich, wenn jeder Ressource eine Identität zugeordnet wird.  Azure App Service und Azure Key Vault sind zwei von vielen Azure-Diensten, für die verwaltete Identitäten für Azure-Ressourcen genutzt werden.  Weitere Informationen zu dieser wichtigen Technologie finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

> [!NOTE]
> „Verwaltete Identitäten für Azure-Ressourcen“ ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

Führen Sie die folgenden Schritte aus, um die verwaltete Identität für die Azure App Service-App zu erstellen und dann zuzulassen, dass diese Identität auf die Key Vault-Instanz zugreift.

1. Erstellen Sie eine verwaltete Identität für die App Service-App.  Ersetzen Sie die Optionen durch die Werte Ihrer Elemente `<resourceGroup>` und `<appName>` aus Ihrer POM-Datei.

   ```azurecli
   az webapp identity assign --resource-group contosorg --name contosokeyvault
   ```

   Die Ausgabedaten lauten etwa wie folgt.  Notieren Sie sich den Wert von `principalId` für den nächsten Schritt.

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
   1. Aktualisieren Sie die `azure.keyvault.client-id` so, dass Sie einen Wert für die `principalId` aus dem vorherigen Schritt enthält.  Die fertige Datei sollte nun wie folgt aussehen.

   ```bash
   azure.keyvault.client-id=56rqs994-0o66-43o3-9roo-8e3534d0cb23
   azure.keyvault.enabled=true
   azure.keyvault.tenant-id=72s988os-86s1-41ns-91ab-2q7pq011qo47
   azure.keyvault.uri=https://contosokv.vault.azure.net/    
   ```

1. Die Konfiguration der Key Vault-Instanz ermöglicht `get`- und `list`-Vorgänge für diese verwaltete Identität.  Der Wert der `object-id` ist die `principalId` aus der vorherigen Ausgabe.

   ```azurecli
   az keyvault set-policy --name contosokv \
     --object-id 2r7s6r00-92o9-4sq7-n10r-popq294ssr8s --secret-permissions get list
   ```

   Die Ausgabe ist ein JSON-Objekt mit vielen Informationen zur Key Vault-Instanz.  Es verfügt über den Eintrag `type` mit dem Wert `Microsoft.KeyVault/vaults`.

   In dieser Tabelle sind die obigen Eigenschaften beschrieben.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | name | Der Name der Key Vault-Instanz. | 
   | object-id | Die `principalId` aus dem vorherigen Befehl. |
   | secret-permissions | Die Liste mit den Vorgängen, die für den benannten Prinzipal zulässig sein sollen. |

1. Verpacken Sie die Anwendung, und stellen Sie sie erneut bereit.

   ```bash
   mvn -DskipTests clean package azure-webapp:deploy
   ```

1. Warten Sie noch einige Minuten, damit die Bereitstellung fertig eingerichtet werden kann.  Anschließend erreichen Sie die Bereitstellung mit einem `curl`-Befehl wie oben gezeigt. Hierbei verwenden Sie nun aber den Hostnamen, der in der Ausgabe von `BUILD SUCCESS` angezeigt wird.  Die folgende Ausgabe dieses `curl`-Befehls weist beispielsweise auf einen erfolgreichen Abschluss hin.

   ```bash
   curl https://contosokeyvault.azurewebsites.net/get
   jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;
   ```

Anstatt `defaultValue` zurückzugeben, wird der Wert für `connectionString` ermittelt und aus der Key Vault-Instanz zurückgegeben.  Im nächsten Abschnitt stellen wir dieselbe App in Azure Spring Cloud bereit.

## <a name="deploy-to-azure-spring-cloud"></a>In Azure Spring Cloud bereitstellen

Azure Spring Cloud ist eine vollständig verwaltete Plattform für die Bereitstellung und Ausführung Ihrer Spring Boot-Anwendungen in Azure.  Eine Übersicht über Azure Spring Cloud finden Sie unter [Was ist Azure Spring Cloud?](/azure/spring-cloud/spring-cloud-overview).

In diesem Abschnitt werden die vorhandene Spring Boot-App und die zuvor erstellte Key Vault-Instanz mit einer neuen Instanz von Azure Spring Cloud verwendet.

In den folgenden Schritten wird veranschaulicht, wie Sie eine Azure Spring Cloud-Ressource erstellen und die App dafür bereitstellen.  Stellen Sie sicher, dass Sie die Azure CLI-Erweiterung für Azure Spring Cloud installiert haben, wie dies unter [Voraussetzungen](#prerequisites) beschrieben ist.

1. Wählen Sie einen Namen für die Dienstinstanz aus.  Für die Nutzung von Azure Spring Cloud in Ihrem Azure-Abonnement müssen Sie eine Azure-Ressource vom Typ „Azure Spring Cloud“ erstellen.  Wie bei allen anderen Azure-Ressourcen auch, muss die Dienstinstanz innerhalb einer Ressourcengruppe bleiben.  Verwenden Sie die bereits erstellte Ressourcengruppe, um die Dienstinstanz darin zu speichern.  Erstellen Sie die Dienstinstanz mit diesem Befehl. 

   ```bash
   az spring-cloud create --resource-group "contosorg" --name "contososvc" 
   ```

   Es dauert mehrere Minuten, bis dieser Befehl abgeschlossen ist.

1. Erstellen Sie innerhalb des Diensts eine Spring Cloud-App.

   ```bash
   az spring-cloud app create --resource-group "contosorg" --name "contosoascsapp" --assign-identity --is-public true
     --runtime-version Java_11 --service "contososvc"
   ```

   Diese Tabelle enthält die Beschreibungen für die oben verwendeten Optionen.

   | Parameter | BESCHREIBUNG |
   |---|---|
   | assign-identity | Bewirkt, dass der Dienst eine Identität für verwaltete Identitäten für Azure-Ressourcen erstellt. |
   | is-public | Weist dem Dienst den Namen einer öffentlichen DNS-Domäne zu. |
   | Name | Der Name der App. |
   | resource-group | Der Name der Ressourcengruppe, in der Sie die vorhandene Dienstinstanz erstellt haben. |
   | runtime-version | Die Java Runtime-Version.  **Der Wert muss mit dem Wert übereinstimmen, den Sie oben in Spring Initializr ausgewählt haben.** |
   | Dienst | Der Name des vorhandenen Diensts. |

   Informationen zum Unterschied zwischen einem *Dienst* und einer *App* finden Sie unter [Grundlegendes zu Apps und Bereitstellungen in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

1. Rufen Sie die verwaltete Identität für die Azure-Ressource ab.  Verwenden Sie sie zum Konfigurieren der vorhandenen Key Vault-Instanz, um den Zugriff mit dieser App zuzulassen.

   ```bash
   SERVICE_IDENTITY=$(az spring-cloud app show --resource-group "contosorg" --name "contosoascsapp" --service "contososvc" | jq -r '.identity.principalId')
   az keyvault set-policy --name "contosokv" --object-id <the value of the environment variable SERVICE_IDENTITY> --secret-permissions set get list
   ```

1. Da die vorhandene Spring Boot-App bereits über die Datei *application.properties* mit der erforderlichen Konfiguration verfügt, können wir diese App mit dem folgenden Befehl direkt in Spring Cloud bereitstellen.  Führen Sie den Befehl in dem Verzeichnis aus, in dem die POM-Datei enthalten ist.

   ```bash
   az spring-cloud app deploy --resource-group "contosorg" --name "contosoascsapp" --jar-path target/keyvault-0.0.1-SNAPSHOT.jar \
     --service "contososvc"
   ```

   Mit diesem Befehl wird unter dem Dienst innerhalb der App eine *Bereitstellung* erstellt.  Weitere Informationen zu den Grundlagen von Dienstinstanzen, Apps und Bereitstellungen finden Sie unter [Grundlegendes zu Apps und Bereitstellungen in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-concept-understand-app-and-deployment).

   Wenn die Bereitstellung nicht erfolgreich ist, sollten Sie die Protokolle für die Problembehandlung wie unter [Konfigurieren von Anwendungsprotokollen](https://aka.ms/azure-spring-cloud-configure-logs) beschrieben konfigurieren.  Die Protokolle verfügen wahrscheinlich über nützliche Informationen zum Diagnostizieren und Beheben des Problems.

1. Nachdem die App erfolgreich bereitgestellt wurde, können Sie `curl` verwenden, um die korrekte Funktionsweise der Key Vault-Integration zu überprüfen.  Da Sie `--is-public` angegeben haben, lautet die Standard-URL für Ihren Dienst `https://<service name>-<app name>.azuremicroservices.io/`.  Wenn wir die richtigen Werte einfügen und den Wert der `@GetMapping`-Anmerkung anfügen, erhalten wir den folgenden `curl`-Befehl.

   ```bash
   curl https://contososvc-contosoascsapp.azuremicroservices.io/get
   ```

   In der Ausgabe wird `jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE` angezeigt.

## <a name="summary"></a>Zusammenfassung

Sie haben mit **Spring Initializr** eine neue Java-Webanwendung erstellt.  Sie haben eine Azure Key Vault-Instanz für die Speicherung von vertraulichen Informationen erstellt und Ihre Anwendung dann so konfiguriert, dass Informationen aus Ihrer Key Vault-Instanz abgerufen werden.  Nach einem lokalen Test haben Sie die App für Azure App Service und Azure Spring Cloud bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Konfigurieren einer Spring Boot Initializer-App für die Verwendung von Application Insights](configure-spring-boot-java-applicationinsights.md)
