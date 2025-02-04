---
title: 'Schnellstart: Verwalten von virtuellen Linux-Computern in Azure mithilfe von Ansible'
description: In diesem Schnellstart erfahren Sie, wie Sie einen virtuellen Linux-Computer in Azure mit Ansible verwalten.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, Playbook, Bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 896616cff3f2df237de625536981b08d5db9175b
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681922"
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

1. Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

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

1. Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

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