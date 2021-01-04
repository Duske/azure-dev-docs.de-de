---
title: Bereitstellen einer Helidon-Web-App in Azure App Service mit Maven
description: Hier erfahren Sie, wie Sie mithilfe des Maven-Plug-Ins für Azure-Web-Apps eine Helidon-App in App Service für Linux bereitstellen.
services: app-service
documentationcenter: java
ms.date: 06/10/2020
ms.service: app-service
ms.topic: article
ms.custom: ''
ms.openlocfilehash: ad3ccbc729b768d8aa72162506d44dea7906e424
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754136"
---
# <a name="deploy-a-helidon-web-app-to-azure-app-service-with-maven"></a>Bereitstellen einer Helidon-Web-App in Azure App Service mit Maven

In dieser Schnellstartanleitung wird das [Maven-Plug-In für Azure App Service-Web-Apps](https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) verwendet, um eine Helidon-Anwendung in [Azure App Service für Linux](/azure/app-service/containers/) bereitzustellen. Es empfiehlt sich, eine Java SE-Bereitstellung über [Tomcat- und WAR-Dateien](/azure/app-service/containers/quickstart-java) zu wählen, wenn Sie die Abhängigkeiten, Runtime und Konfiguration Ihrer App in einem einzigen bereitstellbaren Artefakt zusammenfassen möchten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Die [Azure CLI](/cli/azure/), entweder lokal oder über [Azure Cloud Shell](https://shell.azure.com)
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* Apache [Maven](https://maven.apache.org/) (Version 3)

## <a name="install-and-sign-in-to-azure-cli"></a>Installieren der Azure CLI und Anmelden bei der Azure CLI

Bei Verwendung des Maven-Plug-Ins lässt sich die Helidon-Anwendung am einfachsten und komfortabelsten über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) bereitstellen.

Melden Sie sich mithilfe der Azure CLI bei Ihrem Azure-Konto an:

```azurecli
az login
```

Folgen Sie den Anweisungen, um den Anmeldevorgang abzuschließen.

## <a name="create-sample-app-from-microprofile-starter"></a>Erstellen einer Beispiel-App über MicroProfile Starter

In diesem Abschnitt wird eine Helidon-Anwendung erstellt und lokal getestet.

1. Navigieren Sie in einem Webbrowser zur Website [MicroProfile Starter](https://start.microprofile.io/).

   ![MicroProfile Starter](./media/helidon/microprofile-starter-helidon.png)

1. Füllen Sie die Felder wie folgt aus:  

   |  Feld  | Wert  |
   | ---- | ---- |
   |  groupId  |  com.microsoft.azure.samples.helidon  |
   |  artifactId  |  helidon-hello-azure  |
   |  „MicroProfile Version“ (MicroProfile-Version)  |  MP 3.2  |
   |  „Java SE Version“ (Java SE-Version)  |  Java 11  |
   |  „MicroProfile Runtime“ (MicroProfile-Runtime)  |  Helidon  |
   |  „Examples for Specifications“ (Beispiele für Spezifikationen)  |  Metriken, OpenAPI  |

1. Wählen Sie die Schaltfläche **DOWNLOAD** aus, um das Projekt herunterzuladen.

1. Entzippen Sie die Archivdatei. Beispiel:

   ```bash
   unzip helidon-hello-azure.zip
   ```

1. Alternativ können Sie das Projekt mithilfe des folgenden Befehls erstellen:

   ```bash
   mvn -U archetype:generate -DinteractiveMode=false \
       -DarchetypeGroupId=io.helidon.archetypes \
       -DarchetypeArtifactId=helidon-quickstart-se \
       -DarchetypeVersion=2.0.0 \
       -DgroupId=com.microsoft.azure.samples.helidon \
       -DartifactId=helidon-hello-azure \
       -Dpackage=com.microsoft.azure.samples.helidon
   ```

1. Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt. Beispiel:

   ```bash
   cd helidon-hello-azure/
   ```

1. Erstellen Sie die JAR-Datei mit Maven. Beispiel:

   ```bash
   mvn clean package
   ```

1. Starten Sie die Web-App nach der Erstellung mithilfe von Maven. Beispiel:

   ```bash
   java -jar target/helidon-hello-azure.jar
   ```

1. Testen Sie die Web-App, indem Sie sie lokal mit einem Webbrowser durchsuchen. Sie können beispielsweise den folgenden Befehl verwenden, wenn curl verfügbar ist:

   ```bash
   curl http://localhost:8080/greet
   ```

1. Die folgende Meldung sollte angezeigt werden: **Hello World**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Konfigurieren des Maven-Plug-Ins für Azure App Service

In diesem Abschnitt wird die Datei *pom.xml* des Helidon-Projekts so konfiguriert, dass Maven die App in Azure App Service für Linux bereitstellen kann.

1. Öffnen Sie die Datei *pom.xml* in einem Code-Editor.

2. Fügen Sie in der Datei *pom.xml* im Abschnitt `<build>` innerhalb des Tags `<plugin>` den folgenden Eintrag vom Typ `<plugins>` hinzu:

   ```xml
   <build>
     <finalName>helidon-hello-azure</finalName>
     <plugins>
       <plugin>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>azure-webapp-maven-plugin</artifactId>
         <version>1.10.0</version>
       </plugin>
     </plugins>
   </build>
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
   mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO]
   [INFO] ------< com.microsoft.azure.samples.helidon:helidon-hello-azure >-------
   [INFO] Building myproject 1.0-SNAPSHOT
   [INFO] --------------------------------[ jar ]---------------------------------
   [INFO] 
   [INFO] --- azure-webapp-maven-plugin:1.10.0:config (default-cli) @ helidon-hello-azure ---
   Define value for OS(Default: Linux): 
   1. linux [*]
   2. windows
   3. docker
   Enter index to use: 1
   Define value for javaVersion(Default: Java 8): 
   1. Java 11
   2. Java 8 [*]
   Enter index to use: 1
   Please confirm webapp properties
   AppName : helidon-hello-azure-1600998900939
   ResourceGroup : helidon-hello-azure-1600998900939-rg
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
   [INFO] Total time:  02:44 min
   [INFO] Finished at: 2020-09-25T10:57:35+09:00
   [INFO] ------------------------------------------------------------------------
   ```

4. Fügen Sie den Abschnitt `<appSettings>` zum Abschnitt `<configuration>` von `PORT`, `WEBSITES_PORT` und `WEBSITES_CONTAINER_START_TIME_LIMIT` hinzu. Fügen Sie außerdem `<include>/libs/*.jar</include>` den Ressourcen in der Bereitstellung hinzu.

   ```xml
   <plugin>
     <groupId>com.microsoft.azure</groupId>  
     <artifactId>azure-webapp-maven-plugin</artifactId>  
     <version>1.10.0</version>  
     <configuration>
       <schemaVersion>V2</schemaVersion>
       <resourceGroup>microprofile</resourceGroup>
       <appName>helidon-hello-azure-1591663020899</appName>
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
               <include>/libs/*.jar</include>
             </includes>
           </resource>
         </resources>
       </deployment>
     </configuration>
   ```

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

Nachdem Sie alle Einstellungen in den vorhergehenden Abschnitten in diesem Artikel konfiguriert haben, können Sie Ihre Web-App in Azure bereitstellen. Führen Sie dazu die folgenden Schritte aus:

1. Erstellen Sie an der Eingabeaufforderung oder im zuvor verwendeten Terminalfenster die JAR-Datei mithilfe von Maven neu, wenn Sie Änderungen an der Datei *pom.xml* vorgenommen haben. Beispiel:

   ```bash
   mvn clean package
   ```

1. Stellen Sie Ihre Web-App mit Maven in Azure bereit. Beispiel:

   ```bash
   mvn azure-webapp:deploy
   ```

Maven stellt Ihre Web-App in Azure bereit. Falls die Web-App oder der Web-App-Plan noch nicht vorhanden ist, wird sie bzw. er erstellt. Es kann einige Minuten dauern, bis die Web-App unter der URL aus der Ausgabe angezeigt wird. Navigieren Sie in einem Webbrowser zu der URL.  Daraufhin sollte die folgende Nachricht angezeigt werden: **Hello World**

Nachdem Ihre Web-App bereitgestellt wurde, können Sie sie über das [Azure-Portal] verwalten.

* Ihre Web-App wird unter **App Services** aufgeführt:

   ![Im Azure-Portal unter „App Services“ aufgeführte Web-App](./media/helidon/azure-portal-app-service-screen.png)

* Außerdem können Sie auf Ihre Web-App zugreifen, indem Sie in der **Übersicht** für Ihre Web-App auf die Schaltfläche `Browse` klicken:

   ![Ermitteln der URL für Ihre Web-App im Azure-Portal unter „App Services“](./media/helidon/azure-portal-app-service-overview.png)

Vergewissern Sie sich mithilfe des gleichen cURL-Befehls wie zuvor (`/data/hello`), dass die Bereitstellung erfolgreich war. Verwenden Sie dabei anstelle von `localhost` die URL Ihrer Web-App aus dem Portal. Die folgende Meldung sollte angezeigt werden: **Hello World**

## <a name="confirm-the-log-stream-from-running-app-service"></a>Überprüfen des Protokolldatenstroms der ausgeführten App Service-Instanz

Sie können die Protokolle der ausgeführten App Service-Instanz anzeigen (mithilfe von „tail“). Alle Aufrufe von `console.log` im Websitecode werden im Terminal angezeigt.

```azurecli
az webapp log tail -g microprofile -n helidon-hello-azure-1600998900939
```

![Terminalfenster mit der Protokollausgabe](./media/helidon/azure-cli-app-service-log-stream.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

* Wählen Sie im Azure-Portal im linken Menü die Ressourcengruppe aus.
* Geben Sie **microprofile** in das Feld **Nach Name filtern** ein. (Die in diesem Tutorial erstellte Ressourcengruppe sollte dieses Präfix haben.)
* Wählen Sie die Ressourcengruppe aus, die in diesem Tutorial erstellt wurde.
* Wählen Sie im Menü ganz oben den Befehl zum Löschen der Ressourcengruppe aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu MicroProfile und Azure finden Sie im Dokumentationscenter zu MicroProfile in Azure.

> [!div class="nextstepaction"]
> [MicroProfile in Azure](./index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zu den verschiedenen in diesem Artikel besprochenen Technologien finden Sie in den folgenden Artikeln:

* [Maven Plugin for Azure Web Apps] (Maven-Plug-In für Azure-Web-Apps)

* [Erstellen eines Azure-Dienstprinzipals mit Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven Settings Reference](https://maven.apache.org/settings.html) (Referenz zu Maven-Einstellungen)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: ../index.yml
[Azure-Portal]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Maven Plugin for Azure Web Apps]: https://github.com/microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md (Maven-Plug-In für Azure-Web-Apps)

[Java Development Kit (JDK)]: ../fundamentals/java-jdk-long-term-support.md
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png