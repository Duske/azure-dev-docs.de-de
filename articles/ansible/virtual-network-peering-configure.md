---
title: 'Tutorial: Konfigurieren des Peerings virtueller Azure-Netzwerke mit Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Verbinden von virtuellen Netzwerken per VNET-Peering verwenden.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Netzwerk, Peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 4dfda10693ad86131ec529d7ac06070098a40a5e
ms.sourcegitcommit: 499f7275446f006fa43c4eff3b1f0d001e9a98d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84453691"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Tutorial: Konfigurieren des Azure-VNET-Peerings mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

Das [Peering virtueller Netzwerke (VNET-Peering)](/azure/virtual-network/virtual-network-peering-overview) ermöglicht das nahtlose Verbinden von zwei virtuellen Azure-Netzwerken. Nach dem Peering werden die beiden virtuellen Netzwerke für Verbindungszwecke als einzelnes Element angezeigt. 

Datenverkehr wird zwischen virtuellen Computern in demselben virtuellen Netzwerk über private IP-Adressen weitergeleitet. Auf ähnliche Weise wird Datenverkehr zwischen VMs in einem virtuellen Netzwerk mit Peering über die Microsoft-Backboneinfrastruktur weitergeleitet. So können VMs verschiedener virtueller Netzwerke miteinander kommunizieren.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen zweier virtueller Netzwerke
> * Herstellen einer Peerverbindung zwischen den beiden virtuellen Netzwerken
> * Löschen des Peerings zwischen den beiden Netzwerken

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Erstellen von zwei Ressourcengruppen

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Erstellen von zwei Ressourcengruppen 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Erstellen des ersten virtuellen Netzwerks

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Erstellen eines virtuellen Netzwerks
- Erstellen eines Subnetzes innerhalb des virtuellen Netzwerks

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>Erstellen des zweiten virtuellen Netzwerks

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Erstellen eines virtuellen Netzwerks
- Erstellen eines Subnetzes innerhalb des virtuellen Netzwerks

```yml
  - name: Create second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>Herstellen einer Peerverbindung zwischen den beiden virtuellen Netzwerken

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Initialisieren des VNET-Peerings
- Einrichten des Peerings für die beiden zuvor erstellten virtuellen Netzwerke

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>Löschen des VNET-Peerings

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Löschen des Peerings zwischen den beiden zuvor erstellten virtuellen Netzwerken

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Abrufen der Beispielplaybooks

Das vollständige Beispielplaybook kann auf zwei Arten bezogen werden:

- [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml), und speichern Sie es als `vnet_peering.yml`.
- Erstellen Sie eine neue Datei mit dem Namen `vnet_peering.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Create second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

Der Playbook-Beispielcode in diesem Abschnitt wird verwendet, um verschiedene Features dieses Tutorials zu testen.

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.

Führen Sie das Playbook aus, indem Sie den Befehl „ansible-playbook“ verwenden:

```bash
ansible-playbook vnet_peering.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Löschen der beiden zuvor erstellten Ressourcengruppen

Speichern Sie das folgende Playbook als `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Ersetzen Sie den Platzhalter `{{ resource_group_name-1 }}` durch den Namen der ersten erstellten Ressourcengruppe.
- Ersetzen Sie den Platzhalter `{{ resource_group_name-2 }}` durch den Namen der zweiten erstellten Ressourcengruppe.
- Alle Ressourcen in den beiden angegebenen Ressourcengruppen werden gelöscht.

Führen Sie das Playbook aus, indem Sie den Befehl „ansible-playbook“ verwenden:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)