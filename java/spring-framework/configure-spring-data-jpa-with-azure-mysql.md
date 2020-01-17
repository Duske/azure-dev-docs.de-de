---
title: Verwenden von Spring Data-JPA mit Azure Database for MySQL
description: Hier erfahren Sie, wie Sie die Spring Data-JPA (Java-Persistenz-API) konfigurieren und mit einer Azure Database for MySQL-Instanz verwenden.
documentationcenter: java
ms.date: 11/27/2019
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: conceptual
ms.openlocfilehash: 927cc72a526651be71a7983a298ca2c6718f4546
ms.sourcegitcommit: 2ad3f7ce8c87331f8aff759ac2a3dc1b29581866
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022087"
---
# <a name="how-to-use-spring-data-jpa-with-azure-database-for-mysql"></a>Verwenden von Spring Data-JPA mit Azure Database for MySQL

In diesem Artikel wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data] verwendet, um Informationen in einer [Azure Database for MySQL-Datenbank](/azure/mysql/) mithilfe der [Java-Persistenz-API (JPA)](https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm) zu speichern und abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher
* [cURL](https://curl.haxx.se/) oder ein ähnliches HTTP-Hilfsprogramm zum Testen der Funktionalität
* Das Befehlszeilenprogramm [mysql](https://dev.mysql.com/downloads/)
* Einen [Git-Client](https://git-scm.com/downloads)

## <a name="create-a-azure-database-for-mysql-server"></a>Erstellen eines Azure Database for MySQL-Servers

### <a name="create-a-server-using-the-azure-portal"></a>Erstellen eines Servers über das Azure-Portal

> [!NOTE]
> 
> Ausführlichere Informationen zum Erstellen von MySQL-Datenbanken finden Sie unter [Erstellen eines Azure Database for MySQL-Servers im Azure-Portal](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich an.

1. Wählen Sie **+ Ressource erstellen** > **Datenbanken** > **Azure Database for MySQL** aus.

   ![Erstellen einer MySQL-Datenbank][MYSQL01]

1. Geben Sie Folgendes ein:

   - **Abonnement**: Geben Sie das Azure-Abonnement an, das Sie verwenden möchten.
   - **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   - **Servername**: Wählen Sie einen eindeutigen Namen für Ihren MySQL-Server aus. Dieser Name wird zum Erstellen eines vollqualifizierten Domänennamens (z. B. *wingtiptoysmysql.mysql.database.azure.com*) verwendet.
   - **Quelle auswählen**: Wählen Sie für dieses Tutorial `Blank` aus, um eine neue Datenbank zu erstellen.
   - **Serveradministratoranmeldung**: Geben Sie den Namen des Datenbankadministrators an.
   - **Kennwort** und **Kennwort bestätigen**: Geben Sie das Kennwort für den Datenbankadministrator an.
   - **Standort**: Geben Sie die nächstgelegene geografische Region für Ihre Datenbank an.
   - **Version**: Geben Sie aktuelle Datenbankversion an.

   ![Festlegen der Eigenschaften der MySQL-Datenbank][MYSQL02]

1. Nachdem Sie alle oben genannten Informationen eingegeben haben, klicken Sie auf **Bewerten + erstellen**.

### <a name="configure-a-firewall-rule-for-your-server-using-the-azure-portal"></a>Konfigurieren einer Firewallregel für Ihren Server im Azure-Portal

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Klicken Sie auf **Alle Ressourcen** und anschließend auf die MySQL-Datenbank, die Sie gerade erstellt haben.

1. Klicken Sie auf **Verbindungssicherheit**, und erstellen Sie im Abschnitt **Firewallregeln** eine neue Regel, indem Sie einen eindeutigen Namen für die Regel angeben, den Bereich der IP-Adressen eingeben, die Zugriff auf Ihre Datenbank benötigen, und anschließend auf **Speichern** klicken. (In dieser Übung wird die IP-Adresse Ihres Entwicklungscomputers verwendet, d. h. des Clients.  Sie können sie sowohl für **Start-IP-Adresse** als auch für **End-IP-Adresse** verwenden. Sehen Sie sich auch den Hinweis unter der Überschrift *Erstellen einer Datenbank mithilfe des Befehlszeilen-Hilfsprogramms mysql* an.)

   ![Konfigurieren der Verbindungssicherheit][MYSQL04]

### <a name="retrieve-the-connection-string-for-your-server-using-the-azure-portal"></a>Abrufen der Verbindungszeichenfolge für Ihren Server im Azure-Portal

1. Navigieren Sie zum Azure-Portal unter <https://portal.azure.com/>, und melden Sie sich an.

1. Klicken Sie auf **Alle Ressourcen** und anschließend auf die Azure Database for MySQL-Ressource, die Sie gerade erstellt haben.

1. Klicken Sie auf **Verbindungszeichenfolgen**, und kopieren Sie den Wert im Textfeld **JDBC**.

   ![Abrufen der JDBC-Verbindungszeichenfolge][MYSQL05]

### <a name="create-a-database-using-the-mysql-command-line-utility"></a>Erstellen einer Datenbank mit dem Befehlszeilenprogramm `mysql`

1. Öffnen Sie eine Befehlsshell, und stellen Sie wie im folgenden Beispiel gezeigt eine Verbindung mit Ihrem Azure Database for MySQL-Server her, indem Sie einen `mysql`-Befehl eingeben:

   ```shell
   mysql --host wingtiptoysmysql.mysql.database.azure.com --user wingtiptoysuser@wingtiptoysmysql -p
   ```
   Hierbei gilt:

   | Parameter | Beschreibung |
   |---|---|
   | `host` | Der vollqualifizierte MySQL-Servername, den Sie weiter oben in diesem Artikel festgelegt haben |
   | `user` | Der MySQL-Administratorname, den Sie weiter oben in diesem Artikel festgelegt haben, mit angefügtem gekürzten Servernamen |
   | `p` | Gibt an, dass auf eine Aufforderung zur Kennworteingabe gewartet werden soll |


   Ihr MySQL-Server sollte mit einer Anzeige antworten, die dem folgenden Beispiel ähnelt:

   ```shell
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64552
   Server version: 5.6.39.0 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```
   > Hinweis: Wenn Sie eine Fehlermeldung erhalten, dass der Server diese IP-Adresse nicht erkennt, wird die von Ihrem Client verwendete IP-Adresse in der Fehlermeldung angezeigt.  Gehen Sie zurück, und weisen Sie sie wie zuvor beschrieben zu: *Konfigurieren einer Firewallregel für Ihren Server im Azure-Portal*.

1. Erstellen Sie eine Datenbank mit dem Namen *mysqldb*, indem Sie wie im folgenden Beispiel gezeigt einen `mysql`-Befehl eingeben:

   ```SQL
   CREATE DATABASE mysqldb;
   ```

   Ihr MySQL-Server sollte mit einer Anzeige antworten, die dem folgenden Beispiel ähnelt:

   ```shell
   Query OK, 1 row affected (0.30 sec)
   ```

1. OPTIONAL: Sie können überprüfen, ob Ihre Datenbank erstellt wurde, indem Sie wie im folgenden Beispiel gezeigt einen `mysql`-Befehl eingeben:

   ```SQL
   SHOW DATABASES;
   ```

   Ihr MySQL-Server sollte mit einer Anzeige antworten, die dem folgenden Beispiel ähnelt:

   ```shell
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | mysqldb            |
   | performance_schema |
   | sys                |
   +--------------------+
   ```

1. Geben Sie `\q` ein, um das Befehlszeilenprogramm `mysql` zu beenden.

## <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

1. Öffnen Sie eine Befehlsshell, und klonen Sie das Beispielprojekt wie im folgenden Beispiel gezeigt mit einem Git-Befehl:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jpa-on-azure.git
   ```

1. Suchen Sie im Verzeichnis *resources* des Beispielprojekts nach der Datei *application.properties*, oder erstellen Sie diese Datei, wenn sie noch nicht vorhanden ist.

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie die folgenden Zeilen in der Datei hinzu, bzw. konfigurieren Sie diese Zeilen. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Werte, die Sie zuvor festgelegt haben:

   ```yaml
   spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
   spring.datasource.url=jdbc:mysql://wingtiptoysmysql.mysql.database.azure.com:3306/mysqldb?useSSL=true&requireSSL=false
   spring.datasource.username=wingtiptoysuser@wingtiptoysmysql
   spring.datasource.password=********
    ```
   Hierbei gilt:

   | Parameter | Beschreibung |
   |---|---|
   | `spring.jpa.database-platform` | Die JPA-Datenbankplattform |
   | `spring.datasource.url` | Die MySQL-JDBC-Zeichenfolge, die Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.datasource.username` | Der MySQL-Administratorname, den Sie weiter oben in diesem Artikel festgelegt haben, mit angefügtem gekürzten Servernamen |
   | `spring.datasource.password` | Das MySQL-Administratorkennwort, das Sie weiter oben in diesem Artikel festgelegt haben |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Verpacken und Testen der Beispielanwendung 

1. Erstellen Sie die Beispielanwendung mit Maven. Beispiel:

   ```shell
   mvn clean package -P mysql
   ```

1. Starten Sie die Beispielanwendung. Beispiel:

   ```shell
   java -jar target/spring-data-jpa-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Erstellen Sie wie in den folgenden Beispielen dargestellt über eine Eingabeaufforderung mit `curl` neue Datensätze:

   ```shell
   curl -s -d '{"name":"dog","species":"canine"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d '{"name":"cat","species":"feline"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```shell
   Added Pet(id=1, name=dog, species=canine).

   Added Pet(id=2, name=cat, species=feline).
   ```

1. Rufen Sie wie im folgenden Beispiel dargestellt über eine Eingabeaufforderung mit `curl` alle vorhandenen Datensätze ab:

   ```shell
   curl -s http://localhost:8080/pets
   ```
    
   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```json
   [{"id":1,"name":"dog","species":"canine"},{"id":2,"name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie eine Java-Beispielanwendung erstellt, die Spring Data verwendet, um Informationen in einer Azure Database for MySQL-Instanz mithilfe der Java-Persistenz-API (JPA) zu speichern und abzurufen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

<!-- URL List -->

[Azure für Java-Entwickler]: /azure/java/
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[MYSQL01]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png
[MYSQL02]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png
[MYSQL04]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-04.png
[MYSQL05]: media/configure-spring-data-jpa-with-azure-mysql/create-mysql-05.png
