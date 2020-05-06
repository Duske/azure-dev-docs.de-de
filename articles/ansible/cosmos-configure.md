---
title: 'Tutorial: Konfigurieren von Azure Cosmos DB-Konten mit Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Erstellen und Konfigurieren einer Azure Cosmos DB-Instanz verwenden.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Cosmos DB, Datenbank
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 471b53e71683311a99a811dbc14e4439a81f5948
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743359"
---
# <a name="tutorial-configure-azure-cosmos-db-accounts-using-ansible"></a>Tutorial: Konfigurieren von Azure Cosmos DB-Konten mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[Azure Cosmos DB](/azure/cosmos-db/) ist ein Datenbankdienst, der mehrere Datenbanktypen unterstützt. Zu diesen Datenbanktypen gehören „Dokument“, „Schlüssel/Wert“, „breite Spalten“ und „Graph“. Ansible ermöglicht die Automatisierung der Bereitstellung und Konfiguration von Ressourcen in Ihrer Umgebung.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Kontos
> * Abrufen der Kontoschlüssel
> * Löschen des Kontos

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Erstellen eines zufälligen Postfix

Mit dem Playbook-Beispielcode wird ein zufälliger Postfix erstellt. Der Postfix wird als Teil des Namens für das Azure Cosmos DB-Konto verwendet.

```yml
  - hosts: localhost
    tasks:
      - name: Prepare random postfix
        set_fact:
          rpfx: "{{ 1000 | random }}"
        run_once: yes
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen 

Der Ausschnitt aus dem Beispielplaybook erstellt eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und Subnetzes

Mit dem folgenden Code werden ein virtuelles Netzwerk und ein Subnetz für das Azure Cosmos DB-Konto erstellt:

```yml
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name }}"
      address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
      dns_servers:
        - 127.0.0.1
        - 127.0.0.3

  - name: Add subnet
    azure_rm_subnet:
      name: "{{ subnet_name }}"
      virtual_network_name: "{{ vnet_name }}"
      resource_group: "{{ resource_group }}"
      address_prefix_cidr: "10.1.0.0/24"
```

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Mit dem folgenden Code wird das Cosmos DB-Konto erstellt:

```yml
  - name: Create instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      location: eastus
      kind: global_document_db
      geo_rep_locations:
        - name: eastus
          failover_priority: 0
        - name: westus
          failover_priority: 1
      database_account_offer_type: Standard
      is_virtual_network_filter_enabled: yes
      virtual_network_rules:
        - subnet:
            resource_group: "{{ resource_group }}"
            virtual_network_name: "{{ vnet_name }}"
            subnet_name: "{{ subnet_name }}"
          ignore_missing_vnet_service_endpoint: yes
      enable_automatic_failover: yes
```

Es dauert einige Minuten, bis die Kontoerstellung abgeschlossen ist.

## <a name="retrieve-the-keys"></a>Abrufen der Schlüssel

Mit dem folgenden Code werden die Schlüssel abgerufen, die in Ihrer App verwendet werden.

```yml
  - name: Get Cosmos DB Account facts with keys
    azure_rm_cosmosdbaccount_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      retrieve_keys: all
    register: output

  - name: Display Cosmos DB Acccount facts output
    debug:
      var: output
```

## <a name="delete-the-azure-cosmos-db-account"></a>Löschen des Azure Cosmos DB-Kontos

Im letzten Codeausschnitt wird veranschaulicht, wie Sie ein Azure Cosmos DB-Konto löschen.

```yml
  - name: Delete instance of Cosmos DB Account
    azure_rm_cosmosdbaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ cosmosdbaccount_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Abrufen der Beispielplaybooks

Das vollständige Beispielplaybook kann auf zwei Arten bezogen werden:
- [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/cosmosdb_create.yml), und speichern Sie es als `cosmosdb.yml`.
- Erstellen Sie eine neue Datei mit dem Namen `cosmosdb.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
#  roles:
#    - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cosmosdbaccount_name: cosmos{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name }}"
        address_prefixes_cidr:
          - 10.1.0.0/16
          - 172.100.0.0/16
        dns_servers:
          - 127.0.0.1
          - 127.0.0.3

    - name: Add subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"

    - name: Create instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        location: eastus
        kind: global_document_db
        geo_rep_locations:
          - name: eastus
            failover_priority: 0
          - name: westus
            failover_priority: 1
        database_account_offer_type: Standard
        is_virtual_network_filter_enabled: yes
        virtual_network_rules:
          - subnet:
              resource_group: "{{ resource_group }}"
              virtual_network_name: "{{ vnet_name }}"
              subnet_name: "{{ subnet_name }}"
            ignore_missing_vnet_service_endpoint: yes
        enable_automatic_failover: yes

    - name: Get Cosmos DB Account facts with keys
      azure_rm_cosmosdbaccount_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        retrieve_keys: all
      register: output

    - name: Display Cosmos DB Account facts output
      debug:
        var: output

    - name: Delete instance of Cosmos DB Account
      azure_rm_cosmosdbaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ cosmosdbaccount_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

In diesem Abschnitt führen Sie das Playbook aus, um verschiedene in diesem Artikel gezeigte Features zu testen.

Nehmen Sie vor dem Ausführen des Playbooks die folgenden Änderungen vor:
- Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.
- Stellen Sie sicher, dass „cosmosdbaccount_name“ nur Kleinbuchstaben enthält und global eindeutig ist.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cosmosdb.yml
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie den folgenden Code als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)