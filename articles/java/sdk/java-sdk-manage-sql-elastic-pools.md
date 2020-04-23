---
title: Verwalten von Pools für elastische SQL-Datenbank-Instanzen mit Java | Microsoft-Dokumentation
description: Beispielcode zum Erstellen und Konfigurieren von Azure SQL-Datenbanken mithilfe des Azure SDKs für Java
author: rloutlaw
ms.assetid: 9b461de8-46bc-4650-8e9e-59531f4e2a53
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 8bbc91b093ff9ff4f777008e0b78c9650e3a4989
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81674136"
---
# <a name="manage-azure-sql-databases-in-elastic-pools-from-your-java-applications"></a>Verwalten von Azure SQL-Datenbanken in Pools für elastische Datenbanken über Ihre Java-Anwendungen

[Dieses Beispiel](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool) erstellt einen SQL-Datenbank-Server mit einem [Pool für elastische Datenbanken](/azure/sql-database/sql-database-elastic-pool) zur Verwaltung und Skalierung von Ressourcen für mehrere Datenbanken in einem einzelnen Plan.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Erstellen Sie eine [Authentifizierungsdatei](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md), und legen Sie die Umgebungsvariable `AZURE_AUTH_LOCATION` mit dem vollständigen Pfad zur Datei auf Ihrem Computer fest. Führen Sie dann Folgendes aus:

```
git clone https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool
cd sql-database-java-manage-sql-dbs-in-elastic-pool
mvn clean compile exec:java
```

[Sehen Sie sich das vollständige Codebeispiel auf GitHub an.](https://github.com/Azure-Samples/sql-database-java-manage-sql-dbs-in-elastic-pool)

## <a name="authenticate-with-azure"></a>Authentifizieren über Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-sql-database-server-with-an-elastic-pool"></a>Erstellen eines SQL-Datenbank-Servers mit einem Pool für elastische Datenbanken

```java
SqlServer sqlServer = azure.sqlServers().define(sqlServerName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .withAdministratorLogin(administratorLogin)
                    .withAdministratorPassword(administratorPassword)
                    // Use ElasticPoolEditions.STANDARD as the edition
                    // and create two databases
                    .withNewElasticPool(elasticPoolName, ElasticPoolEditions.STANDARD, 
                        database1Name, database2Name)
                    .create();
```

Aktuelle Editionswerte finden Sie in der [ElasticPoolEditions-Klassenreferenz](/java/api/com.microsoft.azure.management.sql.elasticpooleditions). Die Merkmale der Editionsressourcen können Sie in der [Dokumentation zu Pools für elastische SQL-Datenbank-Instanzen](/azure/sql-database/sql-database-elastic-pool) vergleichen. 

## <a name="change-database-transaction-unit-dtu-settings-in-an-elastic-pool"></a>Ändern der DTU-Einstellungen (Database Transaction Unit, Datenübertragungseinheit) in einem Pool für elastische Datenbanken

```java
// Set an pool of 200 eDTUs to share between the databases
// and ensure that a database always has 10 DTUs available but never uses more than 50
elasticPool = elasticPool.update()
                    .withDtu(200)
                    .withDatabaseDtuMin(10)
                    .withDatabaseDtuMax(50)
                    .apply();
```

Weitere Informationen zum Zuweisen von Ressourcen zu SQL-Datenbanken finden Sie in der [Dokumentation zu DTUs und eDTUs](/azure/sql-database/sql-database-what-is-a-dtu).

## <a name="create-a-new-database-and-add-it-to-an-elastic-pool"></a>Erstellen einer neuen Datenbank in einem Pool für elastische Datenbanken

```java
// Add a newly created database to the elastic pool
SqlDatabase anotherDatabase = sqlServer.databases().define(anotherDatabaseName).create();
elasticPool.update().withExistingDatabase(anotherDatabase).apply();            
```

In der ersten Anweisung erstellt die API `anotherDatabase` im [S0-Tarif](/azure/sql-database/sql-database-service-tiers). Durch Verschieben von `anotherDatabase` in den Pool für elastische Datenbanken werden die Datenbankressourcen auf der Grundlage der Pooleinstellungen zugewiesen.

## <a name="remove-a-database-from-an-elastic-pool"></a>Entfernen einer Datenbank aus einem Pool für elastische Datenbanken
```java
// Assign an edition since the database resources are no longer managed in the pool 
anotherDatabase = anotherDatabase.update()
                     .withoutElasticPool()
                     .withEdition(DatabaseEditions.STANDARD)
                     .apply();
```

An `withEdition()` übergebbare Werte finden Sie in der [DatabaseEditions-Klassenreferenz](/java/api/com.microsoft.azure.management.sql.databaseeditions).

## <a name="list-current-database-activities-in-an-elastic-pool"></a>Auflisten der aktuellen Datenbankaktivitäten in einem Pool für elastische Datenbanken
```java
// get a list of in-flight elastic operations on databases in the pool and log them 
for (ElasticPoolDatabaseActivity databaseActivity : elasticPool.listDatabaseActivities()) {
    System.out.println("Database " + databaseActivity.databaseName() 
        + " performing operation " + databaseActivity.operation() + 
        " with objective " + databaseActivity.requestedServiceObjective());
}
```

Zu Datenbankaktivitäten zählen das Verschieben in einen oder aus einem Pool für elastische Datenbanken sowie das Aktualisieren von Datenbanken in einem Pool für elastische Datenbanken.


## <a name="list-databases-in-an-elastic-pool"></a>Auflisten von Datenbanken in einem Pool für elastische Datenbanken
```java
// Log a list of databases in the elastic pool 
for (SqlDatabase databaseInServer : elasticPool.listDatabases()) {
    System.out.println(databaseInServer.name());
}
```

Sehen Sie sich die Methoden in [com.microsoft.azure.management.sql.SqlDatabase](/java/api/com.microsoft.azure.management.sql.sqldatabase) an, um detailliertere Datenbankabfragen auszuführen.

## <a name="delete-an-elastic-pool"></a>Löschen eines Pools für elastische Datenbanken
```java
sqlServer.elasticPools().delete(elasticPoolName);
```

Der zu löschende Pool für elastische Datenbanken muss leer sein.

## <a name="sample-code-summary"></a>Zusammenfassung des Beispielcodes

Das Beispiel erstellt eine SQL Server-Instanz mit zwei Datenbanken, die in einem einzelnen Pool für elastische Datenbanken verwaltet werden. Die Ressourcenlimits des Pools für elastische Datenbanken werden aktualisiert, und dem Pool werden anschließend zusätzliche Datenbanken hinzugefügt. Danach liest der Code die Konfiguration und den Zustand des Pools für elastische Datenbanken. 

Vor dem Beenden des Beispiels werden alle erstellten Ressourcen wieder gelöscht.

| Im Beispiel verwendete Klasse | Notizen |
|-------|-------|
| [SqlServer](/java/api/com.microsoft.azure.management.sql.sqlserver) | Durch die Fluent-Kette `azure.sqlServers().define()...create()` erstellter SQL-Datenbankserver in Azure. Stellt Methoden zum Erstellen und Verwenden von Datenbanken und von Pools für elastische Datenbanken bereit. 
| [SqlDatabase](/java/api/com.microsoft.azure.management.sql.sqldatabase) | Clientseitiges Objekt, das eine SQL-Datenbank darstellt. Erstellt durch `sqlServer().define()...create()`. 
| [DatabaseEditions](/java/api/com.microsoft.azure.management.sql.databaseeditions) | Konstante statische Felder zum Festlegen von Datenbankressourcen beim Erstellen einer Datenbank außerhalb eines Pools für elastische Datenbanken oder beim Verschieben einer Datenbank aus einem Pool für elastische Datenbanken.  
| [SqlElasticPool](/java/api/com.microsoft.azure.management.sql.sqlelasticpool) | Wird auf der Grundlage des Abschnitts `withNewElasticPool()` der Fluent-Kette erstellt, die die SQL Server-Instanz in Azure erstellt hat. Stellt Methoden zum Festlegen der Ressourcenlimits bereit, die für Datenbanken, die im Pool für elastische Datenbanken ausgeführt werden, und für den eigentlichen Pool für elastische Datenbanken gelten. 
| [ElasticPoolEditions](/java/api/com.microsoft.azure.management.sql.elasticpooleditions) | Klasse konstanter Felder zum Definieren der verfügbaren Ressourcen für einen Pool für elastische Datenbanken. Tarifdetails finden Sie in der [Dokumentation zu Pools für elastische SQL-Datenbank-Instanzen](/azure/sql-database/sql-database-elastic-pool). 
| [ElasticPoolDatabaseActivity](/java/api/com.microsoft.azure.management.sql.elasticpooldatabaseactivity) | Abgerufen aus `SqlElasticPool.listDatabaseActivities()`. Jedes Objekt dieses Typs stellt eine Aktivität dar, die für eine Datenbank im Pool für elastische Datenbanken ausgeführt wurde.
| [ElasticPoolActivity](/java/api/com.microsoft.azure.management.sql.elasticpoolactivity) | Abgerufen in einer Liste aus `SqlElasticPool.listActivities()`. Jedes Objekt in der Liste stellt eine Aktivität dar, die für den Pool für elastische Datenbanken (nicht für die Datenbanken im Pool) ausgeführt wurde.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next-steps](includes/java-next-steps.md)]