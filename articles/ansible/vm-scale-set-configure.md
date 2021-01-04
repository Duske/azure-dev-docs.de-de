---
title: 'Tutorial: Konfigurieren von VM-Skalierungsgruppen in Azure mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Erstellen und Konfigurieren von VM-Skalierungsgruppen in Azure verwenden.
keywords: ansible, azure, devops, bash, playbook, vm, vm-skalierungsgruppen, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: e8f2db42ee43db5fa9b8a61acb3f60119f84145d
ms.sourcegitcommit: e43be891c643ba2ddc3189ad98e4a49f03dfeedc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97824403"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Konfigurieren von VM-Skalierungsgruppen in Azure mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurieren der Ressourcen für einen virtuellen Computer
> * Konfigurieren einer Skalierungsgruppe
> * Skalieren der Skalierungsgruppe durch die Erhöhung der Anzahl von VM-Instanzen 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Konfigurieren einer Skalierungsgruppe

Mit dem Playbookcode in diesem Abschnitt werden die folgenden Ressourcen definiert:

* **Die Ressourcengruppe**, in der Ihre gesamten Ressourcen bereitgestellt werden.
* **Das virtuelle Netzwerk** im Adressraum 10.0.0.0/16
* **Das Subnetz** innerhalb des virtuellen Netzwerks
* **Die öffentliche IP-Adresse**, über die Sie auf Ressourcen im Internet zugreifen können
* **Die Netzwerksicherheitsgruppe**, mit der der Fluss des ein- und ausgehenden Netzwerkdatenverkehrs Ihrer Skalierungsgruppe gesteuert wird.
* **Das Lastenausgleichsmodul**, das den Datenverkehr auf der Grundlage von Lastenausgleichsregeln auf eine Gruppe definierter VMs verteilt.
* **Die VM-Skalierungsgruppe**, die alle erstellten Ressourcen verwendet

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml), und speichern Sie es als `vmss-create.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `vmss-create.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

* Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ admin_password }}` durch Ihr eigenes Kennwort.

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook vmss-create.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Anzeigen der Anzahl von VM-Instanzen

Die [konfigurierte Skalierungsgruppe](#configure-a-scale-set) verfügt derzeit über zwei Instanzen. Die folgenden Schritte werden verwendet, um diesen Wert zu bestätigen:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Navigieren Sie zu der Skalierungsgruppe, die Sie konfiguriert haben.

1. Der Name der Skalierungsgruppe wird mit der Anzahl von Instanzen in Klammern angezeigt: `Standard_DS1_v2 (2 instances)`.

1. Sie können die Änderung auch per [Azure Cloud Shell](https://shell.azure.com/) überprüfen, indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    In den Ergebnissen der Ausführung des Azure CLI-Befehls in Cloud Shell sehen Sie, dass jetzt drei Instanzen vorhanden sind: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Aufskalieren einer Skalierungsgruppe

Mit dem Playbookcode in diesem Abschnitt werden Informationen zur Skalierungsgruppe abgerufen, und die Kapazität wird von 2 in 3 geändert.

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml), und speichern Sie es als `vmss-scale-out.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `vmss-scale-out.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook vmss-scale-out.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Überprüfen der Ergebnisse

Überprüfen Sie die Ergebnisse Ihrer Arbeit über das Azure-Portal:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Navigieren Sie zu der Skalierungsgruppe, die Sie konfiguriert haben.

1. Der Name der Skalierungsgruppe wird mit der Anzahl von Instanzen in Klammern angezeigt: `Standard_DS1_v2 (3 instances)`. 

1. Sie können die Änderung auch mithilfe von [Azure Cloud Shell](https://shell.azure.com/) überprüfen, indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    In den Ergebnissen der Ausführung des Azure CLI-Befehls in Cloud Shell sehen Sie, dass jetzt drei Instanzen vorhanden sind: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Bereitstellen von Apps für VM-Skalierungsgruppen in Azure mit Ansible](./vm-scale-set-deploy-app.md)
