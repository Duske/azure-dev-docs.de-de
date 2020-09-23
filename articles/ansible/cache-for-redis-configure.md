---
title: 'Tutorial: Konfigurieren von Caches in Azure-Cache für Redis mithilfe von Ansible'
description: Es wird beschrieben, wie Sie Ansible verwenden, um Azure Cache for Redis zu erstellen, zu skalieren, neu zu starten und eine Firewallregel hinzuzufügen.
keywords: Ansible, Azure, DevOps, Bash, Playbook, Cache, Redis
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 66da31edef14a09a903b3a78d97e1067856098ef
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681739"
---
# <a name="tutorial-configure-caches-in-azure-cache-for-redis-using-ansible"></a>Tutorial: Konfigurieren von Caches in Azure-Cache für Redis mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

[Azure-Cache für Redis](/azure/azure-cache-for-redis/) ist ein Dienst mit Open-Source-Kompatibilität, mit dem Sie reaktionsschnelle Apps erstellen können, indem Sie den schnellen Zugriff auf Daten ermöglichen. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen eines Caches
> * Skalieren eines Caches
> * Neustarten eines Caches
> * Hinzufügen einer Firewallregel zu einem Cache
> * Löschen eines Caches

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-cache"></a>Erstellen eines Caches

Erstellen Sie eine Instanz von Azure-Cache für Redis in einer neuen Ressourcengruppe.

```yml
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1 
```

Die Bereitstellung eines Caches dauert einige Minuten. Mit dem folgenden Code wird Ansible angewiesen, auf den Abschluss des Vorgangs zu warten:

```yml
  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Während des länger dauernden Bereitstellungsprozesses werden mehrere „Fehlermeldungen“ angezeigt. Sie können diese Meldungen ignorieren. Die wichtige Meldung ist die letzte Meldung. Im folgenden Beispiel kommt es zu vielen Fehlermeldungen, bevor die letzte Meldung („OK“) angezeigt wird.

```Output
FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]
```

## <a name="scale-the-cache"></a>Skalieren des Caches

Azure-Cache für Redis verfügt über verschiedene Angebote, die Sie je nach den Anforderungen Ihrer App in Anspruch nehmen können. Diese Cacheoptionen sorgen für Flexibilität bei der Auswahl der Cachegröße und der Features. Wenn sich Ihre App-Anforderungen nach der Erstellung des Caches ändern, können Sie den Cache wie gewünscht skalieren. Weitere Informationen zur Skalierung finden Sie unter [Skalieren von Azure-Cache für Redis](/azure/azure-cache-for-redis/cache-how-to-scale).

Mit dem folgenden Beispielcode wird für den Cache die Skalierung auf **Standard** durchgeführt:

```yml
- name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1
```

Das Skalieren eines Caches kann einige Minuten dauern. Mit dem folgenden Code wird Ansible angewiesen, auf den Abschluss des Vorgangs zu warten:

```yml
  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

Ähnlich wie bei der Aufgabe zum Bereitstellen von Azure-Cache für Redis kann es zu Meldungen der folgenden Art kommen:

```Ouput
**FAILED - RETRYING: Get facts (100 retries left)** is normal.
```

## <a name="reboot-the-cache"></a>Neustarten des Caches

Mit dem folgenden Code wird der Cache, der in den vorherigen Abschnitten erstellt wurde, neu gestartet.

```yml
  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all
```

### <a name="add-firewall-rule"></a>Hinzufügen einer Firewallregel

Mit dem folgenden Code wird dem Cache eine Firewallregel hinzugefügt:

```yml
  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4
```

## <a name="delete-the-cache"></a>Löschen des Caches

Mit dem folgenden Code wird der Cache gelöscht:

```yml
  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>Abrufen der Beispielplaybooks

Das vollständige Beispielplaybook kann auf zwei Arten bezogen werden:
- [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/rediscache.yml), und speichern Sie es als `rediscache.yml`.
- Erstellen Sie eine neue Datei mit dem Namen `rediscache.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- name: Manage Azure Cache for Redis
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    redis_name: "redis{{ resource_group_name }}"
    location: eastus2

  roles:
    - azure.azure_preview_modules

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1

  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1

  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all

  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4

  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>Ausführen des Beispielplaybooks

In diesem Abschnitt führen Sie das Playbook aus, um verschiedene in diesem Artikel gezeigte Features zu testen.

Ersetzen Sie im Abschnitt `vars` den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.

Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

```bash
ansible-playbook rediscache.yml
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus:

```Output
TASK [create resource group] 
Tuesday 12 March 2019  16:21:07 +0800 (0:00:00.054)       0:00:01.503 
ok: [localhost]

TASK [Create Azure Cache for Redis] 
Tuesday 12 March 2019  16:21:09 +0800 (0:00:01.950)       0:00:03.454 
 [WARNING]: Azure API profile latest does not define an entry for RedisManagementClient

changed: [localhost]

TASK [Dump host name] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:40.125)       0:00:43.580 
ok: [localhost] =>
  output['host_name']: redis0312.redis.cache.windows.net

TASK [Get facts] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:00.056)       0:00:43.636 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
FAILED - RETRYING: Get facts (90 retries left).
ok: [localhost]

TASK [Scale up Azure Cache for Redis] 
Tuesday 12 March 2019  16:33:20 +0800 (0:11:31.296)       0:12:14.933 
changed: [localhost]

TASK [Get facts] 
Tuesday 12 March 2019  16:33:29 +0800 (0:00:09.164)       0:12:24.097 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]

TASK [Reboot Azure Cache for Redis] 
Tuesday 12 March 2019  16:43:57 +0800 (0:10:27.740)       0:22:51.838 
ok: [localhost]

TASK [Add Firewall rule] 
Tuesday 12 March 2019  16:44:02 +0800 (0:00:05.432)       0:22:57.271 
changed: [localhost]

TASK [Delete Azure Cache for Redis] 
Tuesday 12 March 2019  16:44:08 +0800 (0:00:05.137)       0:23:02.409 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=10   changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

Tuesday 12 March 2019  16:44:14 +0800 (0:00:06.217)       0:23:08.626 

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](https://docs.microsoft.com/azure/ansible/)