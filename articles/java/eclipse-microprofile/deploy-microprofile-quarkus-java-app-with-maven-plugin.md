---
title: Bereitstellen einer Quarkus-Web-App in Azure App Service mit Maven
description: Hier erfahren Sie, wie Sie mithilfe des Maven-Plug-Ins für Azure-Web-Apps eine Quarkus-App in App Service für Linux bereitstellen.
services: app-service
documentationcenter: java
ms.date: 06/10/2020
ms.service: app-service
ms.topic: article
ms.custom: ''
ms.openlocfilehash: 9a4532da77a460632e4fc4284f23eda3b1adc7a1
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401844"
---
# <a name="deploy-a-quarkus-web-app-to-azure-app-service-with-maven"></a>Bereitstellen einer Quarkus-Web-App in Azure App Service mit Maven

In dieser Schnellstartanleitung wird das [Maven-Plug-In für Azure App Service-Web-Apps](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) verwendet, um eine Quarkus-Anwendung in [Azure App Service für Linux](/azure/app-service/containers/) bereitzustellen. Es empfiehlt sich, eine Java SE-Bereitstellung über [Tomcat- und WAR-Dateien](/azure/app-service/containers/quickstart-java) zu wählen, wenn Sie die Abhängigkeiten, Runtime und Konfiguration Ihrer App in einem einzigen bereitstellbaren Artefakt zusammenfassen möchten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Die [Azure CLI](/cli/azure/), entweder lokal oder über [Azure Cloud Shell](https://shell.azure.com)
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* Apache [Maven](https://maven.apache.org/) (Version 3)

## <a name="install-and-sign-in-to-azure-cli"></a>Installieren der Azure CLI und Anmelden bei der Azure CLI

Bei Verwendung des Maven-Plug-Ins lässt sich die Quarkus-Anwendung am einfachsten und komfortabelsten über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) bereitstellen.

Melden Sie sich mithilfe der Azure CLI bei Ihrem Azure-Konto an:

```azurecli
az login
```

Folgen Sie den Anweisungen, um den Anmeldevorgang abzuschließen.

## <a name="create-sample-app-from-microprofile-starter"></a>Erstellen einer Beispiel-App über MicroProfile Starter

In diesem Abschnitt wird eine Quarkus-Anwendung erstellt und lokal getestet.

### <a name="create-java-se-8-base-project"></a>Erstellen eines Java SE 8-Basisprojekts

1. Navigieren Sie in einem Webbrowser zur Website [MicroProfile Starter](https://start.microprofile.io/).

   ![MicroProfile Starter für Quarkus](./media/quarkus/microprofile-starter-quarkus.png)

2. Füllen Sie die Felder wie folgt aus:  

   |  Feld  |  Wert  |
   | ---- | ---- |
   |  groupId  |  com.microsoft.azure.samples.quarkus  |
   |  artifactId  |  quarkus-hello-azure  |
   |  „MicroProfile Version“ (MicroProfile-Version)  |  MP 3.2  |
   |  „Java SE Version“ (Java SE-Version)  |  Java 8  |
   |  „MicroProfile Runtime“ (MicroProfile-Runtime)  |  Quarkus  |
   |  „Examples for Specifications“ (Beispiele für Spezifikationen)  |  Metriken, OpenAPI  |

3. Wählen Sie die Schaltfläche **DOWNLOAD** aus, um das Projekt herunterzuladen.

4. Entzippen Sie die Archivdatei. Beispiel:

   ```bash
   unzip Quarkus-hello-azure.zip
   ```

### <a name="create-java-se-11-base-project"></a>Erstellen eines Java SE 11-Basisprojekts

Zum Erstellen des Java 11-Basisprojekts können Sie folgende Befehle ausführen:

   ```bash
   $ mvn io.quarkus:quarkus-maven-plugin:1.8.1.Final:create \
     -DprojectGroupId=com.microsoft.azure.samples.quarkus \
     -DprojectArtifactId=quarkus-hello-azure  \
     -DclassName="com.microsoft.azure.samples.quarkus.App" \
     -Dpath="/hello"
   ```

### <a name="run-the-application-in-local-environment"></a>Ausführen der Anwendung in der lokalen Umgebung

1. Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt. Beispiel:

   ```bash
   cd quarkus-hello-azure/
   ```

2. Erstellen Sie das Projekt mithilfe von Maven, und führen Sie es aus. Beispiel:

   ```bash
   mvn quarkus:dev
   ```

3. Testen Sie die Webanwendung, indem Sie sie lokal in einem Webbrowser aufrufen. Sie können beispielsweise den folgenden Befehl verwenden, wenn curl verfügbar ist:

   Java SE 8-Projekt:

   ```bash
   curl http://localhost:8080/data/hello
   ```

   Java SE 11-Projekt:

   ```bash
   curl localhost:8080/hello
   ```

4. Die folgende Meldung sollte angezeigt werden: **Hello World** oder **hello**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Konfigurieren des Maven-Plug-Ins für Azure App Service

In diesem Abschnitt wird die Datei *pom.xml* des Quarkus-Projekts so konfiguriert, dass Maven die App in Azure App Service für Linux bereitstellen kann.

1. Öffnen Sie die Datei *pom.xml* in einem Code-Editor.

2. Fügen Sie in der Datei *pom.xml* im Abschnitt `<build>` innerhalb des Tags `<plugin>` im Anschluss an `maven-surefire-plugin`s den folgenden Eintrag vom Typ `<plugins>` hinzu:

   ```xml
   <plugin>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-webapp-maven-plugin</artifactId>
       <version>1.10.0</version>
   </plugin>
   ```

3. Anschließend können Sie die Bereitstellung konfigurieren, den folgenden maven-Befehl an der Eingabeaufforderung ausführen und mithilfe der **Nummer** die folgenden Optionen in der Aufforderung auswählen:

   ```bash
   mvn azure-webapp:config
   ```

   Optionenparameter:  

   |  Eingabefeld  |  Einzugebender/Auszuwählender Wert  |
   | ---- | ---- |
   |  „Define value for OS(Default: Linux)“ (Wert für Betriebssystem definieren (Standardwert: Linux)):  | 1. linux  |
   |  „Define value for javaVersion(Default: Java 8)“ (Wert für Java-Version definieren (Standardwert: Java 8)):   | 1. Java 11  |
   |  Confirm (Y/N) (Bestätigen (Ja/Nein)) | j |

   Zum Konfigurieren können Sie den folgenden Befehl verwenden:

   ```bash
   $ mvn azure-webapp:config

   [INFO] Scanning for projects...
   [INFO] 
   [INFO] ------< com.microsoft.azure.samples.quarkus:quarkus-hello-azure >-------
   [INFO] Building quarkus-hello-azure 1.0-SNAPSHOT
   [INFO] --------------------------------[ jar ]---------------------------------
   [INFO] 
   [INFO] --- azure-webapp-maven-plugin:1.10.0:config (default-cli) @ quarkus-hello-azure ---

   Define value for OS(Default: Linux): 
   1. linux [*]
   2. windows
   3. docker
   Enter index to use: 
   Define value for javaVersion(Default: Java 8): 
   1. Java 11
   2. Java 8 [*]
   Enter index to use: 1
   Please confirm webapp properties
   AppName : quarkus-hello-azure-1601011883156
   ResourceGroup : quarkus-hello-azure-1601011883156-rg
   Region : westeurope
   PricingTier : PremiumV2_P1v2
   OS : Linux
   RuntimeStack : JAVA 11-java11
   Deploy to slot : false
   Confirm (Y/N)? : y
   [INFO] Saving configuration to pom.
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  16.502 s
   [INFO] Finished at: 2020-09-25T14:31:34+09:00
   [INFO] ------------------------------------------------------------------------
   ```

4. Fügen Sie den Abschnitt `<appSettings>` zum Abschnitt `<configuration>` von `PORT`, `WEBSITES_PORT` und `WEBSITES_CONTAINER_START_TIME_LIMIT` hinzu.  
 Abschließend wird der folgende XML-Eintrag für `azure-webapp-maven-plugin` angezeigt:

   ```xml
      <plugin>
        <groupId>com.microsoft.azure</groupId>  
        <artifactId>azure-webapp-maven-plugin</artifactId>  
        <version>1.10.0</version>  
        <configuration>
          <schemaVersion>V2</schemaVersion>
          <resourceGroup>microprofile</resourceGroup>
          <appName>quarkus-hello-azure-1591836715762</appName>
          <pricingTier>P1v2</pricingTier>
          <region>japaneast</region>
          <runtime>
            <os>linux</os>
            <javaVersion>java11</javaVersion>
            <webContainer>java11</webContainer>
          </runtime>
          <appSettings>
            <property>
              <name>PORT</name>
              <value>8080</value>
            </property>
            <property>
              <name>WEBSITES_PORT</name>
              <value>8080</value>
            </property>
            <property>
              <name>WEBSITES_CONTAINER_START_TIME_LIMIT</name>
              <value>600</value>
            </property>
          </appSettings>
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
   ```

5. Fügen Sie der Eigenschaftendatei (`src/main/resources/application.properties`) den folgenden Eintrag hinzu, um das Uberjar (FAT) hinzuzufügen:

   `quarkus.package.uber-jar=true`

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

Nachdem Sie alle Einstellungen aus den vorhergehenden Abschnitten dieses Artikels konfiguriert haben, können Sie Ihre Webanwendung in Azure bereitstellen. Führen Sie dazu die folgenden Schritte aus:

1. Erstellen Sie an der Eingabeaufforderung oder im zuvor verwendeten Terminalfenster die JAR-Datei mithilfe von Maven neu, wenn Sie Änderungen an der Datei *pom.xml* vorgenommen haben. Beispiel:

   ```bash
   mvn clean package
   ```

2. Stellen Sie Ihre Web-App mit Maven in Azure bereit. Beispiel:

   ```bash
   mvn azure-webapp:deploy
   ```

   Nach erfolgreicher Bereitstellung wird in der Konsole die folgende Meldung angezeigt:

   ```bash
   mvn azure-webapp:deploy

   [INFO] Successfully deployed the artifact to https://quarkus-hello-azure-1591836715762.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  02:20 min
   [INFO] Finished at: 2020-06-11T10:06:51+09:00
   [INFO] ------------------------------------------------------------------------
   ```

   Maven stellt Ihre Webanwendung in Azure bereit. Sollte die Webanwendung oder der Webanwendungsplan noch nicht vorhanden sein, wird sie bzw. er erstellt. Es kann einige Minuten dauern, bis die Webanwendung unter der URL aus der Ausgabe angezeigt wird. Navigieren Sie in einem Webbrowser zu der URL.  Der folgende Bildschirm sollte angezeigt werden:

   ![Startseite für Quarkus](./media/quarkus/quarkus-front-page-11.png)

   Nachdem Ihre Webanwendung bereitgestellt wurde, können Sie sie über das [Azure-Portal] verwalten.

   * Ihre Webanwendung wird in der Ressourcengruppe **microprofile** aufgeführt:

   ![Im Azure-Portal unter „App Services“ aufgeführte Web-App](./media/quarkus/quarkus-azure-portal-rg.png)

   * Außerdem können Sie auf Ihre Webanwendung zugreifen, indem Sie in der **Übersicht** für Ihre Web-App auf die Schaltfläche `Browse` klicken.  
   Vergewissern Sie sich, dass die Bereitstellung erfolgreich war und ausgeführt wird. Folgender Bildschirm sollte angezeigt werden:

   ![Ermitteln der URL für Ihre Web-App im Azure-Portal unter „App Services“](./media/quarkus/quarkus-azure-portal-manage.png)

## <a name="confirm-the-log-stream-from-running-app-service"></a>Überprüfen des Protokolldatenstroms der ausgeführten App Service-Instanz

Sie können die Protokolle der ausgeführten App Service-Instanz anzeigen (mithilfe von „tail“). Alle Aufrufe von `console.log` im Websitecode werden im Terminal angezeigt.

   ```azurecli
   az webapp log tail -g microprofile -n quarkus-hello-azure-1601011883156
   ```

   ![Terminalfenster mit der Protokollausgabe](./media/Quarkus/azure-cli-app-service-log-stream.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

* Wählen Sie im Azure-Portal im linken Menü die Ressourcengruppe aus.
* Geben Sie **microprofile** in das Feld **Nach Name filtern** ein. (Die in diesem Tutorial erstellte Ressourcengruppe sollte dieses Präfix haben.)
* Wählen Sie die Ressourcengruppe aus, die in diesem Tutorial erstellt wurde.
* Wählen Sie im Menü ganz oben den Befehl zum Löschen der Ressourcengruppe aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu MicroProfile und Azure finden Sie im Dokumentationscenter zu MicroProfile in Azure.

> [!div class="nextstepaction"]
> [MicroProfile in Azure](/azure/developer/java/eclipse-microprofile)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zu den verschiedenen in diesem Artikel besprochenen Technologien finden Sie in den folgenden Artikeln:

* [Maven Plugin for Azure Web Apps] (Maven-Plug-In für Azure-Web-Apps)

* [Erstellen eines Azure-Dienstprinzipals mit Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven Settings Reference](https://maven.apache.org/settings.html) (Referenz zu Maven-Einstellungen)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/developer/java/
[Azure-Portal]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Maven Plugin for Azure Web Apps]: /java/api/overview/azure/maven/azure-webapp-maven-plugin/readme (Maven-Plug-In für Azure-Web-Apps)

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png
