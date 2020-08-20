---
title: Erstellen eines Azure-VM-Clusters mit Terraform und HCL
description: Es wird beschrieben, wie Sie in Azure mit Terraform und HCL einen Cluster mit virtuellen Linux-Computern mit Lastenausgleich erstellen.
keywords: Azure DevOps Terraform VM virtueller Computer Cluster
ms.topic: how-to
ms.date: 03/09/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: b0de2ed0c82f26902470835658ba7bb34f1acd7a
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241202"
---
# <a name="create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Erstellen eines Azure-VM-Clusters mit Terraform und HCL

In diesem Artikel erfahren Sie, wie Sie unter Verwendung von [HCL](https://www.terraform.io/docs/configuration/syntax.html) einen kleinen Computecluster erstellen. 

Dabei lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten der Azure-Authentifizierung
> * Erstellen einer Terraform-Konfigurationsdatei
> * Verwenden einer Terraform-Konfigurationsdatei, um einen Lastausgleich zu erstellen
> * Verwenden einer Terraform-Konfigurationsdatei, um zwei virtuelle Linux-Computer in einer Verfügbarkeitsgruppe bereitzustellen
> * Initialisieren Sie Terraform.
> * Erstellen eines Terraform-Ausführungsplans
> * Anwenden des Terraform-Ausführungsplans, um die Azure-Ressourcen zu erstellen

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="1-create-a-terraform-configuration-file"></a>1. Erstellen einer Terraform-Konfigurationsdatei

In diesem Abschnitt erstellen Sie eine Datei, die Ressourcendefinitionen für die Infrastruktur enthält.

1. Erstellen Sie eine neue Datei mit dem Namen `main.tf`. 

2. Kopieren Sie die folgenden Beispielressourcendefinitionen in die neu erstellte Datei `main.tf`: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="2-initialize-terraform"></a>2. Initialisieren von Terraform

Mit dem Befehl [terraform init](https://www.terraform.io/docs/commands/init.html) wird ein Verzeichnis initialisiert, das die Terraform-Konfigurationsdateien enthält – die Dateien, die Sie in den vorherigen Abschnitten erstellt haben. Es empfiehlt sich, nach dem Schreiben einer neuen Terraform-Konfiguration den Befehl `terraform init` auszuführen. 

> [!TIP]
> Der Befehl `terraform init` ist idempotent, d. h., er kann wiederholt aufgerufen werden und erzeugt immer das gleiche Ergebnis. Wenn Sie in einer Zusammenarbeitsumgebung arbeiten und vermuten, dass die Konfigurationsdateien möglicherweise geändert wurden, ist es deshalb immer ratsam, vor dem Ausführen oder Anwenden eines Plans den Befehl `terraform init` aufzurufen.

Führen Sie zum Initialisieren von Terraform den folgenden Befehl aus:

  ```bash
  terraform init
  ```

  ![Initialisieren von Terraform](media/create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="3-create-a-terraform-execution-plan"></a>3. Erstellen eines Terraform-Ausführungsplans

Der Befehl [terraform plan](https://www.terraform.io/docs/commands/plan.html) dient zum Erstellen eines Ausführungsplans. Zum Generieren eines Ausführungsplans werden alle `.tf`-Dateien im aktuellen Verzeichnis aggregiert. 

Der [Parameter „-out“](https://www.terraform.io/docs/commands/plan.html#out-path) dient dazu, den Ausführungsplan in einer Ausgabedatei zu speichern. Dieses Feature vermeidet Parallelitätsprobleme, wie sie häufig in Umgebungen mit mehreren Entwicklern auftreten. Zu den Problemen, die durch die Ausgabedatei gelöst werden, zählt unter anderem das folgende Szenario:

1. Entwickler 1 erstellt die Konfigurationsdatei.
1. Entwickler 2 ändert die Konfigurationsdatei.
1. Entwickler 1 führt die Konfigurationsdatei aus, um sie anzuwenden.
1. Entwickler 1 erhält unerwartete Ergebnisse und weiß nicht, dass Entwickler 2 die Konfiguration geändert hat.

Wenn Entwickler 1 eine Ausgabedatei angibt, werden Auswirkungen von Entwickler 2 auf Entwickler 1 verhindert. 

Falls Sie Ihren Ausführungsplan nicht speichern müssen, führen Sie den folgenden Befehl aus:

  ```bash
  terraform plan
  ```

Falls Sie Ihren Ausführungsplan speichern müssen, führen Sie den folgenden Befehl aus. Ersetzen Sie die Platzhalter durch entsprechende Werte für Ihre Umgebung.

  ```bash
  terraform plan -out=<path>
  ```

Ein weiterer hilfreicher Parameter ist [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Standardmäßig wurde von Terraform versucht, Ihre Variablendatei wie folgt zu finden:
- Datei namens `terraform.tfvars`
- Datei mit folgendem Benennungsmuster: `*.auto.tfvars`

Für Ihre Variablendatei muss jedoch keine der beiden obigen Konventionen verwendet werden. Geben Sie in diesem Fall den Namen Ihrer Variablendatei mithilfe des Parameters `-var-file` an. Der Name Ihrer Variablendatei darf keine Erweiterung enthalten. Dies wird im folgenden Beispiel veranschaulicht:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform bestimmt die Aktionen, die erforderlich sind, um den in der Konfigurationsdatei angegebenen Zustand zu erreichen.

![Erstellen eines Terraform-Ausführungsplans](media/create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="4-apply-the-terraform-execution-plan"></a>4. Anwenden des Terraform-Ausführungsplans

Im letzten Schritt dieses Artikels führen Sie den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus, um die durch den Befehl `terraform plan` generierten Aktionen anzuwenden.

Wenn Sie den letzten Ausführungsplan anwenden möchten, führen Sie den folgenden Befehl aus:

  ```bash
  terraform apply
  ```

Wenn Sie einen zuvor gespeicherten Ausführungsplan anwenden möchten, führen Sie den folgenden Befehl aus. Ersetzen Sie die Platzhalter durch entsprechende Werte für Ihre Umgebung:

  ```bash
  terraform apply <path>
  ```

![Anwenden eines Terraform-Ausführungsplans](media/create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Erstellen einer Azure-VM-Skalierungsgruppe unter Verwendung von Terraform](create-vm-scaleset-network-disks-hcl.md)