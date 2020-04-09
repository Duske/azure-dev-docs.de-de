---
title: 'Tutorial: Konfigurieren von kubenet-Netzwerken in Azure Kubernetes Service (AKS) mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Konfigurieren von kubenet-Netzwerken im AKS-Cluster (Azure Kubernetes Service) konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, Playbook, AKS, Container, Kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 1f15710de9ab6f2d058b72096f0265541c131d9f
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741688"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Konfigurieren von kubenet-Netzwerken in Azure Kubernetes Service (AKS) mit Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Mit AKS können Sie einen Cluster bereitstellen, indem Sie die folgenden Netzwerkmodelle verwenden:

- [Kubenet-Netzwerke](/azure/aks/configure-kubenet): Netzwerkressourcen werden normalerweise bei der Bereitstellung des AKS-Clusters erstellt und konfiguriert.
- [Azure CNI-Netzwerk (Container Networking Interface)](/azure/aks/configure-azure-cni): Der AKS-Cluster wird mit vorhandenen VNET-Ressourcen und -Konfigurationen verbunden.

Weitere Informationen zu Netzwerken für Ihre Anwendungen in AKS finden Sie im Artikel zu den [Netzwerkkonzepten für Anwendungen in AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines AKS-Clusters
> * Konfigurieren von Azure-kubenet-Netzwerken

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Erstellen eines virtuellen Netzwerks und des Subnetzes

Mit dem Playbookcode in diesem Abschnitt werden die folgenden Azure-Ressourcen erstellt:

- Virtuelles Netzwerk
- Subnetz im virtuellen Netzwerk

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

Mit dem Playbookcode in diesem Abschnitt wird ein AKS-Cluster in einem virtuellen Netzwerk erstellt. 

Speichern Sie das folgende Playbook als `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
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
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Verwenden Sie das Modul `azure_rm_aks_version`, um nach der unterstützten Version zu suchen.
- Bei `vnet_subnet_id` handelt es sich um das Subnetz, das im vorherigen Abschnitt erstellt wurde.
- Mit `network_profile` werden die Eigenschaften für das kubenet-Netzwerk-Plug-In definiert.
- Mit `service_cidr` werden interne Dienste im AKS-Cluster einer IP-Adresse zugewiesen. Dieser IP-Adressbereich muss ein Adressraum sein, der nicht an einer anderen Stelle in Ihrem Netzwerk verwendet wird. 
- Die Adresse `dns_service_ip` sollte die „.10“-Adresse Ihres Dienst-IP-Adressbereichs sein.
- `pod_cidr` sollte ein großer Adressraum sein, der nicht an einer anderen Stelle in Ihrer Netzwerkumgebung verwendet wird. Dieser Adressbereich muss groß genug für die Anzahl von Knoten sein, auf die Sie voraussichtlich hochskalieren möchten. Sie können diesen Adressbereich nicht mehr ändern, nachdem der Cluster bereitgestellt wurde.
- Mit dem Pod-IP-Adressbereich wird jedem Knoten im Cluster ein „/24“-Adressraum zugewiesen. Im folgenden Beispiel wird mit `pod_cidr` von 192.168.0.0/16 dem ersten Knoten 192.168.0.0/24, dem zweiten Knoten 192.168.1.0/24 und dem dritten Knoten 192.168.2.0/24 zugewiesen.
- Beim Skalieren oder Upgraden des Clusters weist Azure weiterhin jedem neuen Knoten einen Pod-IP-Adressbereich zu.
- Das Playbook lädt `ssh_key` aus `~/.ssh/id_rsa.pub`. Verwenden Sie bei etwaigen Änderungen das einzeilige Format, und beginnen Sie die Zeile mit „ssh-rsa“ (ohne Anführungszeichen).
- Die Werte `client_id` und `client_secret` werden aus `~/.azure/credentials` geladen. Dies ist die Standarddatei mit den Anmeldeinformationen. Sie können diese Werte auf Ihren Dienstprinzipal festlegen oder sie aus Umgebungsvariablen laden:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Zuordnen der Netzwerkressourcen

Wenn Sie einen AKS-Cluster erstellen, werden eine Netzwerksicherheitsgruppe und Routingtabelle erstellt. Diese Ressourcen werden per AKS verwaltet und aktualisiert, wenn Sie Dienste erstellen und verfügbar machen. Ordnen Sie die Netzwerksicherheitsgruppe und Routingtabelle wie folgt Ihrem Subnetz des virtuellen Netzwerks zu: 

Speichern Sie das folgende Playbook als `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- `node_resource_group` ist der Name der Ressourcengruppe, in der die AKS-Knoten erstellt werden.
- `vnet_subnet_id` ist das Subnetz, das im vorherigen Abschnitt erstellt wurde.


## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

Dieser Abschnitt enthält das vollständige Beispielplaybook, mit dem die in diesem Artikel erstellten Aufgaben aufgerufen werden. 

Speichern Sie das folgende Playbook als `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Nehmen Sie im Abschnitt `vars` die folgenden Änderungen vor:

- Ändern Sie für den Schlüssel `resource_group` den Wert `aksansibletest` in den Namen Ihrer Ressourcengruppe.
- Ändern Sie für den Schlüssel `name` den Wert `aksansibletest` in den Namen Ihrer AKS-Instanz.
- Ändern Sie für den Schlüssel `Location` den Wert `eastus` in den Standort Ihrer Ressourcengruppe.

Führen Sie das vollständige Playbook mit dem Befehl `ansible-playbook` aus:

```bash
ansible-playbook aks-kubenet.yml
```

Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie den folgenden Code als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren von Azure CNI-Netzwerken (Container Networking Interface) in AKS mit Ansible](./aks-configure-cni-networking.md)