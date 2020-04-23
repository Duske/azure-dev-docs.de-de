---
title: 'Schnellstart: Verwalten von virtuellen Linux-Computern in Azure mithilfe von Ansible'
description: In diesem Schnellstart erfahren Sie, wie Sie einen virtuellen Linux-Computer in Azure mit Ansible verwalten.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, Playbook, Bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 2a1a3439679f4162b4ec874137a332758e4915c5
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743204"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Schnellstart: Verwalten von virtuellen Linux-Computern in Azure mithilfe von Ansible

Ansible ermöglicht die Automatisierung der Bereitstellung und Konfiguration von Ressourcen in Ihrer Umgebung. In diesem Artikel starten und beenden Sie mithilfe eines Ansible-Playbooks einen virtuellen Linux-Computer. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Dient zum Beenden eines virtuellen Computers.

In diesem Abschnitt heben Sie mithilfe von Ansible die Zuordnung eines virtuellen Computers auf (beenden diesen).

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie [Cloud Shell](/azure/cloud-shell/overview).

1. Erstellen Sie eine Datei mit dem Namen `azure-vm-stop.yml`, und öffnen Sie sie im Editor:

    ```bash
    code azure-vm-stop.yml
    ```

1. Fügen Sie den folgenden Beispielcode in den Editor ein:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Ersetzen Sie die Platzhalter `{{ resource_group_name }}` und `{{ vm_name }}` durch Ihre Werte.

1. Speichern Sie die Datei, und beenden Sie den Editor.

1. Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Starten eines virtuellen Computers

In diesem Abschnitt starten Sie einen virtuellen Azure-Computer, dessen Zuordnung aufgehoben wurde (der beendet wurde), mit Ansible.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie [Cloud Shell](/azure/cloud-shell/overview).

1. Erstellen Sie eine Datei mit dem Namen `azure-vm-start.yml`, und öffnen Sie sie im Editor:

    ```bash
    code azure-vm-start.yml
    ```

1. Fügen Sie den folgenden Beispielcode in den Editor ein:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Ersetzen Sie die Platzhalter `{{ resource_group_name }}` und `{{ vm_name }}` durch Ihre Werte.

1. Speichern Sie die Datei, und beenden Sie den Editor.

1. Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Verwalten dynamischer Azure-Bestände mithilfe von Ansible](./dynamic-inventory-configure.md)