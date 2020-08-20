---
title: 'Tutorial: Konfigurieren von Themen in Azure Service Bus mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible zum Erstellen eines Azure Service Bus-Themas verwenden.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Service Bus, Themen, Abonnements
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 8dad002f52ded3baaf66d96a516a3d23dac91d56
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240062"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Tutorial: Konfigurieren von Themen in Azure Service Bus mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Themas
> * Erstellen eines Abonnements
> * Erstellen einer SAS-Richtlinie
> * Abrufen von Namespaceinformationen
> * Abrufen von Informationen zu Themen und Abonnements
> * Widerrufen einer SAS-Richtlinie

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>Erstellen des Service Bus-Themas

Mit dem Playbook-Beispielcode werden die folgenden Ressourcen erstellt:
- Azure-Ressourcengruppe
- Service Bus-Namespace in der Ressourcengruppe
- Service Bus-Thema mit dem Namespace

Speichern Sie das folgende Playbook als `servicebus_topic.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Erstellen des Abonnements

Mit dem Playbook-Beispielcode wird das Abonnement unter einem Service Bus-Thema erstellt. Azure Service Bus-Themen können über mehrere Abonnements verfügen. Ein Abonnent eines Themas kann eine Kopie jeder Nachricht erhalten, die an das Thema gesendet wird. Abonnements sind benannte Entitäten. Diese werden dauerhaft erstellt, können aber optional auch ablaufen.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Speichern Sie das folgende Playbook als `servicebus_subscription.yml`:

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>Erstellen der SAS-Richtlinie

Eine [Shared Access Signature (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) ist ein anspruchsbasierter Autorisierungsmechanismus mit Token. 

Der Beispielcode für das Playbook erstellt zwei SAS-Richtlinien für Service Bus-Warteschlange mit unterschiedlichen Berechtigungen.

Speichern Sie das folgende Playbook als `servicebus_topic_policy.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_topic_policy.yml
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

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Abrufen von Informationen zu Themen und Abonnements

Mit dem Playbook-Beispielcode werden die folgenden Informationen abgefragt:
- Informationen zum Service Bus-Thema
- Liste mit Abonnementdetails zum Thema
 
Speichern Sie das folgende Playbook als `servicebus_list.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

Beachten Sie vor dem Ausführen des Playbooks die folgenden Hinweise:
- Der `show_sas_policies`-Wert gibt an, ob die SAS-Richtlinien unter der angegebenen Warteschlange angezeigt werden. Dieser Wert ist standardmäßig auf `False` festgelegt, um zusätzlichen Netzwerkaufwand zu vermeiden.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_list.yml
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
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
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
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
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

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)