---
title: 'Tutorial: Konfigurieren von Warteschlangen in Azure Service Bus mithilfe von Ansible'
description: Informationen zum Erstellen einer Azure Service Bus-Warteschlange mithilfe von Ansible
keywords: Ansible, Azure, DevOps, Bash, Playbook, Service Bus, Warteschlange
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 2f58dac776de66d0d4f449bf4fe34a7c9d30c8fe
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681989"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Tutorial: Konfigurieren von Warteschlangen in Azure Service Bus mithilfe von Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen einer Warteschlange
> * Erstellen einer SAS-Richtlinie
> * Abrufen von Namespaceinformationen
> * Abrufen von Warteschlangeninformationen
> * Widerrufen der SAS-Richtlinie für die Warteschlange

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Erstellen der Service Bus-Warteschlange

Mit dem Playbook-Beispielcode werden die folgenden Ressourcen erstellt:
- Azure-Ressourcengruppe
- Service Bus-Namespace in der Ressourcengruppe
- Service Bus-Warteschlange mit dem Namespace

Speichern Sie das folgende Playbook als `servicebus_queue.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>Erstellen der SAS-Richtlinie

Eine [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) ist ein anspruchsbasierter Autorisierungsmechanismus mit Token. 

Der Beispielcode für das Playbook erstellt zwei SAS-Richtlinien für Service Bus-Warteschlange mit unterschiedlichen Berechtigungen.

Speichern Sie das folgende Playbook als `servicebus_queue_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:
- Der `rights`-Wert stellt die Berechtigung eines Benutzers für die Warteschlange dar. Geben Sie einen der folgenden Werte an: `manage`, `listen`, `send` oder `listen_send`.

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook servicebus_queue_policy.yml
```

## <a name="retrieve-namespace-information"></a>Abrufen von Namespaceinformationen

Der Beispielcode für das Playbook fragt die Namespaceinformationen ab.

Speichern Sie das folgende Playbook als `servicebus_namespace_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:
- Der `show_sas_policies`-Wert gibt an, ob die SAS-Richtlinien unter dem angegebenen Namespace angezeigt werden. Der Wert ist standardmäßig `False`, um zusätzlichen Netzwerkaufwand zu vermeiden.

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Abrufen von Warteschlangeninformationen

Der Beispielcode für das Playbook fragt die Warteschlangeninformationen ab. 

Speichern Sie das folgende Playbook als `servicebus_queue_info.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:
- Der `show_sas_policies`-Wert gibt an, ob die SAS-Richtlinien unter der angegebenen Warteschlange angezeigt werden. Dieser Wert ist standardmäßig auf `False` festgelegt, um zusätzlichen Netzwerkaufwand zu vermeiden.

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook servicebus_queue_info.yml
```

## <a name="revoke-the-queue-sas-policy"></a>Widerrufen der SAS-Richtlinie für die Warteschlange

Der Beispielcode für das Playbook löscht eine SAS-Richtlinie für die Warteschlange.

Speichern Sie das folgende Playbook als `servicebus_queue_policy_delete.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Speichern Sie den folgenden Code als `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Tutorial: Konfigurieren eines Themas in Azure Service Bus mithilfe von Ansible](service-bus-topic-configure.md)