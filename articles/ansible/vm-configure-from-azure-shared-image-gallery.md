---
title: 'Tutorial: Erstellen einer VM oder einer VM-Skalierungsgruppe über die Azure Shared Image Gallery mit Ansible'
description: Es wird beschrieben, wie Sie Ansible verwenden, um basierend auf einem generalisierten Image in der Shared Image Gallery eine VM oder VM-Skalierungsgruppe zu erstellen.
keywords: Ansible, Azure, DevOps, Bash, Playbook, virtueller Computer, VM-Skalierungsgruppe, Shared Image Gallery
ms.topic: tutorial
ms.date: 10/14/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: c214daa3f63f88711ace9d41ffb9978d20551fc9
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240572"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Tutorial: Erstellen einer VM oder einer VM-Skalierungsgruppe über die Azure Shared Image Gallery mit Ansible

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

Die [Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) ist ein Dienst, der Ihnen das einfache Verwalten, gemeinsame Nutzen und Organisieren von benutzerdefinierten Images ermöglicht. Dieses Feature eignet sich gut für Szenarien, bei denen viele Images verwaltet und gemeinsam genutzt werden. Benutzerdefinierte Images können abonnementübergreifend und von mehreren Azure Active Directory-Mandanten gemeinsam genutzt werden. Darüber hinaus können Images auch in mehreren Regionen repliziert werden, um eine schnellere Bereitstellungsskalierung zu erzielen.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Erstellen einer generalisierten VM und eines benutzerdefinierten Images
> * Erstellen einer Shared Image Gallery-Instanz
> * Erstellen eines gemeinsam genutzten Images und einer Imageversion
> * Erstellen einer VM mit dem generalisierten Image
> * Erstellen einer VM-Skalierungsgruppe mit dem generalisierten Image
> * Abrufen von Informationen zu Ihrer Shared Image Gallery, zum Image und zur Version

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Beschaffen der Beispielplaybooks

Es gibt zwei Arten, wie die gesamten Beispielplaybooks beschafft werden können:

- [Laden Sie den SIG-Ordner](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) herunter, und speichern Sie ihn auf Ihrem lokalen Computer.
- Erstellen Sie für jeden Abschnitt eine neue Datei, und kopieren Sie das Beispielplaybook in die Datei.

Die Datei `vars.yml` enthält die Variablen, die von allen Beispielplaybooks für dieses Tutorial verwendet werden. Sie können die Datei bearbeiten, um eindeutige Namen und Werte anzugeben.

Mit dem ersten Playbook `00-prerequisites.yml` werden die Komponenten erstellt, die für dieses Tutorial erforderlich sind:
- Eine Ressourcengruppe: Dies ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.
- Ein virtuelles Netzwerk, ein Subnetz, eine öffentliche IP-Adresse und eine Netzwerkschnittstellenkarte für den virtuellen Computer.
- Ein virtueller Quellcomputer, der zum Erstellen des generalisierten Images verwendet wird.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 00-prerequisites.yml
```

Überprüfen Sie im [Azure-Portal](https://portal.azure.com) die Ressourcengruppe, die Sie in `vars.yml` angegeben haben, um den neuen virtuellen Computer und die von Ihnen erstellten Ressourcen anzuzeigen.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalisieren der VM und Erstellen eines benutzerdefinierten Images

Mit dem nächsten Playbook (`01a-create-generalized-image.yml`) wird die im vorherigen Schritt erstellte VM generalisiert, und anschließend wird basierend darauf ein benutzerdefiniertes Image erstellt.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Überprüfen Sie Ihre Ressourcengruppe, und stellen Sie sicher, dass `testimagea` angezeigt wird.

## <a name="create-the-shared-image-gallery"></a>Erstellen der Shared Image Gallery-Instanz

Die Image Gallery ist das Repository zum gemeinsamen Nutzen und Verwalten von Images. Mit dem Code des Beispielplaybooks in `02-create-shared-image-gallery.yml` wird in Ihrer Ressourcengruppe eine Shared Image Gallery-Instanz erstellt.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

In Ihrer Ressourcengruppe wird jetzt der neue Katalog `myGallery` angezeigt.

## <a name="create-a-shared-image-and-image-version"></a>Erstellen eines gemeinsam genutzten Images und einer Imageversion

Mit dem nächsten Playbook (`03a-create-shared-image-generalized.yml`) werden eine Imagedefinition und -version erstellt.

Imagedefinitionen enthalten den Imagetyp (Windows oder Linux), Versionshinweise und die Anforderungen in Bezug auf den minimalen und maximalen Arbeitsspeicher. Wie der Name schon sagt, ist die Imageversion die Version des Images. Mit der Gallery, der Imagedefinition und der Imageversion können Sie Images in logischen Gruppen organisieren. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Ihre Ressourcengruppe verfügt nun über eine Imagedefinition und -version für Ihre Gallery.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Erstellen einer VM basierend auf dem generalisierten Image

Führen Sie abschließend `04a-create-vm-using-generalized-image.yml` aus, um eine VM basierend auf dem generalisierten Image zu erstellen, das Sie im vorherigen Schritt erstellt haben.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Erstellen einer VM-Skalierungsgruppe basierend auf dem generalisierten Image

Sie können basierend auf dem generalisierten Image auch eine VM-Skalierungsgruppe erstellen. Führen Sie hierzu `05a-create-vmss-using-generalized-image.yml` aus.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Abrufen von Informationen zur Gallery

Sie können Informationen zur Gallery und zur Imagedefinition und -version abrufen, indem Sie `06-get-info.yml` ausführen.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Löschen des gemeinsam genutzten Images

Informationen zum Löschen der Gallery-Ressourcen finden Sie im Beispielplaybook `07-delete-gallery.yml`. Löschen Sie die Ressourcen in umgekehrter Reihenfolge. Löschen Sie zunächst die Imageversion. Nachdem alle Imageversionen gelöscht sind, können Sie die Imagedefinition löschen. Nachdem alle Imagedefinitionen gelöscht sind, können Sie den Katalog löschen.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. 

Der Playbook-Beispielcode in diesem Abschnitt wird für Folgendes verwendet:

- Löschen der beiden zuvor erstellten Ressourcengruppen

Speichern Sie das folgende Playbook als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Hier sind einige wichtige Hinweise aufgeführt, die Sie beim Arbeiten mit dem Beispielplaybook berücksichtigen sollten:

- Ersetzen Sie den Platzhalter `{{ resource_group_name }}` durch den Namen Ihrer Ressourcengruppe.
- Alle Ressourcen in den beiden angegebenen Ressourcengruppen werden gelöscht.

Führen Sie das Playbook mithilfe des Befehls `ansible-playbook` aus:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Ansible unter Azure](/azure/ansible/)