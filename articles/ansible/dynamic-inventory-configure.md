---
title: 'Tutorial: Konfigurieren von dynamischen Beständen Ihrer Azure-Ressourcen mithilfe von Ansible'
description: Hier erfahren Sie, wie Sie Ihre dynamischen Azure-Bestände mithilfe von Ansible verwalten.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, dynamischer Bestand
ms.topic: tutorial
ms.date: 10/30/2020
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: ff23b6d4d363e8b83e33414c6518560fa82b8ee0
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395265"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: Konfigurieren von dynamischen Beständen Ihrer Azure-Ressourcen mit Ansible

[!INCLUDE [ansible-28-note.md](includes/ansible-28-note.md)]

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

1. Öffnen Sie [Cloud Shell](/azure/cloud-shell/overview).

1. Erstellen Sie für die virtuellen Computer dieses Tutorials eine Azure-Ressourcengruppe.

    > [!IMPORTANT]
    > Für die in diesem Schritt erstellte Azure-Ressourcengruppe muss ein Name angegeben werden, der ausschließlich aus Kleinbuchstaben besteht. Andernfalls tritt bei der Erstellung des dynamischen Bestands ein Fehler auf.

    ```azurecli
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Erstellen Sie mithilfe eines der folgenden Verfahren zwei virtuelle Linux-Computer in Azure:

    - **Ansible-Playbook:** Wie Sie einen virtuellen Computer auf der Grundlage eines Ansible-Playbooks erstellen, erfahren Sie im Artikel [Erstellen eines einfachen virtuellen Computers in Azure mit Ansible](./vm-configure.md). Wenn Sie einen der virtuellen Computer (oder beide) mit einem Playbook definieren, verwenden Sie anstelle eines Kennworts die SSH-Verbindung.

    - **Azure-Befehlszeilenschnittstelle:** Führen Sie die folgenden Befehle in Cloud Shell aus, um die beiden virtuellen Computer zu erstellen:

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Kennzeichnen eines virtuellen Computers

Sie können [Ihre Azure-Ressourcen mithilfe von Tags nach benutzerdefinierten Kategorien organisieren](/azure/azure-resource-manager/resource-group-using-tags#azure-cli).

Geben Sie den folgenden Befehl vom Typ [az resource tag](/cli/azure/resource#az-resource-tag) ein, um den virtuellen Computer `ansible-inventory-test-vm1` mit dem Schlüssel `Ansible=nginx` zu markieren:

```azurecli
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

Ersetzen Sie `<YourAzureSubscriptionID>` durch die Abonnement-ID.

## <a name="generate-a-dynamic-inventory"></a>Generieren eines dynamischen Bestands

Nachdem Sie Ihre virtuellen Computer definiert (und markiert) haben, können Sie den dynamischen Bestand generieren.

Von Ansible wird ein [Azure-Plug-In für dynamische Bestände](https://github.com/ansible/ansible/blob/stable-2.9/lib/ansible/plugins/inventory/azure_rm.py) bereitgestellt. In den folgenden Schritten wird die Nutzung des Plug-Ins beschrieben:

1. Der dynamische Bestand muss auf `azure_rm` enden und die Erweiterung `yml` oder `yaml` aufweisen, andernfalls wird von Ansible nicht das richtige Bestands-Plug-In erkannt. Speichern Sie das folgende Playbook für dieses Tutorialbeispiel als `myazure_rm.yml`:

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

1. Standardmäßig ist die Hostschlüsselüberprüfung aktiviert, was zu folgendem Fehler führen kann.

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```

    Deaktivieren Sie die Hostschlüsselüberprüfung, indem Sie die Umgebungsvariable `ANSIBLE_HOST_KEY_CHECKING` auf `False` festlegen.

    ```bash
    export ANSIBLE_HOST_KEY_CHECKING=False
    ```

1. Beim Ausführen des Playbooks wird in etwa die folgende Ausgabe angezeigt:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Aktivieren des VM-Tags

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

1. Führen Sie das Playbook mithilfe von [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html) aus.

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

1. Rufen Sie mithilfe des Befehls [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) die IP-Adresse des virtuellen Computers `ansible-inventory-test-vm1` ab. Der zurückgegebene Wert (die IP-Adresse des virtuellen Computers) wird dann als Parameter für den SSH-Befehl verwendet, um die Verbindung mit dem virtuellen Computer herzustellen.

    ```azurecli
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

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./vm-configure.md)
