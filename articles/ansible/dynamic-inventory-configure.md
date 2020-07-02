---
title: 'Tutorial: Konfigurieren von dynamischen Beständen Ihrer Azure-Ressourcen mithilfe von Ansible'
description: Hier erfahren Sie, wie Sie Ihre dynamischen Azure-Bestände mithilfe von Ansible verwalten.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, dynamischer Bestand
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 8ad48c49094c19087318434cd02c38dcb1857a76
ms.sourcegitcommit: 7da78b35a847db9929554962dfcc47860f472fb9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133623"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: Konfigurieren von dynamischen Beständen Ihrer Azure-Ressourcen mit Ansible

Mit Ansible können Bestandsinformationen aus verschiedenen Quellen (einschließlich Cloudquellen wie Azure) in einen *dynamischen Bestand* abgerufen werden. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurieren von zwei virtuellen Testcomputern 
> * Markieren von einem virtuellen Computer per Tag
> * Installieren von Nginx auf markierten virtuellen Computern
> * Konfigurieren eines dynamischen Bestands, der die konfigurierten Azure-Ressourcen enthält

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Erstellen der virtuellen Testcomputer

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Erstellen Sie für die virtuellen Computer dieses Tutorials eine Azure-Ressourcengruppe.

    > [!IMPORTANT]    
    > Für die in diesem Schritt erstellte Azure-Ressourcengruppe muss ein Name angegeben werden, der ausschließlich aus Kleinbuchstaben besteht. Andernfalls tritt bei der Erstellung des dynamischen Bestands ein Fehler auf.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Erstellen Sie mithilfe eines der folgenden Verfahren zwei virtuelle Linux-Computer in Azure:

    - **Ansible-Playbook:** Wie Sie einen virtuellen Computer auf der Grundlage eines Ansible-Playbooks erstellen, erfahren Sie im Artikel [Erstellen eines einfachen virtuellen Computers in Azure mit Ansible](./vm-configure.md). Wenn Sie einen der virtuellen Computer (oder beide) mit einem Playbook definieren, verwenden Sie anstelle eines Kennworts die SSH-Verbindung.

    - **Azure-Befehlszeilenschnittstelle:** Führen Sie die folgenden Befehle in Cloud Shell aus, um die beiden virtuellen Computer zu erstellen:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Kennzeichnen eines virtuellen Computers

Sie können [Ihre Azure-Ressourcen mithilfe von Tags nach benutzerdefinierten Kategorien organisieren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli). 

### <a name="using-ansible-version--28"></a>Verwendung einer früheren Version als Ansible 2.8
Geben Sie den folgenden Befehl vom Typ [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) ein, um den virtuellen Computer `ansible-inventory-test-vm1` mit dem Schlüssel `nginx` zu markieren:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Verwendung von Ansible 2.8 oder einer älteren Version
Geben Sie den folgenden Befehl vom Typ [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) ein, um den virtuellen Computer `ansible-inventory-test-vm1` mit dem Schlüssel `Ansible=nginx` zu markieren:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generieren eines dynamischen Bestands

Nachdem Sie Ihre virtuellen Computer definiert (und markiert) haben, können Sie den dynamischen Bestand generieren.

### <a name="using-ansible-version--28"></a>Verwendung einer früheren Version als Ansible 2.8

Unter Ansible wird ein Python-Skript mit dem Namen [azure_rm.py](https://github.com/ansible-collections/community.general/blob/master/scripts/inventory/azure_rm.py) bereitgestellt, mit dem ein dynamischer Bestand Ihrer Azure-Ressourcen generiert wird. Die folgende Anleitung zeigt Schritt für Schritt, wie Sie unter Verwendung des Skripts `azure_rm.py` eine Verbindung mit Ihren beiden virtuellen Azure-Testcomputern herstellen:

1. Rufen Sie mithilfe des GNU-Befehls `wget` das Skript `azure_rm.py` ab:

    ```python
    wget https://raw.githubusercontent.com/ansible-collections/community.general/master/scripts/inventory/azure_rm.py
    ```

1. Ändern Sie mithilfe des Befehls `chmod` die Zugriffsberechtigungen für das Skript `azure_rm.py`. Im folgenden Befehl wird der Parameter `+x` verwendet, um die Ausführung der angegebenen Datei (`azure_rm.py`) zuzulassen:

    ```python
    chmod +x azure_rm.py
    ```

1. Verwenden Sie den [ansible-Befehl](https://docs.ansible.com/ansible/2.4/ansible.html), um eine Verbindung mit Ihrer Ressourcengruppe herzustellen: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Nach der Verbindungsherstellung werden Ergebnisse angezeigt. Diese sehen in etwa wie in der folgenden Ausgabe aus:

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Ansible-Version >= 2.8

Ab Ansible 2.8 wird ein [Azure-Plug-In für dynamische Bestände](https://github.com/ansible/ansible/blob/stable-2.9/lib/ansible/plugins/inventory/azure_rm.py) bereitgestellt. In den folgenden Schritten wird die Nutzung des Plug-Ins beschrieben:

1. Für das Bestands-Plug-In ist eine Konfigurationsdatei erforderlich. Die Konfigurationsdatei muss auf `azure_rm` enden und als Erweiterung entweder `yml` oder `yaml` aufweisen. Speichern Sie das folgende Playbook für dieses Tutorialbeispiel als `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Führen Sie den folgenden Befehl aus, um virtuelle Computer in der Ressourcengruppe zu pingen:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Bei Ausführung des obigen Befehls erhalten Sie ggf. den folgenden Fehler:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Fügen Sie der Ansible-Konfigurationsdatei die folgende Zeile hinzu, falls Sie den Fehler vom Typ „Host-Key Verification“ erhalten. Die Ansible-Konfigurationsdatei befindet sich unter `/etc/ansible/ansible.cfg` oder `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Aktivieren des VM-Tags

### <a name="if-youre-using-ansible--28"></a>Bei Verwendung einer älteren Version als Ansible 2.8

- Nachdem Sie ein Tag festgelegt haben, müssen Sie es „aktivieren“. Eine Möglichkeit zum Aktivieren eines Tags ist der Export des Tags in die Umgebungsvariable `AZURE_TAGS` mit dem Befehl `export`:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Führen Sie den folgenden Befehl aus:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Nun wird nur noch ein einzelner virtueller Computer angezeigt (der Computer, dessen Tag dem Wert entspricht, der in die Umgebungsvariable `AZURE_TAGS` exportiert wurde):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Bei Verwendung von Ansible 2.8 oder einer höheren Version

- Führen Sie den Befehl `ansible-inventory -i myazure_rm.yml --graph` aus, um die folgende Ausgabe zu erhalten:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Sie können auch den folgenden Befehl ausführen, um die Verbindung mit der Nginx-VM zu testen:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Einrichten von Nginx auf dem markierten virtuellen Computer

Das Tag ermöglicht die schnelle und einfache Verwendung von Untergruppen virtueller Computer. Ein Beispiel: Angenommen, Sie möchten Nginx nur auf virtuellen Computern installieren, denen Sie das Tag `nginx` zugewiesen haben. Gehen Sie hierzu wie folgt vor:

1. Erstellen Sie eine Datei mit dem Namen `nginx.yml`:

   ```console
   code nginx.yml
   ```

1. Fügen Sie den folgenden Beispielcode in den Editor ein:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Speichern Sie die Datei, und beenden Sie den Editor.

1. Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

   - Ansible < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2.8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Nach dem Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testen der Nginx-Installation

Dieser Abschnitt beschreibt ein Verfahren, mit dem Sie sich vergewissern können, dass Nginx auf Ihrem virtuellen Computer installiert ist.

1. Rufen Sie mithilfe des Befehls [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) die IP-Adresse des virtuellen Computers `ansible-inventory-test-vm1` ab. Der zurückgegebene Wert (die IP-Adresse des virtuellen Computers) wird dann als Parameter für den SSH-Befehl verwendet, um die Verbindung mit dem virtuellen Computer herzustellen.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Während eine Verbindung mit dem virtuellen Computer `ansible-inventory-test-vm1` besteht, führen Sie den Befehl [nginx -v](https://nginx.org/en/docs/switches.html) aus, um zu ermitteln, ob Nginx installiert ist.

    ```console
    nginx -v
    ```

1. Nach Ausführung des Befehls `nginx -v` wird die Nginx-Version (zweite Zeile) angezeigt. Das bedeutet, dass Nginx installiert ist.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Drücken Sie die Tastenkombination `<Ctrl>D`, um die Verbindung der SSH-Sitzung zu trennen.

1. Wenn Sie die obigen Schritte für den virtuellen Computer `ansible-inventory-test-vm2` ausführen, erscheint eine Informationsmeldung mit dem Hinweis, wo Sie Nginx beziehen können (dies impliziert, dass die Anwendung noch nicht installiert ist):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./vm-configure.md)
