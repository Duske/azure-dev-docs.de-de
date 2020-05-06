---
title: 'Tutorial: Konfigurieren eines Clusters in Azure HDInsight mithilfe von Ansible'
description: Erfahren Sie, wie Sie mithilfe von Ansible einen Azure HDInsight-Cluster konfigurieren, seine Größe ändern und ihn löschen können.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Apache Hadoop, HDInsight
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78cb70a3346aec86d079d2667349f9bc6e0eb4b2
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743635"
---
# <a name="tutorial-configure-a-cluster-in-azure-hdinsight-using-ansible"></a>Tutorial: Konfigurieren eines Clusters in Azure HDInsight mithilfe von Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[Azure HDInsight](/azure/hdinsight/) ist ein Hadoop-basierter Analysedienst zum Verarbeiten von Daten. HDInsight ist ein ETL-Tool (Extrahieren, Transformieren und Laden) für die Verwendung mit strukturierten und unstrukturierten Big Data-Ressourcen. HDInsight unterstützt verschiedene [Clustertypen](/azure/hdinsight/hadoop/apache-hadoop-introduction), wobei jeder Typ unterschiedliche Komponenten unterstützt. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Speicherkontos für HDInsight
> * Konfigurieren eines [HDInsight Spark-Clusters](/azure/hdinsight/spark/apache-spark-overview)
> * Ändern der Clustergröße
> * Löschen eines Clusters

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-random-postfix"></a>Erstellen eines zufälligen Postfix

Mit dem Playbookcode in diesem Abschnitt wird ein zufälliges Postfix generiert, das als Teil des Azure HDInsight-Clusternamens verwendet werden soll.

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"  
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes
```

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Der Playbookcode in diesem Abschnitt erstellt eine Ressourcengruppe.


```yml
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

## <a name="create-a-storage-account-and-retrieve-key"></a>Erstellen eines Speicherkontos und Abrufen des Schlüssels

Ein Azure-Speicherkonto wird als Standardspeicher für den HDInsight-Cluster verwendet. 

Der Playbookcode in diesem Abschnitt ruft den Schlüssel für den Zugriff auf das Speicherkonto ab.

```yml
- name: Create storage account
  azure_rm_storageaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ storage_account_name }}"
      account_type: Standard_LRS
      location: eastus2

- name: Get storage account keys
  azure_rm_resource:
    api_version: '2018-07-01'
    method: POST
    resource_group: "{{ resource_group }}"
    provider: storage
    resource_type: storageaccounts
    resource_name: "{{ storage_account_name }}"
    subresource:
      - type: listkeys
  register: storage_output

- debug:
    var: storage_output
```

## <a name="create-an-hdinsight-spark-cluster"></a>Erstellen eines HDInsight Spark-Clusters

Der Playbookcode in diesem Abschnitt erstellt den Azure HDInsight-Cluster.

```yml
- name: Create instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
```

Die Erstellung der Instanz kann einige Minuten dauern.

## <a name="resize-the-cluster"></a>Ändern der Clustergröße

Nach der Clustererstellung können Sie als einzige Einstellung die Anzahl von Workerknoten ändern. 

Der Playbookcode in diesem Abschnitt erhöht die Anzahl von Workerknoten durch Aktualisierung von `target_instance_count` in `workernode`.

```yml
- name: Resize cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 2
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
    tags:
      aaa: bbb
  register: output
```

## <a name="delete-the-cluster-instance"></a>Löschen der Clusterinstanz

Die Abrechnung für die HDInsight-Cluster erfolgt anteilsmäßig auf Minutenbasis. 

Mit dem Playbookcode in diesem Abschnitt wird der Cluster gelöscht.

```yml
- name: Delete instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    state: absent
```

## <a name="get-the-sample-playbook"></a>Abrufen der Beispielplaybooks

Das vollständige Beispielplaybook kann auf zwei Arten bezogen werden:
- [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/hdinsight_create.yml), und speichern Sie es als `hdinsight_create.yml`.
- Erstellen Sie eine neue Datei mit dem Namen `hdinsight_create.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
---
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus2
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cluster_name: mycluster{{ rpfx }}
    storage_account_name: mystorage{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create storage account
      azure_rm_storageaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ storage_account_name }}"
        account_type: Standard_LRS
        location: "{{ location }}"

    - name: Get storage account keys
      azure_rm_resource:
        api_version: '2018-07-01'
        method: POST
        resource_group: "{{ resource_group }}"
        provider: storage
        resource_type: storageaccounts
        resource_name: "{{ storage_account_name }}"
        subresource:
          - type: listkeys
      register: storage_output
    - debug:
        var: storage_output

    - name: Create instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net" 
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123

    - name: Resize cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net"
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 2
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
        tags:
          aaa: bbb
      register: output
    - debug:
        var: output

    - name: Assert the state has changed
      assert:
        that:
          - output.changed

    - name: Delete instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

In diesem Abschnitt führen Sie das Playbook aus, um verschiedene in diesem Artikel gezeigte Features zu testen.

Nehmen Sie vor dem Ausführen des Playbooks die folgenden Änderungen vor:
- Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook hdinsight.yml
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