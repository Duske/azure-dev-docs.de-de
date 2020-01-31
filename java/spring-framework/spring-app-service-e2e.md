---
title: Bereitstellen einer Spring/Tomcat-App in App Service mit Azure Database for MySQL
description: End-to-End-Tutorial für Java App Service mit MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: 47f318708fbe786b2fd0b58dc7d68cdd5c975856
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76872135"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Bereitstellen einer Spring-App in App Service mit MySQL

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie Java-Web-Apps in App Service Linux erstellen, konfigurieren, bereitstellen, skalieren und Probleme behandeln.

Dieses Tutorial basiert auf der beliebten Beispiel-App „Spring PetClinic“. In diesem Thema testen Sie eine HSQLDB-Version der App lokal und stellen sie dann in [Azure App Service](/azure/app-service/containers) bereit. Anschließend werden Sie eine Version konfigurieren und bereitstellen, die [Azure Database for MySQL](/azure/mysql) verwendet. Schließlich erfahren Sie, wie Sie auf die App-Protokolle zugreifen und durch das Erhöhen der Anzahl der Mitarbeiter, die Ihre App ausführen, eine Erweiterung vornehmen.

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/overview)
* [Java 8](http://java.oracle.com/)
* [Maven 3](http://maven.apache.org/)
* [Git-Client](https://github.com/)
* [Tomcat](https://tomcat.apache.org/download-80.cgi)
* [MySQL-CLI](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Abrufen des Beispiels

Für die ersten Schritte mit der Beispiel-App verwenden Sie folgenden Befehle, um das Quellrepository zu klonen und vorzubereiten.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux.git
cd e2e-java-experience-in-app-service-linux
yes | cp -rf .prep/* .
```

## <a name="build-and-run-the-hsqldb-sample-locally"></a>Erstellen und lokales Ausführen des HSQLDB-Beispiels

Zuerst testen wir das Beispiel lokal mit HSQLDB als Datenbank.

Navigieren Sie zur HSQLDB-Version des Beispiel und erstellen Sie es dann.

```bash
cd initial-hsqldb/spring-framework-petclinic
mvn package
```

Als nächstes legen Sie die Umgebungsvariable „TOMCAT_HOME“ auf den Speicherort Ihrer Tomcat-Installation fest.

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

Aktualisieren Sie dann die Datei *pom.xml*, um Maven für die Bereitstellung einer Tomcat-WAR-Datei zu konfigurieren. Fügen Sie das folgende XML als untergeordnetes Element des vorhandenen `<plugins>`-Elements hinzu. Ändern Sie `1.7.7` ggf. in die aktuelle Version des [Cargo Maven 2-Plug-Ins](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.7</version>
    <configuration>
        <container>
            <containerId>tomcat8x</containerId>
            <type>installed</type>
            <home>${TOMCAT_HOME}</home>
        </container>
        <configuration>
            <type>existing</type>
            <home>${TOMCAT_HOME}</home>
        </configuration>
        <deployables>
            <deployable>
                <groupId>${project.groupId}</groupId>
                <artifactId>${project.artifactId}</artifactId>
                <type>war</type>
                <properties>
                    <context>/</context>
                </properties>
            </deployable>
        </deployables>
    </configuration>
</plugin>
```

Mit dieser Konfiguration können Sie die App lokal in Tomcat bereitstellen.

```bash
mvn cargo:deploy
```

Starten Sie dann Tomcat.

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

Sie können nun in Ihrem Browser zu [http://localhost:8080](http://localhost:8080) navigieren, um die ausgeführte App zu sehen und ein Gefühl dafür zu bekommen, wie sie funktioniert. Wenn Sie fertig sind, wählen Sie an der Bash-Eingabeaufforderung STRG + C, um Tomcat anzuhalten.

## <a name="deploy-to-azure-app-service"></a>Bereitstellung in Azure App Service

Nachdem Sie nun gesehen haben, wie sie lokal ausgeführt wird, stellen wir die App in Azure bereit.

Legen Sie zunächst die folgenden Umgebungsvariablen fest.

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

Maven verwendet diese Werte, um die Azure-Ressourcen mit den von Ihnen angegebenen Namen zu erstellen. Durch die Verwendung von Umgebungsvariablen können Sie Ihre Kontogeheimnisse aus Ihren Projektdateien heraushalten.

Aktualisieren Sie als nächstes die Datei *pom.xml*, um Maven für eine Azure-Bereitstellung zu konfigurieren. Fügen Sie nach dem zuvor hinzugefügten `<plugin>`-Element den folgenden XML-Code hinzu. Ändern Sie, falls notwendig, `1.7.0` in die aktuelle Version des [Maven-Plug-Ins für Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.0</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 8.5</webContainer>
        </runtime>
    </configuration>
</plugin>
```

Als nächstes melden Sie sich bei Azure an.

```azurecli
az login
```

Stellen Sie dann die App für App Service Linux bereit.

```bash
mvn azure-webapp:deploy
```

Sie können jetzt zu `https://<app-name>.azurewebsites.net` navigieren (nachdem `<app-name>` ersetzt wurde), um die ausgeführte App anzuzeigen.

## <a name="set-up-azure-database-for-mysql"></a>Einrichten von Azure Database for MySQL

Als nächstes wechseln wir zur Verwendung von MySQL anstelle von HSQLDB. Wir erstellen eine MySQL-Serverinstanz in Azure und fügen eine Datenbank hinzu. Dann aktualisieren wir die App-Konfiguration mit den neuen Datenbankverbindungsinformationen.

Legen Sie zunächst die folgenden Umgebungsvariablen zur Verwendung in späteren Schritten fest.

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

Erstellen Sie als nächstes den Datenbankserver, und initialisieren Sie ihn. Verwenden [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up) für die Erstkonfiguration. Verwenden Sie dann [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set), um das Verbindungstimeout zu erhöhen und die Serverzeitzone festzulegen.

```azurecli
az mysql up \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server-name ${MYSQL_SERVER_NAME} \
    --database-name ${MYSQL_DATABASE_NAME} \
    --admin-user ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
    --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD}

az mysql server configuration set --name wait_timeout \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value 2147483

az mysql server configuration set --name time_zone \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value -8:00
```

Verwenden Sie dann die MySQL-CLI, um die Datenbank zu erstellen.

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

Führen Sie an der MySQL-CLI-Eingabeaufforderung den folgenden Befehl aus, und ersetzen Sie `<database name>` durch denselben Wert, den Sie zuvor für die Umgebungsvariable `MYSQL_DATABASE_NAME` angegeben haben.

```console
CREATE DATABASE <database name>;
```

MySQL ist jetzt zur Verwendung bereit.

## <a name="configure-the-app-for-mysql"></a>Konfigurieren der App für MySQL

Als Nächstes fügen wir die Verbindungsinformationen zur MySQL-Version der App hinzu und stellen Sie dann für App Service bereit.

Navigieren Sie zunächst an der Bash-Eingabeaufforderung zum richtigen Ordner.

```bash
cd ../../initial-mysql/spring-framework-petclinic
```

Aktualisieren Sie anschließend die Datei *pom.xml*, um MySQL als aktive Konfiguration zu konfigurieren. Entfernen Sie das `<activation>`-Element aus dem HSQLDB-Profil, und fügen Sie es stattdessen in das MySQL-Profil ein, wie hier gezeigt. Der Rest des Codeausschnitts zeigt die vorhandene Konfiguration. Beachten Sie, wie die zuvor festgelegten Umgebungsvariablen von Maven verwendet werden, um Ihren MySQL-Zugriff zu konfigurieren.

```xml
<profile>
    <id>MySQL</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <db.script>mysql</db.script>
        <jpa.database>MYSQL</jpa.database>
        <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
        <jdbc.url>jdbc:mysql://${DOLLAR}{MYSQL_SERVER_FULL_NAME}:3306/${DOLLAR}{MYSQL_DATABASE_NAME}?useUnicode=true</jdbc.url>
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Aktualisieren Sie als nächstes die Datei *pom.xml*, um Maven für eine Azure-Bereitstellung und MySQL-Verwendung zu konfigurieren. Fügen Sie nach dem zuvor hinzugefügten `<plugin>`-Element den folgenden XML-Code hinzu. Ändern Sie, falls notwendig, `1.7.0` in die aktuelle Version des [Maven-Plug-Ins für Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.7.0</version>
    <configuration>

        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>

        <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>

        <appSettings>
            <property>
                <name>MYSQL_SERVER_FULL_NAME</name>
                <value>${MYSQL_SERVER_FULL_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_LOGIN_NAME</name>
                <value>${MYSQL_SERVER_ADMIN_LOGIN_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_PASSWORD</name>
                <value>${MYSQL_SERVER_ADMIN_PASSWORD}</value>
            </property>
            <property>
                <name>MYSQL_DATABASE_NAME</name>
                <value>${MYSQL_DATABASE_NAME}</value>
            </property>
        </appSettings>

    </configuration>
</plugin>
```

Erstellen Sie als nächstes die App, und testen Sie Sie lokal, indem Sie sie mit Tomcat bereitstellen und ausführen.

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

Sie können die App jetzt lokal unter [http://localhost:8080](http://localhost:8080) anzeigen. Die App sieht aus und verhält sich wie zuvor, verwendet aber Azure Database for MySQL anstelle von HSQLDB. Wenn Sie fertig sind, wählen Sie an der Bash-Eingabeaufforderung STRG + C, um Tomcat anzuhalten.

Stellen Sie die App schließlich in App Service bereit.

```bash
mvn azure-webapp:deploy
```

Sie können jetzt zu `https://<app-name>.azurewebsites.net` navigieren, um die ausgeführte App mit App Service und Azure Database for MySQL anzuzeigen.

## <a name="access-the-app-logs"></a>Zugriff auf die App-Protokolle

Wenn Sie Probleme beheben müssen, können Sie sich die App-Protokolle ansehen. Verwenden Sie den folgenden Befehl, um den Remoteprotokollstream auf dem lokalen Computer zu öffnen.

```azurecli
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Wenn Sie mit dem Anzeigen der Protokolle fertig sind, wählen Sie STRG + C, um den Stream anzuhalten.

Der Protokollstream ist auch unter `https://<app-name>.scm.azurewebsites.net/api/logstream`verfügbar.

## <a name="scale-out"></a>Horizontales Skalieren

Zur Bewältigung eines höheren Datenverkehrsaufkommens für Ihre App können Sie mithilfe des folgenden Befehls eine Erweiterung auf mehrere Instanzen durchführen.

```azurecli
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Glückwunsch! Sie haben eine Java-Web-App mit Spring Framework, JSP, Spring Data, Hibernate, JDBC, App Service Linux und Azure Database for MySQL erstellt erweitert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Abschnitten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen voraussichtlich nicht mehr verwenden, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl ausführen.

```azurecli
az group delete --name ${RESOURCEGROUP_NAME}
```

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich als nächstes die anderen Konfigurations- und CI/CD-Optionen an, die für Java mit App Service verfügbar sind.

> [!div class="nextstepaction"]
> [Konfigurieren einer Linux-Java-App für Azure App Service](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen in einer Java-Web-App mithilfe von Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Bereitstellen in Azure App Service mit dem Jenkins-Plug-In](/azure/jenkins/deploy-jenkins-app-service-plugin)
