---
title: 'Tutorial: Konfigurieren von Labs in Azure DevTest Labs mithilfe von Ansible'
description: Hier erfahren Sie, wie Sie mithilfe von Ansible ein Lab in Azure DevTest Labs konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Playbook, MySQL, DevTest Labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741208"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Tutorial: Konfigurieren von Labs in Azure DevTest Labs mithilfe von Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

Mit [Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) können Entwickler die Erstellung von VM-Umgebungen für ihre Apps automatisieren. Diese Umgebungen können zum Entwickeln von Apps sowie zu Test- und Schulungszwecken konfiguriert werden. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Labs
> * Festlegen der Lab-Richtlinien
> * Festlegen der Lab-Zeitpläne
> * Erstellen des virtuellen Lab-Netzwerks
> * Definieren einer Artefaktquelle für das Lab
> * Erstellen eines virtuellen Computers innerhalb des Labs
> * Auflisten der Artefaktquellen und Artefakte des Labs
> * Abrufen von Azure Resource Manager-Informationen für die Artefaktquellen
> * Erstellen der Lab-Umgebung
> * Erstellen de Lab-Images
> * Löschen des Labs

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Der Ausschnitt aus dem Beispielplaybook erstellt eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Erstellen des Labs

Die nächste Aufgabe erstellt das Beispiel-Lab.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Festlegen der Lab-Richtlinien

Sie können Lab-Richtlinieneinstellungen einrichten. Folgende Werte können festgelegt werden:

- `user_owned_lab_vm_count`: Anzahl virtueller Computer, die ein Benutzer besitzen kann
- `user_owned_lab_premium_vm_count`: Maximale Anzahl virtueller Premium-Computer, die ein Benutzer besitzen kann
- `lab_vm_count`: Maximale Anzahl virtueller Lab-Computer
- `lab_premium_vm_count`: Maximale Anzahl virtueller Premium-Lab-Computer
- `lab_vm_size`: Zulässige Größen für virtuelle Lab-Computer
- `gallery_image`: Zulässige Katalogimages
- `user_owned_lab_vm_count_in_subnet`: Maximale Anzahl von virtuellen Computern des Benutzers in einem Subnetz
- `lab_target_cost`: Zielkosten des Labs

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Festlegen der Lab-Zeitpläne

Die Beispielaufgabe in diesem Abschnitt dient zum Konfigurieren des Lab-Zeitplans. 

Im folgenden Codeausschnitt wird mithilfe des Werts `lab_vms_startup` die Startzeit des virtuellen Computers angegeben. Durch Festlegen des Werts `lab_vms_shutdown` wird analog dazu die Zeit angegeben, zu der der virtuelle Computer heruntergefahren werden soll.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Erstellen des virtuellen Lab-Netzwerks

Die folgende Aufgabe erstellt das standardmäßige virtuelle Lab-Netzwerk:

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Definieren einer Artefaktquelle für das Lab

Eine Artefaktquelle ist ein ordnungsgemäß strukturiertes GitHub-Repository mit Artefaktdefinition und Azure Resource Manager-Vorlagen. Jedes Lab verfügt über vordefinierte öffentliche Artefakte. Die folgenden Aufgaben veranschaulichen die Erstellung einer Artefaktquelle für ein Lab:

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Erstellen eines virtuellen Computers innerhalb des Labs

Erstellen Sie einen virtuellen Computer innerhalb des Labs.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Auflisten der Artefaktquellen und Artefakte des Labs

Verwenden Sie die folgende Aufgabe, um alle standardmäßigen und benutzerdefinierten Artefaktquellen im Lab aufzulisten:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Die folgende Aufgabe listet alle Artefakte auf:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Abrufen von Azure Resource Manager-Informationen für die Artefaktquellen

Verwenden Sie das vordefinierte-Repository mit Vorlagen, um die Azure Resource Manager-Vorlagen in `public environment repository` aufzulisten:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Die folgende Aufgabe ruft Details zu einer bestimmten Azure Resource Manager-Vorlage aus dem Repository ab:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Erstellen der Lab-Umgebung

Die folgende Aufgabe erstellt die Lab-Umgebung auf der Grundlage einer der Vorlagen aus dem Repository für öffentliche Umgebungen:

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Erstellen de Lab-Images

Die folgende Aufgabe erstellt ein Image auf der Grundlage eines virtuellen Computers. Das Image ermöglicht die Erstellung identischer virtueller Computer.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Löschen des Labs

Verwenden Sie zum Löschen des Labs die folgende Aufgabe:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Abrufen der Beispielplaybooks

Das vollständige Beispielplaybook kann auf zwei Arten bezogen werden:
- [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml), und speichern Sie es als `devtestlab-create.yml`.
- Erstellen Sie eine neue Datei mit dem Namen `devtestlab-create.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Ausführen des Playbooks

In diesem Abschnitt führen Sie das Playbook aus, um verschiedene in diesem Artikel gezeigte Features zu testen.

Nehmen Sie vor dem Ausführen des Playbooks die folgenden Änderungen vor:
- Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.
- Speichern Sie das GitHub-Token als Umgebungsvariable namens `GITHUB_ACCESS_TOKEN`.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook devtestlab-create.yml
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