---
title: 'Tutorial: Konfigurieren von AKS-Clustern (Azure Kubernetes Service) in Azure mithilfe von Ansible'
description: Erfahren Sie, wie Sie mithilfe von Ansible einen Azure Kubernetes Service-Cluster in Azure erstellen und verwalten können
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, Playbook, AKS, Container, Kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7f68399fe2bf8dfe60a80ffdaff714b5c609b166
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743706"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Tutorial: Konfigurieren von AKS-Clustern (Azure Kubernetes Service) in Azure mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../includes/open-source-devops-intro-aks.md)]

AKS kann für die Verwendung von [Azure Active Directory (AD)](/azure/active-directory/) für die Benutzerauthentifizierung konfiguriert werden. Nach der Konfiguration verwenden Sie Ihr Azure AD-Authentifizierungstoken, um sich am AKS-Cluster anzumelden. Die rollenbasierte Zugriffssteuerung (RBAC) kann auf der Identität eines Benutzers oder auf der Mitgliedschaft in einer Verzeichnisgruppe basieren.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines AKS-Clusters
> * Konfigurieren eines AKS-Clusters

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Erstellen eines verwalteten AKS-Clusters

Mit dem Beispielplaybook werden eine Ressourcengruppe und ein AKS-Cluster in der Ressourcengruppe erstellt.

Speichern Sie das folgende Playbook als `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
    aks_version: aks_version
tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      kubernetes_version: "{{aks_version}}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

- Im ersten Abschnitt von `tasks` wird am Standort `eastus` eine Ressourcengruppe mit dem Namen `myResourceGroup` definiert.
- Im zweiten Abschnitt von `tasks` wird in der Ressourcengruppe `myResourceGroup` ein AKS-Cluster mit dem Namen `myAKSCluster` definiert.
- Geben Sie für den Platzhalter `your_ssh_key` Ihren öffentlichen RSA-Schlüssel im einzeiligen Format beginnend mit „ssh-rsa“ (ohne Anführungszeichen) ein.
- Verwenden Sie für den Platzhalter `aks_version` den Befehl [az aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions).

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook azure_create_aks.yml
```

Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Skalieren der AKS-Knoten

Das Beispielplaybook aus dem vorherigen Abschnitt definiert zwei Knoten. Sie passen die Anzahl von Knoten an, indem Sie im Block `agent_pool_profiles` den Wert `count` ändern.

Speichern Sie das folgende Playbook als `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:

- Geben Sie für den Platzhalter `your_ssh_key` Ihren öffentlichen RSA-Schlüssel im einzeiligen Format beginnend mit „ssh-rsa“ (ohne Anführungszeichen) ein.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook azure_configure_aks.yml
```

Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Löschen eines verwalteten AKS-Clusters

Mit dem Beispielplaybook wird ein AKS-Cluster gelöscht.

Speichern Sie das folgende Playbook als `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook azure_delete_aks.yml
```

Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Skalieren einer Anwendung in Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)