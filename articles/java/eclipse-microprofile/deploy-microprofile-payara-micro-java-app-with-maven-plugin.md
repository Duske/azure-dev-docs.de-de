---
title: Bereitstellen einer Payara Micro-Web-App in Azure App Service mit Maven
description: Hier erfahren Sie, wie Sie mithilfe des Maven-Plug-Ins für Azure-Web-Apps eine Payara Micro-App in App Service für Linux bereitstellen.
services: app-service
documentationcenter: java
ms.date: 06/10/2020
ms.service: app-service
ms.topic: article
ms.custom: ''
ms.openlocfilehash: 4c3c17d2339821f684e6bded7774a46669c0bbbf
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401840"
---
# <a name="deploy-a-payara-micro-web-app-to-azure-app-service-with-maven"></a>Bereitstellen einer Payara Micro-Web-App in Azure App Service mit Maven

In dieser Schnellstartanleitung wird das [Maven-Plug-In für Azure App Service-Web-Apps](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) verwendet, um eine Payara Micro-Anwendung in [Azure App Service für Linux](/azure/app-service/containers/) bereitzustellen. Es empfiehlt sich, eine Java SE-Bereitstellung über [Tomcat- und WAR-Dateien](/azure/app-service/containers/quickstart-java) zu wählen, wenn Sie die Abhängigkeiten, Runtime und Konfiguration Ihrer App in einem einzigen bereitstellbaren Artefakt zusammenfassen möchten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Die [Azure CLI](/cli/azure/), entweder lokal oder über [Azure Cloud Shell](https://shell.azure.com)
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Maven](https://maven.apache.org/) von Apache, Version 3)

## <a name="install-and-sign-in-to-azure-cli"></a>Installieren der Azure CLI und Anmelden bei der Azure CLI

Bei Verwendung des Maven-Plug-Ins lässt sich die Payara Micro-Anwendung am einfachsten und komfortabelsten über die [Azure-Befehlszeilenschnittstelle](/cli/azure/) bereitstellen.

Melden Sie sich mithilfe der Azure CLI bei Ihrem Azure-Konto an:

```azurecli
az login
```

Folgen Sie den Anweisungen, um den Anmeldevorgang abzuschließen.

## <a name="create-sample-app-from-microprofile-starter"></a>Erstellen einer Beispiel-App über MicroProfile Starter

In diesem Abschnitt wird eine Payara Micro-Anwendung erstellt und lokal getestet.

1. Navigieren Sie in einem Webbrowser zur Website [MicroProfile Starter](https://start.microprofile.io/).

   ![MicroProfile Starter für Payara Micro](./media/payara-micro/microprofile-starter-payara-micro.png)

2. Füllen Sie die Felder wie folgt aus:  

   | Feld  |  Wert  |
   | ---- | ---- |
   |  groupId  |  com.microsoft.azure.samples.payaramicro  |
   |  artifactId  |  payaramicro-hello-azure  |
   |  „MicroProfile Version“ (MicroProfile-Version)  |  MP 3.2  |
   |  „Java SE Version“ (Java SE-Version)  |  Java 11  |
   |  „MicroProfile Runtime“ (MicroProfile-Runtime)  |  Payara Micro  |
   |  „Examples for Specifications“ (Beispiele für Spezifikationen)  |  Metriken, OpenAPI  |

3. Wählen Sie die Schaltfläche **DOWNLOAD** aus, um das Projekt herunterzuladen.

4. Entzippen Sie die Archivdatei. Beispiel:

   ```bash
   unzip payaraMicro-hello-azure.zip
   ```

### <a name="run-the-application-in-local-environment"></a>Ausführen der Anwendung in der lokalen Umgebung

1. Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt. Beispiel:

   ```bash
   cd payaramicro-hello-azure/
   ```

2. Erstellen Sie das Projekt mithilfe von Maven. Beispiel:

   ```bash
   mvn clean package
   ```

3. Führen Sie das Projekt aus. Beispiel:

   ```bash
   java -jar target/payaramicro-hello-azure-microbundle.jar
   ```

4. Testen Sie die Web-App, indem Sie sie lokal mit einem Webbrowser durchsuchen. Sie können beispielsweise den folgenden Befehl verwenden, wenn curl verfügbar ist:

   ```bash
   curl http://localhost:8080/data/hello
   ```

5. Die folgende Meldung sollte angezeigt werden: **Hello World**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Konfigurieren des Maven-Plug-Ins für Azure App Service

In diesem Abschnitt wird die Datei *pom.xml* des Payara Micro-Projekts so konfiguriert, dass Maven die App in Azure App Service für Linux bereitstellen kann.

1. Öffnen Sie die Datei *pom.xml* in einem Code-Editor.

2. Fügen Sie in der Datei *pom.xml* im Abschnitt `<build>` innerhalb des Tags `<plugin>` den folgenden Eintrag vom Typ `<plugins>` hinzu:

   ```xml
   <build>
     <finalName>payaramicro-hello-azure</finalName>
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

   ```cmd
   mvn azure-webapp:config
   ```

   Optionenparameter:  

   |  Eingabefeld  |  Einzugebender/Auszuwählender Wert  |
   | ---- | ---- |
   |  „Define value for OS(Default: Linux)“ (Wert für Betriebssystem definieren (Standardwert: Linux)):  | 1. linux  |
   |  „Define value for javaVersion(Default: Java 8)“ (Wert für Java-Version definieren (Standardwert: Java 8)):   | 1. Java 11  |
   |  „Define value for runtimeStack(Default: TOMCAT 8.5)“ (Wert für Runtime-Stapel definieren (Standardwert: TOMCAT 8.5)): | TOMCAT 8.5 |
   |  Confirm (Y/N) (Bestätigen (Ja/Nein)) | j |

   [!NOTE]
   Soll hier tatsächlich `TOMCAT 8.5` ausgewählt werden, obwohl wir nicht Tomcat verwenden? Dieser Wert wird später im Rahmen der Detailkonfiguration von `TOMCAT 8.5` in `Java11` geändert.**

   Zum Konfigurieren können Sie den folgenden Befehl verwenden:

   ```cmd
   mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO] 
   [INFO] --< com.microsoft.azure.samples.payaramicro:payaramicro-hello-azure >---
   [INFO] Building payaramicro-hello-azure 1.0-SNAPSHOT
   [INFO] --------------------------------[ war ]---------------------------------
   [INFO] 
   [INFO] --- azure-webapp-maven-plugin:1.10.0:config (default-cli) @ payaramicro-hello-azure ---
   Define value for OS(Default: Linux): 
   1. linux [*]
   2. windows
   3. docker
   Enter index to use: 
   Define value for javaVersion(Default: Java 8): 
   1. Java 11
   2. Java 8 [*]
   Enter index to use: 1
   Define value for runtimeStack(Default: TOMCAT 8.5): 
   1. TOMCAT 9.0
   2. TOMCAT 8.5 [*]
   Enter index to use: 
   Please confirm webapp properties
   AppName : payaramicro-hello-azure-1601009217863
   ResourceGroup : payaramicro-hello-azure-1601009217863-rg
   Region : westeurope
   PricingTier : PremiumV2_P1v2
   OS : Linux
   RuntimeStack : TOMCAT 8.5-java11
   Deploy to slot : false
   Confirm (Y/N)? : y
   [INFO] Saving configuration to pom.
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  22.302 s
   [INFO] Finished at: 2020-09-25T13:47:11+09:00
   [INFO] ------------------------------------------------------------------------
   ```

4. Ändern Sie die Runtime von `TOMCAT 8.5` in `java11` und die Bereitstellungsdatei von `*.war` in `*.jar`. Fügen Sie anschließend den Abschnitt `<appSettings>` zum Abschnitt `<configuration>` von `PORT`, `WEBSITES_PORT` und `WEBSITES_CONTAINER_START_TIME_LIMIT` hinzu.  
 Abschließend wird der folgende XML-Eintrag für `azure-webapp-maven-plugin` angezeigt:

    ```xml
    <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>1.9.1</version>
      <configuration>
        <schemaVersion>V2</schemaVersion>
        <resourceGroup>microprofile</resourceGroup>
        <appName>payaramicro-hello-azure-1591860934798</appName>
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

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der Anwendung in Azure

Nachdem Sie alle Einstellungen in den vorhergehenden Abschnitten in diesem Artikel konfiguriert haben, können Sie Ihre Web-App in Azure bereitstellen. Führen Sie dazu die folgenden Schritte aus:

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

   [INFO] Successfully deployed the artifact to https://payaramicro-hello-azure-1601009217863.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  01:58 min
   [INFO] Finished at: 2020-09-25T13:55:13+09:00
   [INFO] ------------------------------------------------------------------------
   ```

   Maven stellt Ihre Web-App in Azure bereit. Falls die Web-App oder der Web-App-Plan noch nicht vorhanden ist, wird sie bzw. er erstellt. Es kann einige Minuten dauern, bis die Web-App unter der URL aus der Ausgabe angezeigt wird. Navigieren Sie in einem Webbrowser zu der URL.  Der folgende Bildschirm sollte angezeigt werden:

   ![Erste Seite von Payara Micro](./media/payara-micro/payara-micro-front-page.png)

   Nachdem Ihre Web-App bereitgestellt wurde, können Sie sie über das [Azure-Portal] verwalten.

   * Ihre Web-App wird in der Ressourcengruppe **microprofile** aufgeführt:

   ![Im Azure-Portal unter „App Services“ aufgeführte Web-App](./media/payara-micro/payara-micro-azure-portal-rg.png)

   * Außerdem können Sie auf Ihre Web-App zugreifen, indem Sie in der **Übersicht** für Ihre Web-App auf die Schaltfläche `Browse` klicken.  
Vergewissern Sie sich, dass die Bereitstellung erfolgreich war und ausgeführt wird. Folgender Bildschirm sollte angezeigt werden:

   ![Ermitteln der URL für Ihre Web-App im Azure-Portal unter „App Services“](./media/payara-micro/payara-micro-azure-portal-manage.png)

## <a name="confirm-the-log-stream-from-running-app-service"></a>Überprüfen des Protokolldatenstroms der ausgeführten App Service-Instanz
 
Sie können die Protokolle der ausgeführten App Service-Instanz anzeigen (mithilfe von „tail“). Alle Aufrufe von `console.log` im Websitecode werden im Terminal angezeigt.
 
   ```azurecli
   az webapp log tail -g microprofile -n payaramicro-hello-azure-1601009217863
   ```
 
   ![Überprüfen des Protokolldatenstroms](./media/payara-micro/azure-cli-app-service-log-stream.png)
 
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
