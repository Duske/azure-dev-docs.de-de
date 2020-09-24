---
title: Bereitstellen einer Azure MySQL-Datenbank mithilfe der Azure SDK-Bibliotheken
description: Hier erfahren Sie, wie Sie die Verwaltungsbibliotheken in den Azure SDK-Bibliotheken für Python verwenden, um eine Azure MySQL-, PostgresSQL- oder MariaDB-Datenbank bereitzustellen.
ms.date: 06/02/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 17848311fa713fbe480609d2d49481b5f343eff4
ms.sourcegitcommit: b03cb337db8a35e6e62b063c347891e44a8a5a13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91110542"
---
# <a name="example-use-the-azure-libraries-to-provision-a-database"></a>Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Datenbank

In diesem Beispiel wird gezeigt, wie Sie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwenden, um eine Azure MySQL-Datenbank bereitzustellen. Darüber hinaus finden Sie hier ein einfaches Skript zum Abfragen der Datenbank unter Verwendung der Bibliothek „mysql-connector“ (nicht im Azure SDK enthalten). ([Äquivalente Azure CLI-Befehle](#for-reference-equivalent-azure-cli-commands) finden Sie weiter unten in diesem Artikel. Wenn Sie die Azure-Portal bevorzugen, finden Sie weitere Informationen unter [Erstellen eines PostgreSQL-Servers](/azure/postgresql/quickstart-create-server-database-portal) oder [Erstellen eines MariaDB-Servers](/azure/mariadb/quickstart-create-mariadb-server-database-using-azure-portal).)

PostgreSQL- und MariaDB-Datenbanken können auf ähnliche Weise bereitgestellt werden.

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Installieren der erforderlichen Azure-Bibliothekspakete

Erstellen Sie eine Datei namens *requirements.txt* mit folgendem Inhalt:

```text
azure-mgmt-resource
azure-mgmt-rdbms
azure-cli-core
mysql
mysql-connector
```

Installieren Sie in einem Terminal oder einer Eingabeaufforderung bei aktivierter virtueller Umgebung die Voraussetzungen:

```cmd
pip install -r requirements.txt
```

> [!NOTE]
> Wenn Sie unter Windows versuchen, die MySQL-Bibliothek in einer 32-Bit-Python-Bibliothek zu installieren, tritt ein Fehler für die Datei *mysql.h* auf. Installieren Sie in diesem Fall eine 64-Bit-Version von Python, und versuchen Sie es noch mal.

## <a name="3-write-code-to-provision-the-database"></a>3: Schreiben von Code für die Datenbankbereitstellung

Erstellen Sie eine Python-Datei mit dem Namen *provision_db.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# For PostgreSQL, use the PostgreSQLManagement class.
# For MariaDB, use the MariaDBManagement class.
from azure.mgmt.rdbms.mysql import MySQLManagementClient

from azure.mgmt.rdbms.mysql.models import ServerForCreate, ServerPropertiesForDefaultCreate, ServerVersion

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-DB-rg'
LOCATION = "westus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


# Step 2: Provision the database server

# We use a random number to create a reasonably unique database server name.
# If you've already provisioned a database and need to re-run the script, set
# the DB_SERVER_NAME environment variable to that name instead.
#
# Also set DB_USER_NAME and DB_USER_PASSWORD variables to avoid using the defaults.

db_server_name = os.environ.get("DB_SERVER_NAME", f"PythonAzureExample-MySQL-{random.randint(1,100000):05}")
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

# Obtain the management client object
mysql_client = get_client_from_cli_profile(MySQLManagementClient)

# Provision the server and wait for the result
poller = mysql_client.servers.create(RESOURCE_GROUP_NAME,
    db_server_name,
    ServerForCreate(
        location=LOCATION,
        properties=ServerPropertiesForDefaultCreate(
            administrator_login=db_admin_name,
            administrator_login_password=db_admin_password,
            version=ServerVersion.five_full_stop_seven
        )
    )
)

server = poller.result()

print(f"Provisioned MySQL server {server.name}")

# Step 3: Provision a firewall rule to allow the local workstation to connect

RULE_NAME = "allow_ip"
ip_address = os.environ["PUBLIC_IP_ADDRESS"]

# For the above code, create an environment variable named PUBLIC_IP_ADDRESS that
# contains your workstation's public IP address as reported by a site like
# https://whatismyipaddress.com/.

# Provision the rule and wait for completion
poller = mysql_client.firewall_rules.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, RULE_NAME,
    ip_address,  # Start ip range
    ip_address   # End ip range
)

firewall_rule = poller.result()

print(f"Provisioned firewall rule {firewall_rule.name}")

# Step 4: Provision a database on the server

DB_NAME = "example-db1"

poller = mysql_client.databases.create_or_update(RESOURCE_GROUP_NAME,
    db_server_name, DB_NAME)

db_result = poller.result()

print(f"Provisioned MySQL database {db_result.name} with ID {db_result.id}")
```

Für dieses Beispiel muss eine Umgebungsvariable mit dem Namen `PUBLIC_IP_ADDRESS` und der IP-Adresse Ihrer Arbeitsstation erstellt werden.

Dieser Code verwendet die CLI-basierten Authentifizierungsmethoden (`get_client_from_cli_profile`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden, sollten Sie stattdessen `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode verwenden, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md) beschrieben.

### <a name="reference-links-for-classes-used-in-the-code"></a>Referenzlinks für im Code verwendete Klassen

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [MySQLManagementClient (azure.mgmt.rdbms.mysql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.mysqlmanagementclient?view=azure-python)
- [ServerForCreate (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverforcreate?view=azure-python)
- [ServerPropertiesForDefaultCreate (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverpropertiesfordefaultcreate?view=azure-python)
- [ServerVersion (azure.mgmt.rdbms.mysql.models)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mysql.models.serverversion?view=azure-python)

Siehe auch:
    - [PostgreSQLManagementClient (azure.mgmt.rdbms.postgresql)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.postgresql.postgresqlmanagementclient?view=azure-python)
    - [MariaDBManagementClient (azure.mgmt.rdbms.mariadb)](/python/api/azure-mgmt-rdbms/azure.mgmt.rdbms.mariadb.mariadbmanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4: Führen Sie das Skript aus.

```cmd
python provision_db.py
```

## <a name="5-insert-a-record-and-query-the-database"></a>5: Einfügen eines Datensatzes und Abfragen der Datenbank

Erstellen Sie eine Datei namens *use_db.py* mit folgendem Code. Beachten Sie die Abhängigkeit von den Umgebungsvariablen `DB_SERVER_NAME`, `DB_ADMIN_NAME` und `DB_ADMIN_PASSWORD`. Diese müssen mit den Werten aus dem Bereitstellungscode aufgefüllt werden.

Dieser Code funktioniert nur für MySQL. Für PostgreSQL und MariaDB müssen andere Bibliotheken verwendet werden.

```python
import os
import mysql.connector

db_server_name = os.environ["DB_SERVER_NAME"]
db_admin_name = os.environ.get("DB_ADMIN_NAME", "azureuser")
db_admin_password = os.environ.get("DB_ADMIN_PASSWORD", "ChangePa$$w0rd24")

DB_NAME = "example-db"

connection = mysql.connector.connect(user=f"{db_admin_name}@{db_server_name}",
    password=db_admin_password, host=f"{db_server_name}.mysql.database.azure.com", port=3306,
    database=DB_NAME)

cursor = connection.cursor()

table_name = "ExampleTable1"

sql_create = f"CREATE TABLE {table_name} (name varchar(255), code int)"

cursor.execute(sql_create)
print(f"Successfully created table {table_name}")

sql_insert = f"INSERT INTO {table_name} (name, code) VALUES ('Azure', 1)"
insert_data = "('Azure', 1)"

cursor.execute(sql_insert)
print("Successfully inserted data into table")

sql_select_values= f"SELECT * FROM {table_name}"

cursor.execute(sql_select_values)
row = cursor.fetchone()

while row:
    print(str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()

connection.commit()
```

In diesem Code wird die API „mysql.connector“ verwendet. Der einzige Azure-spezifische Teil ist die vollständige Hostdomäne für den MySQL-Server (mysql.database.azure.com).

Führen Sie anschließend den Code aus:

```cmd
python use_db.py
```

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonAzureExample-DB-rg  --no-wait
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel bereitgestellten Ressourcen nicht behalten müssen, und Sie in Ihrem Abonnement laufende Gebühren vermeiden möchten.

Sie können auch die [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-)-Methode verwenden, um eine Ressourcengruppe aus dem Code zu löschen.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

Mit den folgenden Azure CLI-Befehlen können die gleichen Bereitstellungsschritte ausgeführt werden wie mit dem Python-Skript. Verwenden Sie für eine PostgreSQL-Datenbank [`az postgres`](/cli/azure/postgres?view=azure-cli-latest) und für MariaDB [`az mariadb`](/cli/azure/mariadb?view=azure-cli-latest).

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 ^
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 ^
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-DB-rg

az mysql server create -l westus -g PythonAzureExample-DB-rg -n PythonAzureExample-MySQL-12345 \
    -u azureuser -p ChangePa$$w0rd24 --sku-name B_Gen5_1

# Change the IP address to the public IP address of your workstation, that is, the address shown
# by a site like https://whatismyipaddress.com/. 

az mysql server firewall-rule create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 \
    -n allow_ip --start-ip-address 10.11.12.13 --end-ip-address 10.11.12.13

az mysql db create -g PythonAzureExample-DB-rg --server PythonAzureExample-MySQL-12345 -n example-db
```

---

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage-use.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
