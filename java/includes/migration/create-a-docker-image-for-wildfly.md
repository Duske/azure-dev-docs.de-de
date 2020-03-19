---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: ab70d4861cc619417f78cfc053d44b22dd35014f
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897571"
---
### <a name="create-a-docker-image-for-wildfly"></a>Erstellen eines Docker-Images für WildFly

Zum Erstellen eines Dockerfile benötigen Sie Folgendes:

* Ein unterstütztes JDK
* Eine Installation von WildFly
* Ihre JVM-Runtimeoptionen.
* Eine Möglichkeit zum Übergeben von Umgebungsvariablen (falls zutreffend).

Sie können bei Bedarf dann die in den folgenden Abschnitten beschriebenen Schritte ausführen. Sie können das [Schnellstartrepository für WildFly-Container](https://github.com/Azure/wildfly-container-quickstart) als Ausgangspunkt für Ihr Dockerfile und die Webanwendung verwenden.

1. [Konfigurieren von KeyVault FlexVolume](#configure-keyvault-flexvolume)
2. [Einrichten von Datenquellen](#set-up-data-sources)
3. [Einrichten von JNDI-Ressourcen](#set-up-jndi-resources)
4. [Überprüfen der WildFly-Konfiguration](#review-wildfly-configuration)

#### <a name="configure-keyvault-flexvolume"></a>Konfigurieren von KeyVault FlexVolume

Erstellen Sie eine Azure Key Vault-Instanz, und fügen Sie alle erforderlichen Geheimnisse ein. Weitere Informationen finden Sie unter [Quickstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI](/azure/key-vault/quick-create-cli). Konfigurieren Sie anschließend eine [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md)-Instanz, um diese Geheimnisse für Pods zugänglich zu machen.

Außerdem müssen Sie das Startskript aktualisieren, das für das WildFly-Bootstrapping verwendet wird. Mit diesem Skript müssen die Zertifikate in den von WildFly genutzten Keystore importiert werden, bevor der Server gestartet wird.

#### <a name="set-up-data-sources"></a>Einrichten von Datenquellen

Zum Konfigurieren von WildFly für den Zugriff auf eine Datenquelle müssen Sie die JDBC-JAR-Treiberdatei Ihrem Docker-Image hinzufügen und anschließend die entsprechenden JBoss CLI-Befehle ausführen. Mit diesen Befehlen muss beim Erstellen des Docker-Images die Datenquelle eingerichtet werden.

Die folgenden Schritte enthalten eine Anleitung für PostgreSQL, MySQL und SQL Server.

1. Laden Sie den JDBC-Treiber für [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) oder [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) herunter.

    Entpacken Sie das heruntergeladene Archiv, um die JAR-Treiberdatei zu erhalten.

1. Erstellen Sie eine Datei mit einem Namen wie `module.xml`, und fügen Sie das folgende Markup hinzu. Ersetzen Sie den Platzhalter `<module name>` (einschließlich der spitzen Klammern) mit `org.postgres` für PostgreSQL, `com.mysql` für MySQL oder `com.microsoft` für SQL Server. Ersetzen Sie `<JDBC .jar file path>` durch den Namen der JAR-Datei aus dem vorherigen Schritt. Hierzu gehört auch der vollständige Pfad des Speicherorts, an dem Sie die Datei in Ihrem Docker-Image anordnen möchten, z. B. `/opt/database`.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Erstellen Sie eine Datei mit einem Namen wie `datasource-commands.cli`, und fügen Sie den unten angegebenen Code hinzu. Ersetzen Sie `<JDBC .jar file path>` durch den Wert, den Sie im vorherigen Schritt verwendet haben. Ersetzen Sie `<module file path>` durch den Dateinamen und Pfad aus dem vorherigen Schritt, z. B. `/opt/database/module.xml`.

    **PostgreSQL**

    ```console
    batch
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)
    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    reload
    run batch
    shutdown
    ```

    **MySQL**

    ```console
    batch
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)
    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter
    reload
    run batch
    shutdown
    ```

    **SQL Server**

    ```console
    batch
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)
    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter
    reload
    run batch
    shutdown
    ```

1. Aktualisieren Sie die JTA-Datenquellenkonfiguration für Ihre Anwendung:

    Öffnen Sie die Datei `src/main/resources/META-INF/persistence.xml` für Ihre App, und suchen Sie nach dem Element `<jta-data-source>`. Ersetzen Sie seinen Inhalt wie folgt:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

1. Fügen Sie Ihrem `Dockerfile` Folgendes hinzu, damit die Datenquelle während des Buildvorgangs für Ihr Docker-Image erstellt wird.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/database/datasource-commands.cli && \
    sleep 30
    ```

1. Ermitteln Sie die zu verwendende `DATABASE_CONNECTION_URL`. Sie unterscheidet sich für jeden Datenbankserver und auch von den Werten im Azure-Portal. Die hier angezeigten URL-Formate sind für die Verwendung durch WildFly erforderlich:

    **PostgreSQL**

    ```console
    jdbc:postgresql://<database server name>:5432/<database name>?ssl=true
    ```

    **MySQL**

    ```console
    jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT
    ```

    **SQL Server**

    ```console
    jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

1. Beim späteren Erstellen Ihrer YAML-Bereitstellungsdatei müssen Sie die Umgebungsvariablen `DATABASE_CONNECTION_URL`, `DATABASE_SERVER_ADMIN_FULL_NAME` und `DATABASE_SERVER_ADMIN_PASSWORD` durch die entsprechenden Werte ersetzen.

Weitere Informationen über die Konfigurierung der Datenbankverbindungen mit WildFly finden Sie unter [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) oder [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

#### <a name="set-up-jndi-resources"></a>Einrichten von JNDI-Ressourcen

Normalerweise führen Sie die folgenden Schritte aus, um die einzelnen JNDI-Ressourcen einzurichten, die Sie für WildFly konfigurieren müssen:

1. Laden Sie die erforderlichen JAR-Dateien herunter, und kopieren Sie sie in das Docker-Image.
2. Erstellen Sie die WildFly-Datei *module.xml*, und verweisen Sie darin auf diese JAR-Dateien.
3. Erstellen Sie eine beliebige Konfiguration, die von der jeweiligen JNDI-Ressource benötigt wird.
4. Erstellen Sie ein JBoss CLI-Skript, das während des Docker-Buildvorgangs zum Registrieren der JNDI-Ressource verwendet werden soll.
5. Fügen Sie all diese Elemente dem Dockerfile hinzu.
6. Übergeben Sie die entsprechenden Umgebungsvariablen in Ihrer YAML-Bereitstellungsdatei.

Das Beispiel unten enthält die Schritte, die zum Erstellen der JNDI-Ressource für die JMS-Konnektivität mit Azure Service Bus ausgeführt werden müssen.

1. Laden Sie den [Apache Qpid JMS-Anbieter](https://qpid.apache.org/components/jms/index.html) herunter.

    Entpacken Sie das heruntergeladene Archiv, um die JAR-Dateien zu erhalten.

1. Erstellen Sie eine Datei mit einem Namen wie `module.xml`, und fügen Sie das folgende Markup in `/opt/servicebus` hinzu. Stellen Sie sicher, dass die Versionsnummern der JAR-Dateien mit den Namen der JAR-Dateien aus dem vorherigen Schritt übereinstimmen.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
     <resources>
      <resource-root path="proton-j-0.31.0.jar"/>
      <resource-root path="qpid-jms-client-0.40.0.jar"/>
      <resource-root path="slf4j-log4j12-1.7.25.jar"/>
      <resource-root path="slf4j-api-1.7.25.jar"/>
      <resource-root path="log4j-1.2.17.jar"/>
      <resource-root path="netty-buffer-4.1.32.Final.jar" />
      <resource-root path="netty-codec-4.1.32.Final.jar" />
      <resource-root path="netty-codec-http-4.1.32.Final.jar" />
      <resource-root path="netty-common-4.1.32.Final.jar" />
      <resource-root path="netty-handler-4.1.32.Final.jar" />
      <resource-root path="netty-resolver-4.1.32.Final.jar" />
      <resource-root path="netty-transport-4.1.32.Final.jar" />
      <resource-root path="netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar" />
      <resource-root path="netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar" />
      <resource-root path="netty-transport-native-unix-common-4.1.32.Final.jar" />
      <resource-root path="qpid-jms-discovery-0.40.0.jar" />
     </resources>
     <dependencies>
      <module name="javax.api"/>
      <module name="javax.jms.api"/>
     </dependencies>
    </module>
    ```

1. Erstellen Sie in `/opt/servicebus` die Datei `jndi.properties`.

    ```console
    connectionfactory.${MDB_CONNECTION_FACTORY}=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}
    queue.${MDB_QUEUE}=${SB_QUEUE}
    topic.${MDB_TOPIC}=${SB_TOPIC}
    ```

1. Erstellen Sie eine Datei mit einem Namen wie `servicebus-commands.cli`, und fügen Sie den unten angegebenen Code hinzu.

    ```console
    batch
    /subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)
    /system-property=property.mymdb.queue:add(value=myqueue)
    /system-property=property.connection.factory:add(value=java:global/remoteJMS/SBF)
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/opt/servicebus/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/opt/servicebus/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    run-batch
    reload
    shutdown
    ```

1. Fügen Sie Ihrem `Dockerfile` Folgendes hinzu, damit die JNDI-Ressource während des Buildvorgangs für Ihr Docker-Image erstellt wird.

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/servicebus/servicebus-commands.cli && \
    sleep 30
    ```

1. Beim späteren Erstellen Ihrer YAML-Bereitstellungsdatei müssen Sie die Umgebungsvariablen `MDB_CONNECTION_FACTORY`, `DEFAULT_SBNAMESPACE` und `SB_SAS_POLICY`, `SB_SAS_KEY`, `MDB_QUEUE`, `SB_QUEUE`, `MDB_TOPIC` und `SB_TOPIC` durch die entsprechenden Werte ersetzen.

#### <a name="review-wildfly-configuration"></a>Überprüfen der WildFly-Konfiguration

Lesen Sie das [WildFly-Administratorhandbuch](https://docs.wildfly.org/18/Admin_Guide.html), um sich über alle weiteren Schritte zu informieren, die vor der Migration ausgeführt werden müssen und in diesem Leitfaden nicht beschrieben sind.
