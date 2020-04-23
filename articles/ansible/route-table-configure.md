---
title: 'Tutorial: Konfigurieren von Azure-Routingtabellen mit Ansible'
description: Es wird beschrieben, wie Sie mit Ansible Azure-Routingtabellen erstellen, verwalten und löschen. Außerdem erfahren Sie mehr über das Erstellen und Löschen von Routen.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Netzwerke, Route, Routingtabelle
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: cadc8f63d887cdd1ffe8b230dbe53a68645e601b
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743322"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Tutorial: Konfigurieren von Azure-Routingtabellen mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

Azure führt für Datenverkehr automatisch das Routing zwischen Azure-Subnetzen, virtuellen Netzwerken und lokalen Netzwerken durch. Falls Sie mehr Kontrolle über das Routing für Ihre Umgebung benötigen, können Sie eine [Routingtabelle](/azure/virtual-network/virtual-networks-udr-overview) erstellen. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Erstellen einer Routingtabelle Erstellen eines virtuellen Netzwerks und Subnetzes Zuordnen einer Routingtabelle zu einem Subnetz Aufheben der Zuordnung einer Routingtabelle zu einem Subnetz Erstellen und Löschen von Routen Abfragen einer Routingtabelle Löschen einer Routingtabelle

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Erstellen einer Routingtabelle

Mit dem Playbookcode in diesem Abschnitt wird eine Routingtabelle erstellt. Weitere Informationen zu Einschränkungen für Routingtabellen finden Sie auf der Seite mit den [Azure-Einschränkungen](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Speichern Sie das folgende Playbook als `route_table_create.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Zuordnen einer Routingtabelle zu einem Subnetz

Mit dem Playbookcode in diesem Abschnitt wird Folgendes durchgeführt:

* Erstellt ein virtuelles Netzwerk.
* Erstellen eines Subnetzes innerhalb des virtuellen Netzwerks
* Zuordnen einer Routingtabelle zum Subnetz

Routingtabellen werden nicht virtuellen Netzwerken zugeordnet. Stattdessen werden Routingtabellen dem Subnetz eines virtuellen Netzwerks zugeordnet.

Das virtuelle Netzwerk und die Routingtabelle müssen sich an demselben Azure-Standort und unter demselben Abonnement befinden.

Subnetze und Routingtabellen verfügen über eine 1:n-Beziehung. Ein Subnetz kann ohne zugeordnete Routingtabelle oder mit einer einzelnen Routingtabelle definiert werden. Routingtabellen können keinem, einem oder vielen Subnetzen zugeordnet werden. 

Die Weiterleitung des Datenverkehrs aus dem Subnetz basiert auf:

- In Routingtabellen definierten Routen
- [Standardrouten](/azure/virtual-network/virtual-networks-udr-overview#default)
- Routen, die aus einem lokalen Netzwerk stammen

Das virtuelle Netzwerk muss mit einem Gateway für virtuelle Azure-Netzwerke verbunden werden. Das Gateway kann vom Typ „ExpressRoute“ oder „VPN“ sein, wenn BGP mit einem VPN-Gateway verwendet wird.

Speichern Sie das folgende Playbook als `route_table_associate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Aufheben der Zuordnung einer Routingtabelle zu einem Subnetz

Mit dem Playbookcode in diesem Abschnitt wird die Zuordnung einer Routingtabelle zu einem Subnetz aufgehoben.

Legen Sie beim Aufheben der Zuordnung einer Routingtabelle zu einem Subnetz das `route_table`-Element für das Subnetz auf `None` fest. 

Speichern Sie das folgende Playbook als `route_table_dissociate.yml`:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Erstellen einer Route

Mit dem Playbookcode in diesem Abschnitt wird in einer Routingtabelle eine Route erstellt. 

Speichern Sie das folgende Playbook als `route_create.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* `virtual_network_gateway` ist als `next_hop_type` definiert. Weitere Informationen dazu, wie in Azure Routen ausgewählt werden, finden Sie in der [Routingübersicht](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` ist als `10.1.0.0/16` definiert. Das Präfix kann in der Routingtabelle nicht dupliziert werden.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Löschen einer Route

Mit dem Playbookcode in diesem Abschnitt wird eine Route aus einer Routingtabelle gelöscht.

Speichern Sie das folgende Playbook als `route_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Abrufen von Informationen zur Routingtabelle

Für den Playbookcode in diesem Abschnitt wird das Ansible-Modul `azure_rm_routetable_facts` verwendet, um Informationen zur Routingtabelle abzurufen.

Speichern Sie das folgende Playbook als `route_table_facts.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Löschen einer Routingtabelle

Mit dem Playbookcode in diesem Abschnitt wird eine Routingtabelle gelöscht.

Wenn eine Routingtabelle gelöscht wird, werden auch alle darin enthaltenen Routen gelöscht.

Das Löschen einer Routingtabelle ist nicht möglich, wenn sie einem Subnetz zugeordnet ist. [Heben Sie die Zuordnung der Routingtabelle zu Subnetzen auf](#dissociate-a-route-table-from-a-subnet), bevor Sie versuchen, die Routingtabelle zu löschen. 

Speichern Sie das folgende Playbook als `route_table_delete.yml`:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)