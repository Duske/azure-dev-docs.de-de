---
title: 'Schnellstart: Konfigurieren von virtuellen Linux-Computern in Azure mit Ansible'
description: In diesem Schnellstart erfahren Sie, wie Sie einen virtuellen Linux-Computer in Azure mit Ansible erstellen.
keywords: Ansible, Azure, DevOps, virtueller Computer
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: c9f6d9a954b8bdbd05ede42cc69a0078eae3cea2
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240432"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Schnellstart: Erstellen eines virtuellen Linux-Computers in Azure mithilfe von Ansible

Ansible nutzt eine deklarative Sprache und ermöglicht Ihnen dadurch das Erstellen, Konfigurieren und Bereitstellen von Azure-Ressourcen über Ansible-*Playbooks*. Dieser Artikel enthält ein Ansible-Beispielplaybook für die Konfiguration von virtuellen Linux-Computern. Das [vollständige Ansible-Playbook](#complete-sample-ansible-playbook) finden Sie am Ende dieses Artikels.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Ansible benötigt eine Ressourcengruppe, in der Ihre Ressourcen bereitgestellt werden. Der folgende Abschnitt eines Ansible-Beispielplaybooks erstellt eine Ressourcengruppe namens `myResourceGroup` am Standort `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Beim Erstellen eines virtuellen Azure-Computers müssen Sie ein [virtuelles Netzwerk](/azure/virtual-network/virtual-networks-overview) erstellen oder ein vorhandenes virtuelles Netzwerk verwenden. Außerdem müssen Sie entscheiden, wie auf Ihre virtuellen Computer im virtuellen Netzwerk zugegriffen werden soll. Der folgende Abschnitt eines Ansible-Beispielplaybooks erstellt ein virtuelles Netzwerk mit dem Namen `myVnet` im Adressbereich `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Alle in einem virtuellen Netzwerk bereitgestellten Azure-Ressourcen werden in einem [Subnetz](/azure/virtual-network/virtual-network-manage-subnet) innerhalb eines virtuellen Netzwerks bereitgestellt. 

Der folgende Abschnitt eines Ansible-Beispielplaybooks erstellt ein Subnetz mit dem Namen `mySubnet` im virtuellen Netzwerk `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse





[Öffentliche IP-Adressen](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) ermöglichen Internetressourcen die eingehende Kommunikation mit Azure-Ressourcen. Öffentliche IP-Adressen ermöglichen Azure-Ressourcen außerdem die ausgehende Kommunikation mit öffentlichen Azure-Diensten. In beiden Szenarien wird der Ressource, auf die zugegriffen wird, eine IP-Adresse zugewiesen. Die Adresse ist für die Ressource reserviert, bis Sie die Zuweisung aufheben. Wenn einer Ressource keine öffentliche IP-Adresse zugewiesen ist, kann die Ressource trotzdem ausgehend mit dem Internet kommunizieren. Die Verbindung erfolgt, indem Azure dynamisch eine verfügbare IP-Adresse zuweist. Die dynamisch zugewiesene Adresse ist nicht für die Ressource reserviert.

Der folgende Abschnitt eines Ansible-Beispielplaybooks erstellt eine öffentliche IP-Adresse mit dem Namen `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

[Netzwerksicherheitsgruppen](/azure/virtual-network/security-overview) filtern den Netzwerkdatenverkehr zwischen Azure-Ressourcen in einem virtuellen Netzwerk. Es werden Sicherheitsregeln definiert, die eingehenden und ausgehenden Datenverkehr von und zu Azure-Ressourcen steuern. Weitere Informationen zu Azure-Ressourcen und Netzwerksicherheitsgruppen finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](/azure/virtual-network/virtual-network-for-azure-services).

Mit dem folgenden Playbook wird eine Netzwerksicherheitsgruppe namens `myNetworkSecurityGroup` erstellt. Die Netzwerksicherheitsgruppe schließt eine Regel ein, die SSH-Datenverkehr an TCP-Port 22 zulässt.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Erstellen einer virtuellen Netzwerkadapterkarte

Ein virtueller Netzwerkadapterkarte verbindet Ihren virtuellen Computer mit einem angegebenen virtuellen Netzwerk, einer öffentlichen IP-Adresse und einer Netzwerksicherheitsgruppe. 

Im folgenden Abschnitt eines Ansible-Beispielplaybooks wird die virtuelle Netzwerkadapterkarte `myNIC` erstellt, die mit den von Ihnen erstellen virtuellen Netzwerkressourcen verbunden ist:

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Im letzten Schritt wird ein virtueller Computer erstellt, der alle Ressourcen verwendet, die Sie in den vorherigen Abschnitten dieses Artikels erstellt haben. 

Mit dem Abschnitt des Ansible-Beispielplaybooks in diesem Abschnitt wird ein virtueller Computer namens `myVM` erstellt und die virtuelle Netzwerkadapterkarte `myNIC` angefügt. Ersetzen Sie den Platzhalter „&lt;your-key-data>“ durch die Daten Ihres eigenen öffentlichen Schlüssels.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Vollständiges Ansible-Beispielplaybook

Dieser Abschnitt enthält das gesamte Ansible-Beispielplaybook, das Sie im Rahmen dieses Artikels erstellt haben. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network interface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Ausführen des Ansible-Beispielplaybooks

In diesem Abschnitt wird die Ausführung des in diesem Artikel vorgestellten Ansible-Beispielplaybooks Schritt für Schritt erläutert.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie [Cloud Shell](/azure/cloud-shell/overview).

1. Erstellen Sie für Ihr Playbook eine Datei namens `azure_create_complete_vm.yml`, und öffnen Sie sie wie folgt im vi-Editor:

   ```bash
   vi azure_create_complete_vm.yml
   ```

1. Drücken Sie die **I-TASTE**, um den Einfügemodus zu starten.

1. Fügen Sie das [vollständige Ansible-Beispielplaybook](#complete-sample-ansible-playbook) in den Editor ein.

1. Drücken Sie die **ESC**-TASTE, um den Einfügemodus zu beenden.

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

1. Führen Sie das Ansible-Beispielplaybook aus.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Die Ausgabe sieht etwa wie im folgenden Beispiel aus, das zeigt, dass erfolgreich ein virtueller Computer erstellt wurde:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. Für den Zugriff auf den virtuellen Linux-Computer wird der SSH-Befehl verwendet. Ersetzen Sie den Platzhalter „&lt;ip-address>“ durch die IP-Adresse aus dem vorherigen Schritt.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Schnellstart: Verwalten eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./vm-manage.md)
