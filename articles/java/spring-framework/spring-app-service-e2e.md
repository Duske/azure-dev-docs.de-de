---
title: Bereitstellen einer Spring/Tomcat-App in App Service mit Azure Database for MySQL
description: End-to-End-Tutorial für Java App Service mit MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: 19eb7a5633f51400e139ba8dd7ad0a1f5999a213
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507446"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Bereitstellen einer Spring-App in App Service mit MySQL

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie Java-Web-Apps in App Service Linux erstellen, konfigurieren, bereitstellen, skalieren und Probleme behandeln.

Dieses Tutorial basiert auf der beliebten Beispiel-App „Spring PetClinic“. In diesem Thema testen Sie eine HSQLDB-Version der App lokal und stellen sie dann in [Azure App Service](/azure/app-service/containers) bereit. Anschließend werden Sie eine Version konfigurieren und bereitstellen, die [Azure Database for MySQL](/azure/mysql) verwendet. Schließlich erfahren Sie, wie Sie auf die App-Protokolle zugreifen und durch das Erhöhen der Anzahl der Mitarbeiter, die Ihre App ausführen, eine Aufskalierung vornehmen.

## <a name="prerequisites"></a>Voraussetzungen

* [Azure-Befehlszeilenschnittstelle](/cli/azure/overview)
* [Java 8](http://java.oracle.com/)
* [Maven 3](http://maven.apache.org/)
* [Git-Client](https://github.com/)
* [Tomcat 9](https://tomcat.apache.org/download-80.cgi)
* [MySQL-CLI](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Abrufen des Beispiels

Für die ersten Schritte mit der Beispiel-App verwenden Sie folgenden Befehle, um das Quellrepository zu klonen und vorzubereiten.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```
---


## <a name="build-and-run-the-hsqldb-sample-locally"></a>Erstellen und lokales Ausführen des HSQLDB-Beispiels

Zuerst testen wir das Beispiel lokal mit HSQLDB als Datenbank.

Erstellen der HSQLDB-Version der Beispiel-App.

``` azurecli
mvn package
```

Als nächstes legen Sie die Umgebungsvariable „TOMCAT_HOME“ auf den Speicherort Ihrer Tomcat-Installation fest.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$env:TOMCAT_HOME="<Tomcat install directory>"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set TOMCAT_HOME=<Tomcat install directory>
```
---

Aktualisieren Sie dann die *Datei pom.xml* für die Bereitstellung der WAR-Datei. Fügen Sie das folgende XML als untergeordnetes Element des vorhandenen `<plugins>`-Elements hinzu. Ändern Sie `1.7.11` ggf. in die aktuelle Version des [Cargo Maven 2-Plug-Ins](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.11</version>
    <configuration>
        <container>
            <containerId>tomcat9x</containerId>
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

```azurecli
mvn cargo:deploy
```

Starten Sie dann Tomcat.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
& $env:TOMCAT_HOME/bin/catalina.bat run
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
%TOMCAT_HOME%\bin\catalina.bat run
```
---

Sie können nun in Ihrem Browser zu `http://localhost:8080` navigieren, um die ausgeführte App in Aktion zu sehen und ein Gefühl dafür zu bekommen, wie sie funktioniert. Wenn Sie fertig sind, wählen Sie an der Bash-Eingabeaufforderung STRG + C, um Tomcat anzuhalten.

## <a name="deploy-to-azure-app-service"></a>Bereitstellung in Azure App Service

Nachdem Sie nun gesehen haben, wie sie lokal ausgeführt wird, stellen wir die App in Azure bereit.

Legen Sie zunächst die folgenden Umgebungsvariablen fest. Verwenden Sie für `REGION` `West US 2` oder andere Regionen, die sie [hier](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=app-service) finden.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$env:RESOURCEGROUP_NAME="<resource group>"
$env:WEBAPP_NAME="<web app>"
$env:WEBAPP_PLAN_NAME="$env:WEBAPP_NAME-appservice-plan"
$env:REGION="<region>"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCEGROUP_NAME=<resource group>
set WEBAPP_NAME=<web app>
set WEBAPP_PLAN_NAME=%WEBAPP_NAME%-appservice-plan
set REGION=<region>
```
---

Maven verwendet diese Werte, um die Azure-Ressourcen mit den von Ihnen angegebenen Namen zu erstellen. Durch die Verwendung von Umgebungsvariablen können Sie Ihre Kontogeheimnisse aus Ihren Projektdateien heraushalten.

Aktualisieren Sie als nächstes die Datei *pom.xml*, um Maven für eine Azure-Bereitstellung zu konfigurieren. Fügen Sie nach dem zuvor hinzugefügten `<plugin>`-Element den folgenden XML-Code hinzu. Ändern Sie, falls notwendig, `1.9.1` in die aktuelle Version des [Maven-Plug-Ins für Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.1</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 9.0</webContainer>
        </runtime>
        <deployment>
            <resources>
                <resource>
                    <directory>${project.basedir}/target</directory>
                    <includes>
                        <include>*.war</include>
                    </includes>
                </resource>
             </resources>
         </deployment>
    </configuration>
</plugin>
```

Als nächstes melden Sie sich bei Azure an.

```azurecli
az login
```

Stellen Sie dann die App für App Service Linux bereit.

```azurecli
mvn azure-webapp:deploy
```

Sie können jetzt zu `https://<app-name>.azurewebsites.net` navigieren (nachdem `<app-name>` ersetzt wurde), um die ausgeführte App anzuzeigen.

## <a name="set-up-azure-database-for-mysql"></a>Einrichten von Azure Database for MySQL

Als nächstes wechseln wir zur Verwendung von MySQL anstelle von HSQLDB. Wir erstellen eine MySQL-Serverinstanz in Azure und fügen eine Datenbank hinzu. Dann aktualisieren wir die App-Konfiguration mit den neuen Datenbankverbindungsinformationen.

Legen Sie zunächst die folgenden Umgebungsvariablen zur Verwendung in späteren Schritten fest.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
$env:MYSQL_SERVER_NAME="<server>"
$env:MYSQL_SERVER_FULL_NAME="$env:MYSQL_SERVER_NAME.mysql.database.azure.com"
$env:MYSQL_SERVER_ADMIN_LOGIN_NAME="<admin>"
$env:MYSQL_SERVER_ADMIN_PASSWORD="<password>"
$env:MYSQL_DATABASE_NAME="<database>"
$env:DOLLAR="$"
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set MYSQL_SERVER_NAME=<server>
set MYSQL_SERVER_FULL_NAME=%MYSQL_SERVER_NAME%.mysql.database.azure.com
set MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
set MYSQL_SERVER_ADMIN_PASSWORD=<password>
set MYSQL_DATABASE_NAME=<database>
set DOLLAR=$
```
---

Erstellen Sie als nächstes den Datenbankserver, und initialisieren Sie ihn. Verwenden [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up) für die Erstkonfiguration. Verwenden Sie dann [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set), um das Verbindungstimeout zu erhöhen und die Serverzeitzone festzulegen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
az extension add --name db-up

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
    --server ${MYSQL_SERVER_NAME} --value=-8:00
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az extension add --name db-up

az mysql up `
    --resource-group $env:RESOURCEGROUP_NAME `
    --server-name $env:MYSQL_SERVER_NAME `
    --database-name $env:MYSQL_DATABASE_NAME `
    --admin-user $env:MYSQL_SERVER_ADMIN_LOGIN_NAME `
    --admin-password $env:MYSQL_SERVER_ADMIN_PASSWORD

az mysql server configuration set --name wait_timeout `
    --resource-group $env:RESOURCEGROUP_NAME `
    --server $env:MYSQL_SERVER_NAME --value 2147483

az mysql server configuration set --name time_zone `
    --resource-group $env:RESOURCEGROUP_NAME `
    --server $env:MYSQL_SERVER_NAME --value=-8:00
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
az extension add --name db-up

az mysql up ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server-name %MYSQL_SERVER_NAME% ^
    --database-name %MYSQL_DATABASE_NAME% ^
    --admin-user %MYSQL_SERVER_ADMIN_LOGIN_NAME% ^
    --admin-password %MYSQL_SERVER_ADMIN_PASSWORD%

az mysql server configuration set --name wait_timeout ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server %MYSQL_SERVER_NAME% --value 2147483

az mysql server configuration set --name time_zone ^
    --resource-group %RESOURCEGROUP_NAME% ^
    --server %MYSQL_SERVER_NAME% --value=-8:00
```
---

Verwenden Sie anschließend die MySQL-Befehlszeilenschnittstelle, um die Verbindung mit Ihrer Datenbank in Azure herzustellen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME}@${MYSQL_SERVER_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
mysql -u $env:MYSQL_SERVER_ADMIN_LOGIN_NAME@$env:MYSQL_SERVER_NAME `
 -h $env:MYSQL_SERVER_FULL_NAME -P 3306 -p
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mysql -u %MYSQL_SERVER_ADMIN_LOGIN_NAME%@%MYSQL_SERVER_NAME% ^
 -h %MYSQL_SERVER_FULL_NAME% -P 3306 -p
```
---

Führen Sie an der MySQL-Befehlszeilenschnittstelle den folgenden Befehl aus, um zu überprüfen, ob Ihre Datenbank mit dem gleichen Wert benannt ist, den Sie zuvor für die Umgebungsvariable `MYSQL_DATABASE_NAME` angegeben haben.

```console
show databases;
```

MySQL ist jetzt zur Verwendung bereit.

## <a name="configure-the-app-for-mysql"></a>Konfigurieren der App für MySQL

Als Nächstes fügen wir die Verbindungsinformationen zur MySQL-Version der App hinzu und stellen Sie dann für App Service bereit.

Aktualisieren Sie die *pom.xml*-Datei, um MySQL zur aktiven Konfiguration zu machen. Entfernen Sie das `<activation>`-Element aus dem HSQLDB-Profil, und fügen Sie es stattdessen in das MySQL-Profil ein, wie hier gezeigt. Der Rest des Codeausschnitts zeigt die vorhandene Konfiguration. Beachten Sie, wie die zuvor festgelegten Umgebungsvariablen von Maven verwendet werden, um Ihren MySQL-Zugriff zu konfigurieren.

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
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}@${DOLLAR}{MYSQL_SERVER_FULL_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Aktualisieren Sie als nächstes die Datei *pom.xml*, um Maven für eine Azure-Bereitstellung und MySQL-Verwendung zu konfigurieren. Fügen Sie nach dem zuvor hinzugefügten `<plugin>`-Element den folgenden XML-Code hinzu. Ändern Sie, falls notwendig, `1.9.1` in die aktuelle Version des [Maven-Plug-Ins für Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.1</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 9.0</webContainer>
        </runtime>
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
        <deployment>
            <resources>
                <resource>
                    <directory>${project.basedir}/target</directory>
                    <includes>
                        <include>*.war</include>
                    </includes>
                </resource>
             </resources>
         </deployment>
    </configuration>
</plugin>
```

Erstellen Sie als nächstes die App, und testen Sie Sie lokal, indem Sie sie mit Tomcat bereitstellen und ausführen.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
mvn package
mvn cargo:deploy
& $env:TOMCAT_HOME/bin/catalina.bat run
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
mvn package
mvn cargo:deploy
%TOMCAT_HOME%\bin\catalina.bat run
```
---

Sie können die App jetzt lokal unter `http://localhost:8080` anzeigen. Die App sieht aus und verhält sich wie zuvor, verwendet aber Azure Database for MySQL anstelle von HSQLDB. Wenn Sie fertig sind, wählen Sie an der Bash-Eingabeaufforderung STRG + C, um Tomcat anzuhalten.

Stellen Sie die App schließlich in App Service bereit.

```bash
mvn azure-webapp:deploy
```

Sie können jetzt zu `https://<app-name>.azurewebsites.net` navigieren, um die ausgeführte App mit App Service und Azure Database for MySQL anzuzeigen.

## <a name="access-the-app-logs"></a>Zugriff auf die App-Protokolle

Wenn Sie Probleme beheben müssen, können Sie sich die App-Protokolle ansehen. Verwenden Sie den folgenden Befehl, um den Remoteprotokollstream auf dem lokalen Computer zu öffnen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az webapp log tail --name $env:WEBAPP_NAME `
    --resource-group $env:RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az webapp log tail --name %WEBAPP_NAME% ^
    --resource-group %RESOURCEGROUP_NAME%
```
---

Wenn Sie mit dem Anzeigen der Protokolle fertig sind, wählen Sie STRG + C, um den Stream anzuhalten.

Der Protokollstream ist auch unter `https://<app-name>.scm.azurewebsites.net/api/logstream`verfügbar.

## <a name="scale-out"></a>Aufskalieren

Zur Bewältigung eines höheren Datenverkehrsaufkommens für Ihre App können Sie mithilfe des folgenden Befehls eine Aufskalierung auf mehrere Instanzen durchführen.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az appservice plan update --number-of-workers 2 `
    --name $env:WEBAPP_PLAN_NAME `
    --resource-group $env:RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az appservice plan update --number-of-workers 2 ^
    --name %WEBAPP_PLAN_NAME% ^
    --resource-group %RESOURCEGROUP_NAME%
```
---

Glückwunsch! Sie haben eine Java-Web-App mit Spring Framework, JSP, Spring Data, Hibernate, JDBC, App Service Linux und Azure Database for MySQL erstellt erweitert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Abschnitten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen voraussichtlich nicht mehr verwenden, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl ausführen.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
az group delete --name ${RESOURCEGROUP_NAME}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```ps
az group delete --name $env:RESOURCEGROUP_NAME
````

# <a name="cmd"></a>[Cmd](#tab/cmd)

```bash
az group delete --name %RESOURCEGROUP_NAME%
```
---

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich als nächstes die anderen Konfigurations- und CI/CD-Optionen an, die für Java mit App Service verfügbar sind.

> [!div class="nextstepaction"]
> [Konfigurieren einer Linux-Java-App für Azure App Service](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen in einer Java-Web-App mithilfe von Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Bereitstellen in Azure App Service mit dem Jenkins-Plug-In](/azure/jenkins/deploy-jenkins-app-service-plugin)
