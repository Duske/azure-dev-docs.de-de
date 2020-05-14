---
title: 'Tutorial: Erstellen einer Azure-VM-Skalierungsgruppe unter Verwendung von Terraform'
description: Es wird beschrieben, wie Sie mit Terraform eine Azure-VM-Skalierungsgruppe konfigurieren und mit einer Version versehen.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: bb6175c92d0487bd5707b721ebc39ce4b727fed6
ms.sourcegitcommit: aa417af8b5f00cbc056666e481250ef45c661d52
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153727"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Tutorial: Erstellen einer Azure-VM-Skalierungsgruppe unter Verwendung von Terraform

[Azure-VM-Skalierungsgruppen](/azure/virtual-machine-scale-sets) ermöglichen die Konfiguration identischer virtueller Computer. Die Anzahl von VM-Instanzen kann bedarfs- zeitplangesteuert angepasst werden. Weitere Informationen finden Sie unter [Automatisches Skalieren einer VM-Skalierungsgruppe im Azure-Portal](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Terraform-Bereitstellung
> * Verwenden von Variablen und Ausgaben für die Terraform-Bereitstellung
> * Erstellen und Bereitstellen der Netzwerkinfrastruktur
> * Erstellen und Bereitstellen einer VM-Skalierungsgruppe und Anfügen an das Netzwerk
> * Erstellen und Bereitstellen einer Jumpbox zum Herstellen einer SSH-Verbindung mit den virtuellen Computern

> [!NOTE]
> Die neueste Version der in diesem Artikel verwendeten Terraform-Konfigurationsdateien befinden sich im [Repository „awesome-terraform“](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss) auf GitHub.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

- **Installieren von Terraform**: Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Terraform zum Bereitstellen von VMs und sonstiger Infrastruktur in Azure](install-configure.md).

- **Erstellen eines SSH-Schlüsselpaars**: Weitere Informationen finden Sie unter [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `vmss`.

    ```bash
    mkdir vmss
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Erstellen der Variablendefinitionsdatei
In diesem Abschnitt definieren Sie die Variablen zur Anpassung der durch Terraform erstellten Ressourcen.

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

   ```hcl
   variable "location" {
    description = "The location where resources will be created"
   }

   variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = map

    default = {
      environment = "codelab"
    }
   }

   variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
   }
   ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="create-the-output-definitions-file"></a>Erstellen der Ausgabedefinitionsdatei
In diesem Abschnitt erstellen Sie die Datei, die die Ausgabe nach der Bereitstellung definiert.

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `output.tf`.

    ```bash
    code output.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein, um den vollqualifizierten Domänennamen (FQDN) für die virtuellen Computer verfügbar zu machen.
   decodiert werden:

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Definieren der Netzwerkinfrastruktur in einer Vorlage
In diesem Abschnitt erstellen Sie in einer neuen Azure-Ressourcengruppe die folgende Netzwerkinfrastruktur:

  - Ein virtuelles Netzwerk (VNET) mit dem Adressraum 10.0.0.0/16
  - Ein Subnetz mit dem Adressraum 10.0.2.0/24
  - Zwei öffentliche IP-Adressen: eine für den Lastenausgleich der VM-Skalierungsgruppe, eine für die Verbindungsherstellung mit der SSH-Jumpbox

Führen Sie in der Azure Cloud Shell-Instanz die folgenden Schritte aus:

1. Erstellen Sie eine Datei mit dem Namen `vmss.tf` zur Beschreibung der Infrastruktur der VM-Skalierungsgruppe.

    ```bash
    code vmss.tf
    ```

1. Fügen Sie den folgenden Code am Ende der Datei ein, um den vollqualifizierten Domänennamen (FQDN) für die virtuellen Computer verfügbar zu machen.

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
   }

   resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
   }

   resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
   }

   resource "azurerm_subnet" "vmss" {
    name                 = "vmss-subnet"
    resource_group_name  = azurerm_resource_group.vmss.name
    virtual_network_name = azurerm_virtual_network.vmss.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "vmss" {
    name                         = "vmss-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="provision-the-network-infrastructure"></a>Bereitstellen der Netzwerkinfrastruktur
Führen Sie unter Verwendung der Azure Cloud Shell-Instanz in dem Verzeichnis, in dem Sie die Konfigurationsdateien (TF-Dateien) erstellt haben, die folgenden Schritte aus:

1. Initialisieren Sie Terraform.

   ```bash
   terraform init
   ```

1. Führen Sie den folgenden Befehl aus, um die definierte Infrastruktur in Azure bereitzustellen.

   ```bash
   terraform apply
   ```

   Terraform fordert Sie zur Eingabe eines Werts vom Typ `location` auf, da die Variable `location` zwar in `variables.tf` definiert ist, aber nicht festgelegt wurde. Sie können also einen beliebigen gültigen Speicherort – z.B. „USA, Westen“ eingeben und dann die EINGABETASTE drücken. (Bei allen Werten mit Leerzeichen sind Klammern zu verwenden.)

1. Terraform gibt die Daten aus, wie sie in der `output.tf`-Datei definiert sind. Der folgende Screenshot zeigt das Format des FQDN: `<ID>.<location>.cloudapp.azure.com`. Die ID ist ein berechneter Wert, und der Standort entspricht dem Wert, der beim Ausführen von Terraform angegeben wurde.

   ![VM-Skalierungsgruppe: vollqualifizierter Domänenname für die öffentliche IP-Adresse](./media/create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Wählen Sie im Hauptmenü des Azure-Portals **Ressourcengruppen** aus.

1. Wählen Sie auf der Registerkarte **Ressourcengruppen** **myResourceGroup** aus, damit die von Terraform erstellten Ressourcen angezeigt werden.
   ![Netzwerkressourcen der VM-Skalierungsgruppe](./media/create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Hinzufügen einer VM-Skalierungsgruppe

In diesem Abschnitt erfahren Sie, wie sich der Vorlage die folgenden Ressourcen hinzufügen lassen:

- Einen Azure-Lastenausgleich und Regeln, um die Anwendung zu versorgen und an die öffentliche IP-Adresse anzufügen, deren Konfiguration oben im Artikel beschrieben wurde
- Einen Azure-Back-End-Adresspool, der dem Lastenausgleich zugewiesen werden muss
- Einen Integritätstestport, der von der Anwendung verwendet und für den Lastenausgleich konfiguriert wird
- Eine VM-Skalierungsgruppe, die sich hinter dem Lastenausgleich befindet und in dem VNET ausgeführt wird, dessen Bereitstellung oben im Artikel beschrieben wurde
- [Nginx](https://nginx.org/) auf den Knoten der VM-Skalierungsgruppe mit [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/)

Führen Sie in Cloud Shell die folgenden Schritte aus:

1. Öffnen Sie die `vmss.tf`-Konfigurationsdatei.

   ```bash
   code vmss.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
   }

   resource "azurerm_lb_backend_address_pool" "bpepool" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_lb_probe" "vmss" {
    resource_group_name = azurerm_resource_group.vmss.name
    loadbalancer_id     = azurerm_lb.vmss.id
    name                = "ssh-running-probe"
    port                = var.application_port
   }

   resource "azurerm_lb_rule" "lbnatrule" {
      resource_group_name            = azurerm_resource_group.vmss.name
      loadbalancer_id                = azurerm_lb.vmss.id
      name                           = "http"
      protocol                       = "Tcp"
      frontend_port                  = var.application_port
      backend_port                   = var.application_port
      backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
      frontend_ip_configuration_name = "PublicIPAddress"
      probe_id                       = azurerm_lb_probe.vmss.id
   }

   resource "azurerm_virtual_machine_scale_set" "vmss" {
    name                = "vmscaleset"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    upgrade_policy_mode = "Manual"

    sku {
      name     = "Standard_DS1_v2"
      tier     = "Standard"
      capacity = 2
    }

    storage_profile_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = ""
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    storage_profile_data_disk {
      lun          = 0
      caching        = "ReadWrite"
      create_option  = "Empty"
      disk_size_gb   = 10
    }

    os_profile {
      computer_name_prefix = "vmlab"
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    network_profile {
      name    = "terraformnetworkprofile"
      primary = true

      ip_configuration {
        name                                   = "IPConfiguration"
        subnet_id                              = azurerm_subnet.vmss.id
        load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
        primary = true
      }
    }

    tags = var.tags
   }
   ```

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

    ```bash
    :wq
    ```

1. Erstellen Sie eine Datei mit dem Namen `web.conf`. Sie dient als Cloud-Init-Konfiguration für die virtuellen Computer, die zur Skalierungsgruppe gehören.

    ```bash
    code web.conf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Speichern Sie die Datei, und geben Sie den folgenden Befehl ein, um den vi-Editor zu schließen:

     ```bash
     :wq
     ```

1. Öffnen Sie die `variables.tf`-Konfigurationsdatei.

    ```bash
    code variables.tf
    ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Passen Sie die Bereitstellung an, indem Sie am Ende der Datei den folgenden Code einfügen:

    ```hcl
    variable "application_port" {
       description = "The port that you want to expose to the external load balancer"
       default     = 80
    }

    variable "admin_user" {
       description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
       default     = "azureuser"
    }

    variable "admin_password" {
       description = "Default password for admin account"
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Erstellen Sie einen Terraform-Plan, um die Bereitstellung der VM-Skalierungsgruppe zu visualisieren. (Hierbei ist die Angabe eines Kennworts Ihrer Wahl und eines Speicherorts für Ihre Ressourcen erforderlich.)

    ```bash
    terraform plan
    ```

    Die Ausgabe des Befehls sollte ungefähr so wie auf dem folgenden Screenshot aussehen:

    ![Ausgabe des Erstellens der VM-Skalierungsgruppe](./media/create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Stellen Sie die neuen Ressourcen in Azure bereit.

    ```bash
    terraform apply
    ```

    Die Ausgabe des Befehls sollte ungefähr so wie auf dem folgenden Screenshot aussehen:

    ![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Öffnen Sie einen Browser, und stellen Sie eine Verbindung mit dem FQDN her, der durch den Befehl zurückgegeben wurde.

    ![Ergebnisse des Navigierens zum FQDN](./media/create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Hinzufügen einer SSH-Jumpbox
Bei einer SSH-*Jumpbox* handelt es sich um einen Einzelserver, durch den Sie sozusagen „springen“, um auf andere Server im Netzwerk zuzugreifen. In diesem Schritt konfigurieren Sie die folgenden Ressourcen:

- Eine Netzwerkschnittstelle (oder Jumpbox), die mit dem gleichen Subnetz verbunden ist wie die VM-Skalierungsgruppe

- Einen virtuellen Computer, der mit dieser Netzwerkschnittstelle verbunden ist. Auf diese Jumpbox kann remote zugegriffen werden. Nach der Verbindungsherstellung können Sie eine SSH-Verbindung mit einem beliebigen virtuellen Computer in der Skalierungsgruppe herstellen.

1. Öffnen Sie die `vmss.tf`-Konfigurationsdatei.

   ```bash
   code vmss.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
   }

   resource "azurerm_network_interface" "jumpbox" {
    name                = "jumpbox-nic"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    ip_configuration {
      name                          = "IPConfiguration"
      subnet_id                     = azurerm_subnet.vmss.id
      private_ip_address_allocation = "dynamic"
      public_ip_address_id          = azurerm_public_ip.jumpbox.id
    }

    tags = var.tags
   }

   resource "azurerm_virtual_machine" "jumpbox" {
    name                  = "jumpbox"
    location              = var.location
    resource_group_name   = azurerm_resource_group.vmss.name
    network_interface_ids = [azurerm_network_interface.jumpbox.id]
    vm_size               = "Standard_DS1_v2"

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "jumpbox-osdisk"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    os_profile {
      computer_name  = "jumpbox"
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Öffnen Sie die `output.tf`-Konfigurationsdatei.

   ```bash
   code output.tf
   ```

1. Gehen Sie ans Ende der Datei und drücken Sie die A-TASTE, um den Anfügemodus zu starten.

1. Fügen Sie am Ende der Datei den folgenden Code hinzu, um nach Abschluss der Bereitstellung den Hostnamen der Jumpbox anzuzeigen:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Stellen Sie die Jumpbox bereit.

   ```bash
   terraform apply
   ```

Nach Abschluss der Bereitstellung sieht der Inhalt der Ressourcengruppe wie derjenige auf dem folgenden Screenshot aus:

![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Die Anmeldung mit einem Kennwort ist für die bereitgestellte Jumpbox und für die bereitgestellte VM-Skalierungsgruppe deaktiviert. Melden Sie sich mit SSH an, um auf die virtuellen Computer zuzugreifen.

## <a name="environment-cleanup"></a>Bereinigen der Umgebung

Wenn Sie die in diesem Tutorial erstellten Terraform-Ressourcen löschen möchten, geben Sie in Cloud Shell den folgenden Befehl ein:

```bash
terraform destroy
```

Die Bereinigung kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)
