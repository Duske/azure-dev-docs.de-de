---
title: 'Tutorial: Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS) mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Konfigurieren von kubenet-Netzwerken im AKS-Cluster (Azure Kubernetes Service) konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, Playbook, AKS, Container, Kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 6be7056a89b0ac0327ffc5ba39b77c31c23bb0fe
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743908"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS) mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

Mit AKS können Sie einen Cluster bereitstellen, indem Sie die folgenden Netzwerkmodelle verwenden:

- [Kubenet-Netzwerke](/azure/aks/configure-kubenet): Netzwerkressourcen werden normalerweise bei der Bereitstellung des AKS-Clusters erstellt und konfiguriert.
- [Azure CNI-Netzwerke](/azure/aks/configure-azure-cni): Der AKS-Cluster wird mit vorhandenen VNET-Ressourcen und -Konfigurationen verbunden.

Weitere Informationen zu Netzwerken für Ihre Anwendungen in AKS finden Sie im Artikel zu den [Netzwerkkonzepten für Anwendungen in AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines AKS-Clusters
> * Konfigurieren von Azure CNI-Netzwerken

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Erstellen eines virtuellen Netzwerks
- Erstellen eines Subnetzes innerhalb des virtuellen Netzwerks

Speichern Sie das folgende Playbook als `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Erstellen eines AKS-Clusters im virtuellen Netzwerk

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Erstellen eines AKS-Clusters in einem virtuellen Netzwerk

Speichern Sie das folgende Playbook als `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Verwenden Sie das `azure_rm_aks_version`-Modul, um die unterstützte Version zu ermitteln.
- Bei `vnet_subnet_id` handelt es sich um das Subnetz, das im vorherigen Abschnitt erstellt wurde.
- Das Playbook lädt `ssh_key` aus `~/.ssh/id_rsa.pub`. Verwenden Sie bei etwaigen Änderungen das einzeilige Format, und beginnen Sie die Zeile mit „ssh-rsa“ (ohne Anführungszeichen).
- Die Werte `client_id` und `client_secret` werden aus `~/.azure/credentials` geladen. Dies ist die Standarddatei mit den Anmeldeinformationen. Sie können diese Werte auf Ihren Dienstprinzipal festlegen oder sie aus Umgebungsvariablen laden:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

Der Playbook-Beispielcode in diesem Abschnitt wird verwendet, um verschiedene Features dieses Tutorials zu testen.

Speichern Sie das folgende Playbook als `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Ändern Sie den Wert `aksansibletest` in Ihren Ressourcengruppennamen.
- Ändern Sie den Wert `aksansibletest` in Ihren AKS-Namen.
- Ändern Sie den Wert `eastus` in Ihren Ressourcengruppenstandort.

Führen Sie das Playbook aus, indem Sie den Befehl „ansible-playbook“ verwenden:

```bash
ansible-playbook aks-azure-cni.yml
```

Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Löschen einer Ressourcengruppe, auf die im Abschnitt `vars` verwiesen wird

Speichern Sie das folgende Playbook als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Ersetzen Sie den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.
- Alle Ressourcen innerhalb der angegebenen Ressourcengruppe werden gelöscht.

Führen Sie das Playbook aus, indem Sie den Befehl „ansible-playbook“ verwenden:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von Azure Active Directory in AKS mit Ansible](./aks-configure-rbac.md)