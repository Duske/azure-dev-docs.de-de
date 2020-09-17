---
title: 'Tutorial: Aktualisieren des benutzerdefinierten Image von Azure-VM-Skalierungsgruppen mithilfe von Ansible'
description: Informationen zum Verwenden von Ansible zum Aktualisieren von VM-Skalierungsgruppen mit benutzerdefiniertem Image in Azure
keywords: ansible, azure, devops, bash, playbook, vm, vm-skalierungsgruppen, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 85fe3ec53b0a32a06ded0bbe0da47c62e15fcf41
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681838"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Tutorial: Aktualisieren des benutzerdefinierten Image von Skalierungsgruppen für virtuelle Azure-Computer mit Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

Nach der Bereitstellung eines virtuellen Computers konfigurieren Sie ihn mit der für Ihre App erforderlichen Software. Anstatt diese Konfigurationsaufgabe für jeden virtuellen Computer durchzuführen, können Sie ein benutzerdefiniertes Image erstellen. Ein benutzerdefiniertes Image ist eine Momentaufnahme eines vorhandenen virtuellen Computers, welche die gesamte installierte Software enthält. Beim [Konfigurieren einer Skalierungsgruppe](./vm-scale-set-configure.md) geben Sie das Image an, das für die virtuellen Computer dieser Skalierungsgruppe verwendet werden soll. Durch die Verwendung eines benutzerdefinierten Images wird jede VM-Instanz für Ihre App identisch konfiguriert. Bisweilen müssen Sie das benutzerdefinierte Image Ihrer Skalierungsgruppe aktualisieren. Um diese Aufgabe geht es in diesem Tutorial.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurieren von zwei virtuellen Computern mit HTTPD
> * Erstellen eines benutzerdefinierten Image von einem vorhandenen virtuellen Computer
> * Erstellen einer Skalierungsgruppe aus einem Image
> * Aktualisieren des benutzerdefinierten Image

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Konfigurieren von zwei virtuellen Computern

Mit dem Playbookcode in diesem Abschnitt werden zwei virtuelle Computer erstellt, auf denen jeweils HTTPD installiert ist. 

Die Seite `index.html` zeigt für jeden virtuellen Computer eine Testzeichenfolge an:

* Der erste virtuelle Computer zeigt den Wert `Image A` an.
* Der zweite virtuelle Computer zeigt den Wert `Image B` an.

Mit diese Zeichenfolge soll das Konfigurieren der virtuellen Computer mit jeweils anderer Software imitiert werden.

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml), und speichern Sie es als `create_vms.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `create_vms.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Führen Sie das Playbook mit dem Befehl `ansible-playbook` aus, und ersetzen Sie dabei `myrg` durch den Namen Ihrer Ressourcengruppe:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Aufgrund der `debug`-Abschnitte des Playbooks gibt der Befehl `ansible-playbook` die IP-Adressen der einzelnen virtuellen Computer aus. Kopieren Sie diese IP-Adressen für die spätere Verwendung.

![IP-Adressen der virtuellen Computer](media/vm-scale-set-update-image/ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Herstellen einer Verbindung mit den beiden virtuellen Computern

In diesem Abschnitt stellen Sie eine Verbindung mit beiden virtuellen Computern her. Wie im vorherigen Abschnitt erwähnt, imitieren die Zeichenfolgen `Image A` und `Image B` zwei verschiedene virtuelle Computer mit unterschiedlichen Konfigurationen.

Verwenden Sie die IP-Adressen aus dem vorherigen Abschnitt, um eine Verbindung mit beiden virtuellen Computern herzustellen:

![Screenshot des virtuellen Computers A](media/vm-scale-set-update-image/vm-a.png)

![Screenshot des virtuellen Computers B](media/vm-scale-set-update-image/vm-b.png)

## <a name="create-images-from-each-vm"></a>Erstellen von Images von jedem virtuellen Computer

An dieser Stelle haben Sie zwei virtuelle Computer mit geringfügig unterschiedlichen Konfigurationen (ihre `index.html`-Dateien).

Mit dem Playbookcode in diesem Abschnitt wird für jeden virtuellen Computer ein benutzerdefiniertes Image erstellt.

* `image_vmforimageA`: benutzerdefiniertes Images für den virtuellen Computer, auf dessen Startseite `Image A` angezeigt wird
* `image_vmforimageB`: benutzerdefiniertes Images für den virtuellen Computer, auf dessen Startseite `Image B` angezeigt wird

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml), und speichern Sie es als `capture-images.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `capture-images.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Führen Sie das Playbook mit dem Befehl `ansible-playbook` aus, und ersetzen Sie dabei `myrg` durch den Namen Ihrer Ressourcengruppe:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Erstellen der Skalierungsgruppe mit Image A

In diesem Abschnitt wird ein Playbook verwendet, um die folgenden Azure-Ressourcen zu konfigurieren:

* Öffentliche IP-Adresse
* Load Balancer
* Skalierungsgruppe, die auf `image_vmforimageA` verweist

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml), und speichern Sie es als `create-vmss.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `create-vmss.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Führen Sie das Playbook mit dem Befehl `ansible-playbook` aus, und ersetzen Sie dabei `myrg` durch den Namen Ihrer Ressourcengruppe:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Aufgrund des `debug`-Abschnitt des Playbooks gibt der Befehl `ansible-playbook` die IP-Adresse der Skalierungsgruppe aus. Kopieren Sie diese IP-Adresse für die spätere Verwendung.

![Öffentliche IP-Adresse](media/vm-scale-set-update-image/public-ip.png)

## <a name="connect-to-the-scale-set"></a>Herstellen einer Verbindung mit der Skalierungsgruppe

In diesem Abschnitt stellen Sie eine Verbindung mit der Skalierungsgruppe her. 

Verwenden Sie die IP-Adresse aus dem vorherigen Abschnitt, um eine Verbindung mit der Skalierungsgruppe herzustellen:

Wie im vorherigen Abschnitt erwähnt, imitieren die Zeichenfolgen `Image A` und `Image B` zwei verschiedene virtuelle Computer mit unterschiedlichen Konfigurationen.

Die Skalierungsgruppe verweist auf das benutzerdefinierte Image namens `image_vmforimageA`. Das benutzerdefinierte Image `image_vmforimageA` wurde aus dem virtuellen Computer erstellt, auf dessen Startseite `Image A` angezeigt wird.

Daher sehen Sie eine Startseite, die `Image A`anzeigt:

![Die Skalierungsgruppe ist dem ersten virtuellen Computer zugeordnet.](media/vm-scale-set-update-image/initial-vm-scale-set.png)

Lassen Sie das Browserfenster geöffnet, während Sie mit dem nächsten Abschnitt fortfahren.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Ändern des benutzerdefinierte Image in der Skalierungsgruppe und Aktualisieren von Instanzen

Mit dem Playbookcode in diesem Abschnitt wird das Image der Skalierungsgruppe von `image_vmforimageA` in `image_vmforimageB` geändert. Außerdem werden alle derzeit bereitgestellten virtuellen Computer von der Skalierungsgruppe aktualisiert.

Es gibt zwei Möglichkeiten, das Beispielplaybook abzurufen:

* [Laden Sie das Playbook herunter](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml), und speichern Sie es als `update-vmss-image.yml`.
* Erstellen Sie eine neue Datei mit dem Namen `update-vmss-image.yml`, und kopieren Sie die folgenden Inhalte in diese Datei:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Führen Sie das Playbook mit dem Befehl `ansible-playbook` aus, und ersetzen Sie dabei `myrg` durch den Namen Ihrer Ressourcengruppe:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Kehren Sie zum Browser zurück, und aktualisieren Sie die Seite. 

Sie sehen, dass das dem virtuellen Computer zugrunde liegende benutzerdefinierte Image aktualisiert wurde.

![Die Skalierungsgruppe ist dem zweiten virtuellen Computer zugeordnet.](media/vm-scale-set-update-image/updated-vm-scale-set.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible)