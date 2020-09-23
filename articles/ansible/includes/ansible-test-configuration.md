---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 5351445b63618d072ecf4cf8beeffc9a071bd84d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682041"
---
In diesem Abschnitt wird gezeigt, wie Sie eine Testressourcengruppe in der neuen Ansible-Konfiguration erstellen. Ist dies nicht erforderlich, können Sie diesen Schritt überspringen.

### <a name="create-an-azure-resource-group"></a>Erstellen einer Azure-Ressourcengruppe

1. Speichern Sie den folgenden Code als `create_rg.yml`.

    ```yaml
    ---
    - hosts: localhost
      connection: local
      tasks:
        - name: Creating resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            location: "{{ location }}"
          register: rg
        - debug:
            var: rg
    ```

1. Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus. Ersetzen Sie die Platzhalter durch den Namen und den Speicherort der zu erstellenden Ressourcengruppe.

    ```bash
    ansible-playbook create_rg.yml --extra-vars "name=<resource_group_name> location=<resource_group_location>"
    ```

    **Hinweise**:

    - Aufgrund der Variablen `register` und des Abschnitts `debug` des Playbooks werden die Ergebnisse angezeigt, wenn der Befehl abgeschlossen ist.

### <a name="delete-an-azure-resource-group"></a>Löschen einer Azure-Ressourcengruppe

[!INCLUDE [ansible-delete-resource-group.md](ansible-delete-resource-group.md)]
