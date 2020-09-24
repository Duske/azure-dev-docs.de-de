---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 885764615beed7a623db03499b4ff6a6ab705ba7
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831170"
---
#### <a name="ansible"></a>[Ansible](#tab/ansible)

1. Speichern Sie den folgenden Code als `delete_rg.yml`.

    ```yml
    ---
    - hosts: localhost
      tasks:
        - name: Deleting resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            state: absent
          register: rg
        - debug:
            var: rg
    ```

1. Führen Sie das Playbook mithilfe des Befehls [ansible-playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) aus. Ersetzen Sie den Platzhalter durch den Namen der zu löschenden Ressourcengruppe. Alle Ressourcen innerhalb der Ressourcengruppe werden gelöscht.

    ```bash
    ansible-playbook delete_rg.yml --extra-vars "name=<resource_group>"
    ```

    **Hinweise**:

    - Aufgrund der Variablen `register` und des Abschnitts `debug` des Playbooks werden die Ergebnisse angezeigt, wenn der Befehl abgeschlossen ist.
    
#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Führen Sie [az group delete](/cli/azure/group#az_group_delete) aus, um die Ressourcengruppe zu löschen. Alle Ressourcen innerhalb der Ressourcengruppe werden gelöscht.

    ```azurecli
    az group delete --name <resource_group>
    ```

1. Überprüfen Sie mithilfe von [az group show](/cli/azure/group#az_group_show), ob die Ressourcengruppe gelöscht wurde.

    ```azurecli
    az group show --name <resource_group>
    ```

#### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

1. Führen Sie [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup) aus, um die Ressourcengruppe zu löschen. Alle Ressourcen innerhalb der Ressourcengruppe werden gelöscht.

    ```azurepowershell
    Remove-AzResourceGroup -Name <resource_group>
    ```

1. Überprüfen Sie mithilfe von [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup), ob die Ressourcengruppe gelöscht wurde.

    ```azurepowershell
    Get-AzResourceGroup -Name <resource_group>
    ```

---