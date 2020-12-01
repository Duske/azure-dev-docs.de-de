---
title: Erste Schritte mit Azure SDK für Java
description: Hier finden Sie Informationen zum Erstellen von Azure-Cloudressourcen und erfahren, wie Sie sie verbinden und in Ihren Java-Anwendungen nutzen.
keywords: Azure, Java, SDK, API, authentifizieren, erste Schritte
author: bmitchell287
ms.author: brendm
ms.date: 11/20/2020
ms.topic: article
ms.service: multiple
ms.assetid: b1e10b79-f75e-4605-aecd-eed64873e2d3
ms.custom: seo-java-august2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5b569d07a7686b73bbed7983ea3343b8a36d7bfc
ms.sourcegitcommit: 29930f1593563c5e968b86117945c3452bdefac1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96035422"
---
# <a name="get-started-with-cloud-development-using-java-on-azure"></a>Erste Schritte bei der Cloudentwicklung mit Java in Azure

In diesem Artikel werden die Schritte zum Einrichten einer Entwicklungsumgebung für die Azure-Entwicklung in Java erläutert. Anschließend erstellen Sie Azure-Ressourcen und stellen Verbindungen mit ihnen her, um einige allgemeine Aufgaben (etwa Hochladen einer Datei oder Bereitstellen einer Webanwendung) auszuführen. Wenn Sie fertig sind, können Sie Azure-Dienste in Ihren eigenen Java-Anwendungen verwenden.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie noch kein Konto haben, können Sie eine [kostenlose Testversion](https://azure.microsoft.com/free/) verwenden.
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) oder [Azure CLI 2.0](/cli/azure/install-az-cli2).
- [Java 8](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support) (in Azure Cloud Shell enthalten)
- [Maven 3](https://maven.apache.org/download.cgi) (in Azure Cloud Shell enthalten)

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Die Java-Anwendung benötigt Lese- und Schreibberechtigungen in Ihrem Azure-Abonnement, um den Beispielcode in diesem Tutorial ausführen zu können. Erstellen Sie einen Dienstprinzipal, und konfigurieren Sie Ihre Anwendung so, dass sie mit dessen Anmeldeinformationen ausgeführt wird. Dienstprinzipale ermöglichen die Erstellung eines nicht interaktiven, Ihrer Identität zugeordneten Kontos, dem Sie nur die Berechtigungen erteilen, die zum Ausführen Ihrer App erforderlich sind.

[Erstellen Sie einen Dienstprinzipal mithilfe der Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli), und erfassen Sie die Ausgabe:

```azurecli-interactive
az ad sp create-for-rbac --name AzureJavaTest
```

Sie erhalten eine Antwort im folgenden Format:

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureJavaTest",
  "name": "http://AzureJavaTest",
  "password": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
  "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
}
```

Konfigurieren Sie als Nächstes die Umgebungsvariablen:

- `AZURE_SUBSCRIPTION_ID`: Verwenden Sie den ID-Wert (*id*) aus `az account show` in der Azure CLI 2.0.
- `AZURE_CLIENT_ID`: Verwenden Sie den App-ID-Wert (*appId*) aus der Dienstprinzipalausgabe.
- `AZURE_CLIENT_SECRET`: Verwenden Sie den Kennwortwert (*password*) aus der Dienstprinzipalausgabe.
- `AZURE_TENANT_ID`: Verwenden Sie den Mandantenwert (*tenant*) aus der Dienstprinzipalausgabe.

Weitere Authentifizierungsoptionen finden Sie unter [Azure Identity-Clientbibliothek für Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-java).

## <a name="tooling"></a>Tools

### <a name="create-a-new-maven-project"></a>Erstellen eines neuen Maven-Projekts

> [!NOTE]
> In diesem Artikel wird das Maven-Buildtool verwendet, um den Beispielcode zu erstellen und auszuführen. Andere Buildtools (beispielsweise Gradle) können ebenfalls mit den Azure-Bibliotheken für Java verwendet werden.

Erstellen Sie über die Befehlszeile ein Maven-Projekt in einem neuen Verzeichnis Ihres Systems.

```shell
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=AzureApp \
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Mit diesem Schritt wird ein einfaches Maven-Projekt unter dem Ordner `testAzureApp` erstellt. Fügen Sie dem Projekt `pom.xml` die folgenden Einträge hinzu, um die Bibliotheken zu importieren, die im Beispielcode dieses Tutorials verwendet werden.

```XML
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager</artifactId>
    <version>2.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.8.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.1.jre8</version>
</dependency>
<!-- Only for SQL sample as it's still in preview -->
<dependency>
    <groupId>com.azure.resourcemanager</groupId>
    <artifactId>azure-resourcemanager-sql</artifactId>
    <version>2.0.0-beta.5</version>
</dependency>
```

Fügen Sie unter dem Element `project` der obersten Ebene einen Eintrag vom Typ `build` hinzu, um die Beispiele mithilfe von [maven-exec-plugin](https://www.mojohaus.org/exec-maven-plugin/) auszuführen.

```XML
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.AzureApp</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
 ```

### <a name="install-the-azure-toolkit-for-intellij"></a>Installieren des Azure-Toolkits für IntelliJ

Das [Azure-Toolkit](../toolkit-for-intellij/index.yml) ist erforderlich, wenn Sie Web-Apps oder APIs programmgesteuert bereitstellen möchten. Derzeit wird es für keine anderen Entwicklungsarten verwendet. Der Installationsprozess ist in den folgenden Schritten zusammengefasst. Eine Schnellstartanleitung finden Sie unter [Erstellen einer „Hello World“-Web-App für Azure App Service mit IntelliJ](../toolkit-for-intellij/create-hello-world-web-app.md).

1. Wählen Sie im Menü **File** (Datei) die Option **Settings** (Einstellungen) aus.
1. Klicken Sie auf **Browse repositories** (Repositorys durchsuchen), suchen Sie nach **Azure**, und installieren Sie dann **Azure toolkit for Intellij** (Azure-Toolkit für IntelliJ).
1. Starten Sie IntelliJ neu.

### <a name="install-the-azure-toolkit-for-eclipse"></a>Installieren des Azure-Toolkits für Eclipse

Das [Azure-Toolkit](../toolkit-for-eclipse/index.yml) ist erforderlich, wenn Sie Web-Apps oder APIs programmgesteuert bereitstellen möchten. Derzeit wird es für keine anderen Entwicklungsarten verwendet. Der Installationsprozess ist in den folgenden Schritten zusammengefasst. Eine Schnellstartanleitung finden Sie unter [Erstellen einer „Hello World“-Web-App für Azure App Service mit Eclipse](../toolkit-for-eclipse/create-hello-world-web-app.md).

1. Klicken Sie auf das Menü **Help** (Hilfe) und dann auf **Install New Software** (Neue Software installieren).
1. Geben Sie im Feld **Work with** (Arbeiten mit) die URL `http://dl.microsoft.com/eclipse/` ein, und drücken Sie die **EINGABETASTE**.
1. Aktivieren Sie das Kontrollkästchen neben **Azure toolkit for Java** (Azure-Toolkit für Java). Deaktivieren Sie das Kontrollkästchen **Contact all update sites during install to find required software** (Während der Installation alle Updatesites kontaktieren, um erforderliche Software zu finden). Wählen Sie **Weiter** aus.

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer Linux-VM

Erstellen Sie im Projektverzeichnis `src/main/java/com/fabrikam` eine neue Datei namens `AzureApp.java`, und fügen Sie den folgenden Codeblock ein. Aktualisieren Sie die Variablen `userName` und `sshKey` mit echten Werten für Ihren Computer. Durch den Code wird in der Azure-Region „USA, Osten“ ein neuer virtueller Linux-Computer namens `testLinuxVM` in der Ressourcengruppe `sampleResourceGroup` erstellt.

```java
package com.fabrikam;

import com.azure.core.credential.TokenCredential;
import com.azure.core.http.policy.HttpLogDetailLevel;
import com.azure.core.management.AzureEnvironment;
import com.azure.core.management.Region;
import com.azure.core.management.profile.AzureProfile;
import com.azure.identity.AzureAuthorityHosts;
import com.azure.identity.EnvironmentCredentialBuilder;
import com.azure.resourcemanager.AzureResourceManager;
import com.azure.resourcemanager.compute.models.KnownLinuxVirtualMachineImage;
import com.azure.resourcemanager.compute.models.VirtualMachine;
import com.azure.resourcemanager.compute.models.VirtualMachineSizeTypes;

public class AzureApp {

    public static void main(String[] args) {

        final String userName = "YOUR_VM_USERNAME";
        final String sshKey = "YOUR_PUBLIC_SSH_KEY";

        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            // Create an Ubuntu virtual machine in a new resource group.
            VirtualMachine linuxVM = azureResourceManager.virtualMachines().define("testLinuxVM")
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleVmResourceGroup")
                    .withNewPrimaryNetwork("10.0.0.0/24")
                    .withPrimaryPrivateIPAddressDynamic()
                    .withoutPrimaryPublicIPAddress()
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withUnmanagedDisks()
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
}
```

Führen Sie das Beispiel über die Befehlszeile aus:

```shell
mvn compile exec:java
```

In der Konsole werden einige REST-Anforderungen und Antworten angezeigt, während das SDK die zugrunde liegenden Aufrufe an die REST-API sendet, um den virtuellen Computer und die zugehörigen Ressourcen zu konfigurieren. Überprüfen Sie nach Abschluss des Programms den virtuellen Computer unter Ihrem Abonnement mithilfe der Azure CLI 2.0:

```azurecli-interactive
az vm list --resource-group sampleVmResourceGroup
```

Nachdem Sie sich vergewissert haben, dass der Code erfolgreich ausgeführt wurde, können Sie den virtuellen Computer und dessen Ressourcen über die Befehlszeile löschen:

```azurecli-interactive
az group delete --name sampleVmResourceGroup
```

## <a name="deploy-a-web-app-from-a-github-repo"></a>Bereitstellen einer Web-App aus einem GitHub-Repository

Ersetzen Sie in `AzureApp.java` die Methode „main“ durch die im Anschluss angegebene Methode. Aktualisieren Sie die Variable `appName` auf einen eindeutigen Wert, bevor Sie den Code ausführen. Dieser Code stellt eine Webanwendung aus der Verzweigung `master` in einem öffentlichen GitHub-Repository für eine neue [Azure App Service-Web-App](/azure/app-service-web/app-service-web-overview) bereit, die unter dem Tarif „Free“ ausgeführt wird.

```java
    public static void main(String[] args) {
        try {

            final String appName = "YOUR_APP_NAME";

            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);
            
            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile)
                    .withDefaultSubscription();

            WebApp app = azureResourceManager.webApps().define(appName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleWebResourceGroup")
                    .withNewWindowsPlan(PricingTier.FREE_F1)
                    .defineSourceControl()
                    .withPublicGitRepository(
                            "https://github.com/Azure-Samples/app-service-web-java-get-started")
                    .withBranch("master")
                    .attach()
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Führen Sie den Code wie zuvor mit Maven aus:

```shell
mvn clean compile exec:java
```

Öffnen Sie die Anwendung über die Befehlszeilenschnittstelle in einem Browser:

```azurecli-interactive
az appservice web browse --resource-group sampleWebResourceGroup --name YOUR_APP_NAME
```

Entfernen Sie die Web-App und den Plan aus Ihrem Abonnement, nachdem Sie die Bereitstellung überprüft haben:

```azurecli-interactive
az group delete --name sampleWebResourceGroup
```

## <a name="connect-to-an-azure-sql-database"></a>Herstellen einer Verbindung mit einer Azure SQL-Datenbank

Ersetzen Sie in `AzureApp.java` die aktuelle Methode „main“ durch den im Anschluss angegebenen Code. Legen Sie für die Variablen echte Werte fest.
Durch diesen Code wird eine neue SQL-Datenbank mit einer Firewallregel erstellt, die Remotezugriffe zulässt. Anschließend wird durch den Code unter Verwendung des JBDC-Treibers der SQL-Datenbank eine Verbindung mit der Datenbank hergestellt.

```java
    public static void main(String args[])
    {
        // Create the db using the management libraries.
        try {
            TokenCredential credential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            SqlServerManager sqlServerManager = SqlServerManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(credential, profile);

            final String adminUser = "YOUR_USERNAME_HERE";
            final String sqlServerName = "YOUR_SERVER_NAME_HERE";
            final String sqlDbName = "YOUR_DB_NAME_HERE";
            final String dbPassword = "YOUR_PASSWORD_HERE";
            final String firewallRuleName = "YOUR_RULE_NAME_HERE";

            SqlServer sampleSQLServer = sqlServerManager.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleSqlResourceGroup")
                    .withAdministratorLogin(adminUser)
                    .withAdministratorPassword(dbPassword)
                    .defineFirewallRule(firewallRuleName)
                        .withIpAddressRange("0.0.0.0","255.255.255.255")
                        .attach()
                    .create();

            SqlDatabase sampleSQLDb = sampleSQLServer.databases().define(sqlDbName).create();

            // Assemble the connection string to the database.
            final String domain = sampleSQLServer.fullyQualifiedDomainName();
            String url = "jdbc:sqlserver://"+ domain + ":1433;" +
                    "database=" + sqlDbName +";" +
                    "user=" + adminUser+ "@" + sqlServerName + ";" +
                    "password=" + dbPassword + ";" +
                    "encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";

            // Connect to the database, create a table, and insert an entry into it.
            Connection conn = DriverManager.getConnection(url);

            String createTable = "CREATE TABLE CLOUD ( name varchar(255), code int);";
            String insertValues = "INSERT INTO CLOUD (name, code ) VALUES ('Azure', 1);";
            String selectValues = "SELECT * FROM CLOUD";
            Statement createStatement = conn.createStatement();
            createStatement.execute(createTable);
            Statement insertStatement = conn.createStatement();
            insertStatement.execute(insertValues);
            Statement selectStatement = conn.createStatement();
            ResultSet rst = selectStatement.executeQuery(selectValues);

            while (rst.next()) {
                System.out.println(rst.getString(1) + " "
                        + rst.getString(2));
            }


        } catch (Exception e) {
            System.out.println(e.getMessage());
            System.out.println(e.getStackTrace().toString());
        }
    }
```

Führen Sie das Beispiel über die Befehlszeile aus:

```shell
mvn clean compile exec:java
```

Bereinigen Sie die Ressourcen anschließend über die Befehlszeilenschnittstelle:

```azurecli-interactive
az group delete --name sampleSqlResourceGroup
```

## <a name="write-a-blob-into-a-new-storage-account"></a>Schreiben eines Blobs in ein neues Speicherkonto

Ersetzen Sie in `AzureApp.java` die aktuelle Methode „main“ durch den im Anschluss angegebenen Code. Durch diesen Code wird ein [Azure-Speicherkonto](/azure/storage/common/storage-introduction) erstellt. Anschließend wird unter Verwendung der Azure Storage-Bibliotheken für Java eine neue Textdatei in der Cloud erstellt.

```java
    public static void main(String[] args) {

        try {
            TokenCredential tokenCredential = new EnvironmentCredentialBuilder()
                    .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                    .build();

            // If you do not set the tenant ID and subscription ID via environment variables,
            // change to create the Azure profile with tenantId, subscriptionId, and Azure environment.
            AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

            AzureResourceManager azureResourceManager = AzureResourceManager.configure()
                    .withLogLevel(HttpLogDetailLevel.BASIC)
                    .authenticate(tokenCredential, profile)
                    .withDefaultSubscription();

            // Create a new storage account.
            String storageAccountName = "YOUR_STORAGE_ACCOUNT_NAME_HERE";
            StorageAccount storage = azureResourceManager.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleStorageResourceGroup")
                    .create();

            // Create a storage container to hold the file.
            List<StorageAccountKey> keys = storage.getKeys();
            PublicEndpoints endpoints = storage.endPoints();
            String accountName = storage.name();
            String accountKey = keys.get(0).value();
            String endpoint = endpoints.primary().blob();

            StorageSharedKeyCredential credential = new StorageSharedKeyCredential(accountName, accountKey);

            BlobServiceClient storageClient =new BlobServiceClientBuilder()
                    .endpoint(endpoint)
                    .credential(credential)
                    .buildClient();

            // Container name must be lowercase.
            BlobContainerClient blobContainerClient = storageClient.getBlobContainerClient("helloazure");
            blobContainerClient.create();

            // Make the container public.
            blobContainerClient.setAccessPolicy(PublicAccessType.CONTAINER, null);

            // Write a blob to the container.
            String fileName = "helloazure.txt";
            String textNew = "Hello Azure";

            BlobClient blobClient = blobContainerClient.getBlobClient(fileName);
            InputStream is = new ByteArrayInputStream(textNew.getBytes());
            blobClient.upload(is, textNew.length());

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Führen Sie das Beispiel über die Befehlszeile aus:

```shell
mvn clean compile exec:java
```

Sie können über das Azure-Portal oder mit dem [Azure Storage-Explorer](/azure/vs-azure-tools-storage-explorer-blobs) zur Datei `helloazure.txt` in Ihrem Speicherkonto navigieren.

Bereinigen Sie das Speicherkonto über die Befehlszeilenschnittstelle:

```azurecli-interactive
az group delete --name sampleStorageResourceGroup
```

## <a name="explore-more-samples"></a>Erkunden weiterer Beispiele

Weitere Informationen zur Ressourcenverwaltung und Aufgabenautomatisierung mit den Azure-Verwaltungsbibliotheken für Java finden Sie in unserem Beispielcode für [virtuelle Computer](java-sdk-azure-virtual-machine-samples.md), [Web-Apps](java-sdk-azure-web-apps-samples.md) und [SQL-Datenbank](java-sdk-azure-sql-database-samples.md).

## <a name="reference-and-release-notes"></a>Referenz und Versionshinweise

Für alle Pakete steht eine [Referenz](/java/api) zur Verfügung.

## <a name="get-help-and-give-feedback"></a>Hilfe und Feedback

Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure+java) Fragen an die Community. Melden Sie Fehler und Probleme im Zusammenhang mit den Azure-Bibliotheken für Java auf der [projektspezifischen GitHub-Seite](https://github.com/Azure/azure-sdk-for-java).
