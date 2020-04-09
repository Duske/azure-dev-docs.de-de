---
title: 'Tutorial: Konfigurieren von Datenbanken in Azure Database for MySQL mithilfe von Ansible'
description: Hier wird beschrieben, wie Sie mithilfe von Ansible einen Azure Database for MySQL-Server erstellen und konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Playbook, MySQL, Datenbank
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9cd574417733518b993bb242c2c168aba338e34a
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741548"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Tutorial: Konfigurieren von Datenbanken in Azure Database for MySQL mit Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure Database for MySQL](/azure/mysql/overview) ist ein relationaler Datenbankdienst, der auf der MySQL Community-Edition basiert. Mit Azure Database for MySQL können Sie MySQL-Datenbanken in Ihren Web-Apps verwalten.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines MySQL-Servers
> * Erstellen einer MySQL-Datenbank
> * Konfigurieren einer Firewallregel, damit eine externe App eine Verbindung mit Ihrem Server herstellen kann
> * Herstellen einer Verbindung mit Ihrem MySQL-Server über Azure Cloud Shell
> * Abfragen Ihrer verfügbaren MySQL-Server
> * Auflisten aller Datenbanken Ihrer verbundenen Server

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Mit dem Playbookcode in diesem Abschnitt wird eine Azure-Ressourcengruppe erstellt. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.  

Speichern Sie das folgende Playbook als `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Eine Ressourcengruppe mit dem Namen `myResourceGroup` wird erstellt.
* Die Ressourcengruppe wird am Standort `eastus` erstellt:

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Erstellen eines MySQL-Servers und einer Datenbank

Mit dem Playbookcode in diesem Abschnitt werden ein MySQL-Server und eine Azure Database for MySQL-Instanz erstellt. Der neue MySQL-Server ist ein Server vom Typ „Gen 5 Basic“ mit einem V-Kern und dem Namen `mysqlserveransible`. Die Datenbankinstanz hat den Namen `mysqldbansible`.

Weitere Informationen zu Tarifen finden Sie unter [Azure Database for MySQL – Tarife](/azure/mysql/concepts-pricing-tiers). 

Speichern Sie das folgende Playbook als `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Im Abschnitt `vars` muss der Wert von `mysqlserver_name` eindeutig sein.
* Ersetzen Sie im Abschnitt `vars` den Platzhalter `<server_admin_password>` durch ein Kennwort.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Konfigurieren einer Firewallregel

Eine Firewallregel auf Serverebene ermöglicht es einer externen App, über die Firewall des Azure MySQL-Diensts eine Verbindung mit Ihrem Server herzustellen. Beispiele für externe Apps sind das Befehlszeilentool `mysql` und MySQL Workbench.

Mit dem Playbookcode in diesem Abschnitt wird eine Firewallregel mit dem Namen `extenalaccess` erstellt, die Verbindungen von allen externen IP-Adressen ermöglicht. 

Speichern Sie das folgende Playbook als `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Ersetzen Sie im Abschnitt „vars“ die Elemente `startIpAddress` und `endIpAddress`. Verwenden Sie den Bereich mit den IP-Adressen, die dem Bereich entsprechen, von dem aus Sie die Verbindung herstellen.
* Die Kommunikation für Verbindungen mit Azure-Datenbank für MySQL erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.
* Im Playbook wird das Modul `azure_rm_resource` verwendet, das die direkte Nutzung der REST-API ermöglicht.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Herstellen einer Verbindung mit dem Server

In diesem Abschnitt verwenden Sie Azure Cloud Shell, um eine Verbindung mit dem zuvor erstellten Server herzustellen.

1. Öffnen Sie „shell.azure.com“, indem Sie Folgendes auswählen:

   [![Start einbetten](https://shell.azure.com/images/launchcloudshell.png "Starten von Azure Cloud Shell")](https://shell.azure.com)

1. Geben Sie den folgenden Code ein:

    ```sql
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Geben Sie an der Eingabeaufforderung den folgenden Befehl zum Abfragen des Serverstatus ein:

    ```sql
    mysql> status
    ```
    
    Wenn keine Fehler auftreten, wird in etwa die folgende Ausgabe angezeigt:
    
    ```output
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Abfragen von MySQL-Servern

Mit dem Playbookcode in diesem Abschnitt werden MySQL-Server in `myResourceGroup` abgefragt und die Datenbanken der gefundenen Server aufgelistet.

Speichern Sie das folgende Playbook als `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook mysql_query.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

Darüber hinaus wird die folgende Ausgabe für die MySQL-Datenbank angezeigt:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie das folgende Playbook als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)