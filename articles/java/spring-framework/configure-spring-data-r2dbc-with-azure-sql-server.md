---
title: Verwenden von Spring Data R2DBC mit Azure SQL-Datenbank
description: Hier erfahren Sie, wie Sie Spring Data R2DBC mit einer Azure SQL-Datenbank verwenden.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 057f261de707adac2ab3ef9ac52834a9848362b6
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369997"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Verwenden von Spring Data R2DBC mit Azure SQL-Datenbank

In diesem Thema wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) verwendet, um Informationen in [Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/) mithilfe der R2DBC-Implementierung für Microsoft SQL Server aus dem [GitHub-Repository „r2dbc-mssql“](https://github.com/r2dbc/r2dbc-mssql) zu speichern und abzurufen.

[R2DBC](https://r2dbc.io/) ermöglicht die Nutzung reaktiver APIs mit herkömmlichen relationalen Datenbanken. Sie können es zusammen mit Spring WebFlux verwenden, um vollständig reaktive Spring Boot-Anwendungen zu erstellen, die nicht blockierende APIs verwenden. Dies bietet bessere Skalierbarkeit als der klassische „Ein-Thread-pro-Verbindung“-Ansatz.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Richten Sie zunächst mithilfe der folgenden Befehle einige Umgebungsvariablen ein:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name für den Azure SQL-Datenbank-Server. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<AZ_SQL_SERVER_PASSWORD>`: Das Kennwort Ihres Azure SQL-Datenbank-Servers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Spring Boot-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie als Nächstes eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Wir verwenden das in [Azure Cloud Shell](https://shell.azure.com/) standardmäßig installierte Dienstprogramm `jq`, um JSON-Daten anzuzeigen und ihre Lesbarkeit zu erhöhen.
> Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-sql-database-instance"></a>Erstellen einer Azure SQL-Datenbankinstanz

Sie erstellen zuerst einen verwalteten Azure SQL-Datenbank-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von Azure SQL-Datenbank-Servern finden Sie unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](/azure/sql-database/sql-database-single-database-get-started).

Führen Sie in der [Azure Cloud Shell](https://shell.azure.com/) das folgende Skript aus:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

Mit diesem Befehl wird ein Azure SQL-Datenbank-Server erstellt.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Konfigurieren einer Firewallregel für den Azure SQL-Datenbank-Server

Azure SQL-Datenbankinstanzen sind standardmäßig gesichert. Sie besitzen eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie Folgendes ausführen:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Konfigurieren einer Azure SQL-Datenbank

Der von Ihnen zuvor erstellte Azure SQL-Datenbank-Server ist leer. Er verfügt über keine Datenbank, die Sie mit der Spring Boot-Anwendung verwenden können. Erstellen Sie eine neue Datenbank mit dem Namen `demo`:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Erstellen der Anwendung mithilfe von Spring Initializr

Generieren der Anwendung an der Befehlszeile durch Eingeben von:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RC1 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Hinzufügen der reaktiven Treiberimplementierung für Azure SQL-Datenbank

Öffnen Sie die Datei *pom.xml* des generierten Projekts, um den reaktiven Azure SQL-Datenbank-Treiber aus dem [GitHub-Repository „r2dbc-mssql“](https://github.com/r2dbc/r2dbc-mssql) hinzuzufügen.

Fügen Sie nach der Abhängigkeit `spring-boot-starter-webflux` den folgenden Codeausschnitt hinzu:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Konfigurieren von Spring Boot für die Verwendung von Azure SQL-Datenbank

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie Folgendes hinzu:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

- Ersetzen Sie die beiden Variablen `$AZ_DATABASE_NAME` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.
- Ersetzen Sie die Variable `$AZ_SQL_SERVER_PASSWORD` durch den Wert, den Sie zu Beginn dieses Artikels konfiguriert haben.

> [!NOTE]
> Zur Verbesserung der Leistung ist die `spring.r2dbc.url`-Eigenschaft so konfiguriert, dass ein Verbindungspool mit [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool) verwendet wird.

Sie sollten Ihre Anwendung nun mithilfe des angegebenen Maven-Wrappers starten können:

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Anwendung bei der ersten Ausführung:

[![Die ausgeführte Anwendung](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Erstellen des Datenbankschemas

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

Halten Sie die ausgeführte Anwendung an, und starten Sie sie erneut. Die Anwendung verwendet nun die Datenbank `demo`, die Sie zuvor erstellt haben, und erstellt darin eine `todo`-Tabelle.

```bash
./mvnw spring-boot:run
```

Hier sehen Sie einen Screenshot der Datenbanktabelle während der Erstellung:

[![Erstellung der Datenbanktabelle](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png#lightbox)

## <a name="code-the-application"></a>Codieren der Anwendung

Fügen Sie als Nächstes den Java-Code hinzu, der R2DBC zum Speichern und Abrufen von Daten auf bzw. aus Ihrem Azure SQL-Datenbank-Server verwendet.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen:

[![Testen mit cURL](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png#lightbox)

Glückwunsch! Sie haben eine vollständig reaktive Spring Boot-Anwendung erstellt, die R2DBC zum Speichern und Abrufen von Daten in bzw. aus Azure SQL-Datenbank verwendet.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu Spring Data R2DBC finden Sie in der [Referenzdokumentation](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) zu Spring.

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler](/azure/developer/java/) und [Working with Azure DevOps and Java](/azure/devops/) (Arbeiten mit Azure DevOps und Java).
