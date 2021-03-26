---
title: 'Tutorial: Schützen von Spring Boot-Apps mit Azure Key Vault-Zertifikaten'
description: In diesem Tutorial schützen Sie Ihre Spring Boot-Apps (einschließlich Azure Spring Cloud) mit TLS/SSL-Zertifikaten, indem Sie Azure Key Vault und verwaltete Identitäten für Azure-Ressourcen verwenden.
ms.date: 03/11/2021
ms.service: key-vault
ms.topic: tutorial
ms.custom: devx-track-java, devx-track-azurecli
ms.author: edburns
author: edburns
ms.openlocfilehash: 57fea456490ed53e76984524744a5d54b6deb31e
ms.sourcegitcommit: 3536f174735cd3bb7da7e4b266fbf43349a22b67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103196359"
---
# <a name="tutorial-secure-spring-boot-apps-using-azure-key-vault-certificates"></a>Tutorial: Schützen von Spring Boot-Apps mit Azure Key Vault-Zertifikaten

In diesem Tutorial wird veranschaulicht, wie Sie Ihre Spring Boot-Apps (einschließlich Azure Spring Cloud) mit TLS/SSL-Zertifikaten schützen, indem Sie Azure Key Vault und verwaltete Identitäten für Azure-Ressourcen verwenden.

Für Spring Boot-Anwendungen für die Produktion – ob in der Cloud oder lokal – ist eine End-to-End-Verschlüsselung für Netzwerkdatenverkehr mit TLS-Standardprotokollen erforderlich. Die meisten gängigen TLS/SSL-Zertifikate können über eine öffentliche Stammzertifizierungsstelle ermittelt werden. Es kann aber auch vorkommen, dass diese Ermittlung nicht möglich ist. Wenn Zertifikate nicht ermittelt werden können, muss für die App eine Möglichkeit bestehen, diese Zertifikate zu laden und sie für eingehende Netzwerkverbindungen bereitzustellen und für ausgehende Netzwerkverbindungen zu akzeptieren.

Für Spring Boot-Apps wird die Verwendung von TLS normalerweise ermöglicht, indem die Zertifikate installiert werden. Die Zertifikate werden im lokalen Schlüsselspeicher der JVM installiert, von der die Spring Boot-App ausgeführt wird. Bei Spring in Azure werden Zertifikate nicht lokal installiert. Stattdessen gibt es bei der Spring-Integration für Microsoft Azure eine sichere und reibungslose Vorgehensweise, um die Verwendung von TLS über Azure Key Vault und die verwaltete Identität für Azure-Ressourcen zu ermöglichen.

<!-- https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-doc-resource/spring-to-azure-keyvault-certificates.ai -->
:::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault-certificates/spring-to-azure-key-vault-certificates.svg" alt-text="Diagramm: Interaktion von Elementen in diesem Tutorial" border="false":::

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer GNU/Linux-VM mit systemseitig zugewiesener verwalteter Identität
> * Erstellen einer Azure Key Vault-Instanz
> * Erstellen eines selbstsignierten TLS/SSL-Zertifikats
> * Speichern des selbstsignierten TLS/SSL-Zertifikats auf der Azure Key Vault-Instanz
> * Ausführen einer Spring Boot-Anwendung, bei der das TLS/SSL-Zertifikat für eingehende Verbindungen aus Azure Key Vault stammt
> * Ausführen einer Spring Boot-Anwendung, bei der das TLS/SSL-Zertifikat für ausgehende Verbindungen aus Azure Key Vault stammt

## <a name="prerequisites"></a>Voraussetzungen

- [!INCLUDE [free subscription](includes/quickstarts-free-trial-note.md)]
[!INCLUDE [curl](includes/prerequisites-curl.md)]
[!INCLUDE [jq](includes/prerequisites-jq.md)]
[!INCLUDE [Azure CLI](includes/prerequisites-azure-cli.md)]

- Ein unterstütztes Java Development Kit (JDK), Version 8. Weitere Informationen finden Sie unter [Langfristiger und mittelfristiger Java-Support für Azure und Azure Stack](../fundamentals/java-jdk-long-term-support.md).

- [Apache Maven](http://maven.apache.org/), Version 3.0.

## <a name="create-a-gnulinux-vm-with-system-assigned-managed-identity"></a>Erstellen einer GNU/Linux-VM mit systemseitig zugewiesener verwalteter Identität

Verwenden Sie die folgenden Schritte, um eine Azure-VM mit einer systemseitig zugewiesenen verwalteten Identität zu erstellen und für die Ausführung der Spring Boot-Anwendung vorzubereiten. Eine Übersicht über verwaltete Identitäten für Azure-Ressourcen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

1. Öffnen Sie eine Bash-Shell.

1. Melden Sie sich ab, und löschen Sie einige Authentifizierungsdateien, um ggf. noch vorhandene Anmeldeinformationen zu entfernen.

   ```azurecli
   az logout
   rm ~/.azure/accessTokens.json
   rm ~/.azure/azureProfile.json
   ```

1. Melden Sie sich bei Ihrer Azure CLI-Instanz an.

   ```azurecli
   az login
   ```

1. Legen Sie die Abonnement-ID fest. Achten Sie darauf, dass Sie den Platzhalter durch den richtigen Wert ersetzen.

   ```azurecli
   az account set -s <your subscription ID>
   ```

1. Erstellen einer Azure-Ressourcengruppe. Notieren Sie sich den Namen der Ressourcengruppe zur späteren Verwendung.

   ```azurecli
   az group create \
       --name <your resource group name> \
       --location <your resource group region>
   ```

1. Ermitteln Sie den Uniform Resource Name (URN) für die VM, die Sie erstellen möchten. In diesem Beispiel wird das zertifizierte VM-Image „Azul Zulu for Azure – Enterprise Edition“ verwendet. Ausführliche Informationen zu Azul Zulu for Azure finden Sie auf der Seite mit dem [Java-Download für Azure](https://www.azul.com/downloads/azure-only/zulu/).

   ```azurecli
   az vm image list \
       --offer Zulu \
       --location <your region> \
       --all | grep urn
   ```

   Die Ausführung des Befehls kann eine Weile dauern. Nach Abschluss der Befehlsausführung werden in etwa die folgenden Zeilen ausgegeben. Wählen Sie den Wert für JDK 11 unter Ubuntu aus.

   ```output
   "urn": "azul:azul-zulu11-ubuntu-2004:zulu-jdk11-ubtu2004:20.11.0",
   ...
   "urn": "azul:azul-zulu8-ubuntu-2004:zulu-jdk8-ubtu2004:20.11.0",
   "urn": "azul:azul-zulu8-windows-2019:azul-zulu8-windows2019:20.11.0",
   ```

1. Akzeptieren Sie die Bedingungen, die für das Image gelten, damit die VM erstellt werden kann.

   ```azurecli
   az vm image terms accept --urn azul:azul-zulu11-ubuntu-2004:zulu-jdk11-ubtu2004:20.11.0
   ```

1. Erstellen Sie die VM-Instanz mit einer aktivierten systemseitig zugewiesenen verwalteten Identität, und weisen Sie die Rolle **Besitzer** im Ressourcengruppenbereich zu.

   ```azurecli
   az vm create \
       --resource-group <your resource group name> \
       --name <your VM name> \
       --debug \
       --generate-ssh-keys \
       --assign-identity \
       --image azul:azul-zulu11-ubuntu-2004:zulu-jdk11-ubtu2004:20.11.0 \
       --scope "/subscriptions/<your subscription ID>/resourcegroups/<your resource group name>" \
       --admin-username azureuser \
       --role owner
   ```

   Notieren Sie sich die Werte der Eigenschaften `publicIpAddress` und `systemAssignedIdentity`, die in der JSON-Ausgabe enthalten sind. Sie verwenden diese Werte später im Tutorial.

## <a name="create-and-configure-an-azure-key-vault"></a>Erstellen und Konfigurieren einer Azure Key Vault-Instanz

Verwenden Sie die folgenden Schritte, um eine Azure Key Vault-Instanz zu erstellen und für die systemseitig zugewiesene verwaltete Identität der VM die Berechtigung für den Zugriff auf Key Vault durch Zertifikate zu gewähren.

1. Erstellen Sie in der Ressourcengruppe eine Azure Key Vault-Instanz.

   ```azurecli
   az keyvault create \
       --resource-group <your resource group name> \
       --name <your Key Vault name> \
       --location <your resource group region>
   export KEY_VAULT_URI=$(az keyvault show --name ${KEY_VAULT} | jq -r '.properties.vaultUri')
   ```

   Notieren Sie sich den Wert für `KEY_VAULT_URI`. Es wird später noch benötigt.

1. Gewähren Sie der VM die Berechtigung zur Verwendung der Key Vault-Instanz für Zertifikate.

   ```azurecli
   az keyvault set-policy \
       --name <your Key Vault name> \
       --object-id <your system-assigned identity> \
       --secret-permissions get list \
       --certificate-permissions get list import
   ```

## <a name="create-and-store-a-self-signed-tlsssl-certificate"></a>Erstellen und Speichern eines selbstsignierten TLS/SSL-Zertifikats

Die Schritte in diesem Tutorial gelten für alle TLS/SSL-Zertifikate (einschließlich selbstsignierte Zertifikate), die direkt in Azure Key Vault gespeichert werden. Selbstsignierte Zertifikate sind nicht für die Verwendung in der Produktion geeignet, aber sie sind hilfreich für Entwicklungs- und Testanwendungen. In diesem Tutorial wird ein selbstsigniertes Zertifikat verwendet. Verwenden Sie den folgenden Befehl, um das Zertifikat zu erstellen.

```azurecli
az keyvault certificate create \
    --vault-name <your Key Vault name> \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

## <a name="run-a-spring-boot-application-with-secure-inbound-connections"></a>Ausführen einer Spring Boot-Anwendung mit sicheren eingehenden Verbindungen

In diesem Abschnitt erstellen Sie eine Spring Boot Starter-Anwendung, bei der das TLS/SSL-Zertifikat für eingehende Verbindungen von Azure Key Vault stammt.

Verwenden Sie die folgenden Schritte, um die Anwendung zu erstellen:

1. Navigieren Sie zu <https://start.spring.io/>.
1. Wählen Sie die Optionen so aus, wie dies in der Abbildung unterhalb dieser Liste dargestellt ist.
   * **Project** (Projekt): **Maven Project** (Maven-Projekt)
   * **Language** (Sprache): **Java**
   * **Spring Boot**: **2.3.7**
   * **Group** (Gruppe): *com.contoso* (Sie können hier einen beliebigen gültigen Java-Paketnamen eingeben.)
   * **Artifact** (Artefakt): *ssltest* (Sie können hier einen beliebigen gültigen Java-Klassennamen eingeben.)
   * **Packaging** (Paketerstellung): **Jar**
   * **Java**: **11**
1. Wählen Sie **Add Dependencies...** (Abhängigkeiten hinzufügen...) aus.
1. Geben Sie im Textfeld den Text *Spring Web* ein, und drücken Sie STRG+EINGABE.
1. Geben Sie im Textfeld den Text *Azure Support* ein, und drücken Sie die EINGABETASTE. Auf Ihrem Bildschirm sollte nun Folgendes angezeigt werden.
   :::image type="content" source="media/configure-spring-boot-starter-java-app-with-azure-key-vault-certificates/spring-initializr-choices.png" alt-text="Spring Initializr mit Auswahl der richtigen Optionen":::
1. Wählen Sie am unteren Rand der Seite die Option **Generate** (Generieren) aus.
1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter. In diesem Tutorial wird das Verzeichnis *ssltest* im aktuellen Basisverzeichnis des Benutzers verwendet. Die oben eingegebenen Werte führen dazu, dass in diesem Verzeichnis die Datei *ssltest.zip* erstellt wird.

### <a name="enable-the-spring-boot-app-to-load-the-tlsssl-certificate"></a>Aktivieren der Spring Boot-App zum Laden des TLS/SSL-Zertifikats

Verwenden Sie die folgenden Schritte, um für die App das Laden des Zertifikats zu ermöglichen:

1. Entzippen Sie die Datei *ssltest.zip*.

1. Entfernen Sie das Verzeichnis *test* und dessen Unterverzeichnisse. Sie können das Verzeichnis problemlos löschen, da der Test in diesem Tutorial ignoriert wird.

1. Die Datei enthält das unten angegebene Layout.

   ```
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       └── main
           ├── java
           │   └── com
           │       └── contoso
           │           └── ssltest
           │               └── SsltestApplication.java
           └── resources
               ├── application.properties
               ├── static
               └── templates
   ```

1. Ändern Sie die POM-Datei, um eine Abhängigkeit von `azure-spring-boot-starter-keyvault-certificates` hinzuzufügen. Fügen Sie dem Abschnitt `<dependencies>` der Datei *pom.xml* den folgenden Code hinzu:

   ```xml
   <dependency>
      <groupId>com.azure.spring</groupId>
      <artifactId>azure-spring-boot-starter-keyvault-certificates</artifactId>
      <version>3.0.0-beta.2</version>
   </dependency>
   ```

1. Bearbeiten Sie die Datei *src/main/resources/application.properties* so, dass sie Folgendes enthält:

   ```properties
   server.port=8443
   server.ssl.key-alias=mycert
   server.ssl.key-store-type=AzureKeyVault
   server.ssl.trust-store-type=AzureKeyVault
   azure.keyvault.uri=https://<your Key Vault name>.vault.azure.net/
   ```

   Basierend auf diesen Werten kann die Spring Boot-App die Aktion *Laden* für das TLS/SSL-Zertifikat so durchführen, wie dies am Anfang des Tutorials beschrieben ist. Die folgende Tabelle enthält eine Beschreibung der Eigenschaftswerte.

   | Eigenschaftsname | Erklärung |
   |---------------|-------------|
   |server.port|Der lokale TCP-Port, über den auf HTTPS-Verbindungen gelauscht wird.|
   |server.ssl.key-alias|Der Wert des Arguments `--name`, das Sie an `az keyvault certificate create` übergeben haben.|
   |server.ssl.key-store-type|Muss `AzureKeyVault`lauten.|
   |server.ssl.trust-store-type|Muss `AzureKeyVault`lauten.|
   |azure.keyvault.uri|Die Eigenschaft `vaultUri` im JSON-Code, der von `az keyvault create` zurückgegeben wird. Sie haben diesen Wert in einer Umgebungsvariablen gespeichert.|

   Die einzige spezifische Eigenschaft für Key Vault ist `azure.keyvault.uri`. Die App wird auf einer VM ausgeführt, für deren systemseitig zugewiesene verwaltete Identität der Zugriff auf Key Vault gewährt wurde. Aus diesem Grund wurde der Zugriff auch für die App gewährt.

Diese Änderungen ermöglichen der Spring Boot-App das Laden des TLS/SSL-Zertifikats. Im nächsten Abschnitt ermöglichen Sie für die App das Durchführen der Aktion *Akzeptieren* für das TLS/SSL-Zertifikat, wie dies am Anfang des Tutorials beschrieben ist.

### <a name="create-a-spring-boot-rest-controller"></a>Erstellen eines Spring Boot-REST-Controllers

Verwenden Sie die folgenden Schritte, um den REST-Controller zu erstellen:

1. Bearbeiten Sie die Datei *src/main/java/com/contoso/ssltest/SsltestApplication.java* so, dass sie Folgendes enthält:

   ```java
   package com.contoso.ssltest;

   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @SpringBootApplication
   @RestController
   public class SsltestApplication {

       public static void main(String[] args) {
           SpringApplication.run(SsltestApplication.class, args);
       }

       @GetMapping(value = "/ssl-test")
       public String inbound(){
           return "Inbound TLS is working!!";
       }

       @GetMapping(value = "/exit")
       public void exit() {
           System.exit(0);
       }

   }
   ```

   Beachten Sie hierbei, dass das Aufrufen von `System.exit(0)` aus einem nicht authentifizierten REST-GET-Aufruf nur zu Demonstrationszwecken erfolgt. Führen Sie dies nicht in einer realen Anwendung durch.

   Dieser Code veranschaulicht die Aktion *Bereitstellen*, die am Anfang dieses Tutorials erwähnt wurde. Die folgende Liste enthält einige Details zu diesem Code:

   * Die von Spring Initializr generierte Klasse `SsltestApplication` enthält jetzt die Anmerkung `@RestController`.
   * Es ist eine Methode mit der Anmerkung `@GetMapping` und einem `value` für den von Ihnen durchgeführten HTTP-Aufruf vorhanden.
   * Von der `inbound`-Methode wird lediglich eine Begrüßung zurückgegeben, wenn von einem Browser eine HTTPS-Anforderung an den Pfad `/ssl-test` gesendet wird. Mit der `inbound`-Methode wird veranschaulicht, wie das TLS/SSL-Zertifikat vom Browser für den Server bereitgestellt wird.
   * Die `exit`-Methode führt dazu, dass die JVM beim Aufrufen beendet wird. Diese Methode dient als Hilfe, um die Ausführung des Beispiels im Rahmen dieses Tutorials zu vereinfachen.

1. Öffnen Sie eine neue Bash-Shell, und navigieren Sie zum Verzeichnis *ssltest*. Führen Sie den folgenden Befehl aus:

   ```bash
   mvn clean package
   ```

   Der Code wird von Maven kompiliert und als ausführbare JAR-Datei verpackt.

1. Vergewissern Sie sich, dass für die in `<your resource group name>` erstellte Netzwerksicherheitsgruppe eingehender Datenverkehr über die Ports 22 und 8443 von Ihrer IP-Adresse zulässig ist. Informationen zur Konfiguration von Netzwerksicherheitsgruppen-Regeln zum Zulassen von eingehendem Datenverkehr finden Sie unter [Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe](/azure/virtual-network/manage-network-security-group) im Abschnitt [Arbeiten mit Sicherheitsregeln](/azure/virtual-network/manage-network-security-group#work-with-security-rules).

1. Fügen Sie die ausführbare JAR-Datei auf der VM ein.

   ```bash
   cd target
   sftp azureuser@<your VM public IP address>
   put *.jar
   ```

### <a name="run-the-app-on-the-server"></a>Ausführen der App auf dem Server

Nachdem Sie die Spring Boot-App nun erstellt und auf die VM hochgeladen haben, führen Sie sie mit den folgenden Schritten auf der VM aus und rufen den REST-Endpunkt per curl auf.

1. Stellen Sie per SSH eine Verbindung mit der VM her, und führen Sie dann die ausführbare JAR-Datei aus.

   ```bash
   set -o noglob
   ssh azureuser@<your VM public IP address> "java -jar *.jar"
   ```

1. Öffnen Sie eine neue Bash-Shell, und führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass das TLS/SSL-Zertifikat vom Server bereitgestellt wird.

   ```bash
   curl https://<your VM public IP address>:8443/ssl-test
   ```

   Es sollte eine Fehlermeldung zur Legitimität des Servers angezeigt werden. Diese Nachricht ist zu erwarten, da es sich um ein selbstsigniertes Zertifikat handelt. Fügen Sie dem `curl`-Befehl die Option `--insecure` hinzu. Die folgende Meldung sollte angezeigt werden: `Inbound TLS is working!!`.

1. Rufen Sie den `exit`-Pfad auf, um den Server zu beenden und die Netzwerksockets zu schließen.

   ```bash
   curl https://<your VM public IP address>:8443/exit
   ```

Nachdem Sie sich nun mit den Aktionen *Laden* und *Bereitstellen* mit einem selbstsignierten TLS/SSL-Zertifikat vertraut gemacht haben, nehmen Sie an der App einige kleinere Änderungen vor, um die Aktion *Akzeptieren* durchzuführen.

## <a name="run-a-spring-boot-application-with-secure-outbound-connections"></a>Ausführen einer Spring Boot-Anwendung mit sicheren ausgehenden Verbindungen

In diesem Abschnitt ändern Sie den Code aus dem vorherigen Abschnitt so, dass das TLS/SSL-Zertifikat für ausgehende Verbindungen von Azure Key Vault stammt. Die Aktionen *Laden*, *Bereitstellen* und *Akzeptieren* basieren also auf Azure Key Vault.

### <a name="modify-the-ssltestapplication-to-illustrate-outbound-tls-connections"></a>Ändern von SsltestApplication zur Veranschaulichung von ausgehenden TLS-Verbindungen

Fügen Sie zunächst einen neuen REST-Endpunkt mit dem Namen `ssl-test-outbound` hinzu. Für diesen Endpunkt wird ein eigener TLS-Socket geöffnet und sichergestellt, dass die TLS-Verbindung das TLS/SSL-Zertifikat akzeptiert.

Ersetzen Sie den Inhalt von *SsltestApplication.java* durch den folgenden Code:

```java
   package com.contoso.ssltest;


   import java.security.GeneralSecurityException;
   import java.security.KeyStore;
   import javax.net.ssl.HostnameVerifier;
   import javax.net.ssl.SSLContext;
   import javax.net.ssl.SSLSession;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.http.HttpStatus;
   import org.springframework.http.ResponseEntity;
   import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.web.client.RestTemplate;

   import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
   import org.apache.http.conn.ssl.TrustSelfSignedStrategy;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClients;
   import org.apache.http.ssl.SSLContexts;

   @SpringBootApplication
   @RestController
   public class SsltestApplication {

       public static void main(String[] args) {
           SpringApplication.run(SsltestApplication.class, args);
       }

       @GetMapping(value = "/ssl-test")
       public String inbound(){
           return "Inbound TLS is working!!";
       }

       @GetMapping(value = "/ssl-test-outbound")
       public String outbound() throws GeneralSecurityException {
           KeyStore ks = KeyStore.getInstance(KeyStore.getDefaultType());
           SSLContext sslContext = SSLContexts.custom()
               .loadTrustMaterial(ks, new TrustSelfSignedStrategy())
               .build();

       HostnameVerifier allowAll = (String hostName, SSLSession session) -> true;
       SSLConnectionSocketFactory csf = new SSLConnectionSocketFactory(sslContext, allowAll);

       CloseableHttpClient httpClient = HttpClients.custom()
           .setSSLSocketFactory(csf)
           .build();

       HttpComponentsClientHttpRequestFactory requestFactory =
           new HttpComponentsClientHttpRequestFactory();

       requestFactory.setHttpClient(httpClient);
       RestTemplate restTemplate = new RestTemplate(requestFactory);
       String sslTest = "https://localhost:8443/ssl-test";

       ResponseEntity<String> response
           = restTemplate.getForEntity(sslTest, String.class);

       return "Outbound TLS " +
           (response.getStatusCode() == HttpStatus.OK ? "is" : "is not")  + " Working!!";
       }

       @GetMapping(value = "/exit")
       public void exit() {
           System.exit(0);
       }

   }
```

Führen Sie als Nächstes die Neuerstellung der App durch, laden Sie sie auf die VM hoch, und führen Sie sie dann erneut aus.

1. Fügen Sie die Abhängigkeit vom Apache-HTTP-Client hinzu, indem Sie im Abschnitt `<dependencies>` der Datei *pom.xml* den folgenden Code hinzufügen:

   ```xml
   <dependency>
      <groupId>org.apache.httpcomponents</groupId>
      <artifactId>httpclient</artifactId>
      <version>4.5.13</version>
   </dependency>
   ```

1. Erstellen Sie die App.

   ```bash
   cd ssltest
   mvn clean package
   ```

1. Laden Sie die App erneut hoch, indem Sie den gleichen `sftp`-Befehl wie weiter oben in diesem Artikel verwenden.

   ```bash
   cd target
   sftp <your VM public IP address>
   put *.jar
   ```

1. Führen Sie die App auf der VM aus.

   ```bash
   set -o noglob
   ssh azureuser@<your VM public IP address> "java -jar *.jar"
   ```

1. Überprüfen Sie nach dem Beginn der Ausführung des Servers, ob dieser das TLS/SSL-Zertifikat akzeptiert. Führen Sie in derselben Bash-Shell, in der Sie den obigen `curl`-Befehl ausgeführt haben, den folgenden Befehl aus:

   ```bash
   curl --insecure https://<your VM public IP address>:8443/ssl-test-outbound
   ```

   Die Meldung `Outbound TLS is working!!` sollte angezeigt werden.

1. Rufen Sie den `exit`-Pfad auf, um den Server zu beenden und die Netzwerksockets zu schließen.

   ```bash
   curl https://<your VM public IP address>:8443/exit
   ```

Sie haben nun eine einfache Veranschaulichung der Aktionen *Laden*, *Bereitstellen* und *Akzeptieren* mit der Speicherung eines selbstsignierten TLS/SSL-Zertifikats in Azure Key Vault verfolgt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie mithilfe des folgenden Befehls löschen:

```azurecli
az group delete --name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die weiteren Möglichkeiten mit Spring und Azure.

> [!div class="nextstepaction"]
> [Weitere Spring Boot Starter-Optionen](spring-boot-starters-for-azure.md)
