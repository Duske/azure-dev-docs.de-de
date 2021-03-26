---
title: 'Tutorial: Erstellen eines Spoke-Netzwerks in Azure mit Terraform'
description: Es wird beschrieben, wie Sie zwei virtuelle Spoke-Netzwerke (VNETs) implementieren, die in einer Hub-Spoke-Topologie mit einem Hub verbunden sind.
ms.topic: tutorial
ms.date: 03/08/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 15f5c14af653354b7c741bc5fcf1a1d4c443e83c
ms.sourcegitcommit: 7991f748720673d2dc50baaa8658348ff6cc1044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102604021"
---
# <a name="tutorial-create-a-spoke-network-in-azure-using-terraform"></a>Tutorial: Erstellen eines Spoke-Netzwerks in Azure mit Terraform

In diesem Tutorial implementieren Sie zwei getrennte Spoke-Netzwerke, um die Trennung von Workloads zu demonstrieren. Die Netzwerke teilen sich gemeinsame Ressourcen über ein virtuelles Hub-Netzwerk. Spokes können verwendet werden, um Workloads in ihren eigenen VNETs, die getrennt von anderen Spokes verwaltet werden, zu isolieren. Jede Workload kann mehrere Schichten umfassen, wobei mehrere Subnetze über Azure Load Balancer verbunden sind.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Verwenden von HCL (HashiCorp-Sprache), um die Spoke-VNETs in einer Hub-Spoke-Topologie zu implementieren
> * Verwenden von Terraform zum Erstellen virtueller Computer in den Spoke-Netzwerke
> * Verwenden von Terraform für das Peering eines virtuellen Netzwerks mit den Hub-Netzwerken

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen einer hybriden Hub-Spoke-Netzwerktopologie mit Terraform in Azure](./hub-spoke-introduction.md)
1. [Erstellen eines lokalen virtuellen Netzwerks mit Terraform in Azure](./hub-spoke-on-prem.md)
1. [Erstellen eines virtuellen Hub-Netzwerks mit Terraform in Azure](./hub-spoke-hub-network.md)
1. [Erstellen einer virtuellen Hub-Netzwerkappliance mit Terraform in Azure](./hub-spoke-hub-nva.md)

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

In diesem Abschnitt werden die zwei Spoke-Skripts erstellt. Jedes Skript definiert ein virtuelles Spoke-Netzwerk und einen virtuellen Computer für die Workload. Dann wird ein virtuelles Netzwerk mit Peering vom Hub zum Spoke erstellt.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/common/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-two-spoke-networks"></a>Deklarieren der zwei Spoke-Netzwerke

1. Öffnen Sie in Cloud Shell eine neue Datei namens `spoke1.tf`.

    ```bash
    code spoke1.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    locals {
      spoke1-location       = "eastus"
      spoke1-resource-group = "spoke1-vnet-rg"
      prefix-spoke1         = "spoke1"
    }

    resource "azurerm_resource_group" "spoke1-vnet-rg" {
      name     = local.spoke1-resource-group
      location = local.spoke1-location
    }

    resource "azurerm_virtual_network" "spoke1-vnet" {
      name                = "spoke1-vnet"
      location            = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke1-vnet-rg.name
      address_space       = ["10.1.0.0/16"]

      tags = {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_subnet" "spoke1-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefixes     = ["10.1.0.64/27"]
    }

    resource "azurerm_subnet" "spoke1-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke1-vnet.name
      address_prefixes     = ["10.1.1.0/24"]
    }

    resource "azurerm_virtual_network_peering" "spoke1-hub-peer" {
      name                      = "spoke1-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke1-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke1-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = [azurerm_virtual_network.spoke1-vnet, azurerm_virtual_network.hub-vnet , azurerm_virtual_network_gateway.hub-vnet-gateway]
    }

    resource "azurerm_network_interface" "spoke1-nic" {
      name                 = "${local.prefix-spoke1}-nic"
      location             = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke1-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke1
        subnet_id                     = azurerm_subnet.spoke1-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }
    }

    resource "azurerm_virtual_machine" "spoke1-vm" {
      name                  = "${local.prefix-spoke1}-vm"
      location              = azurerm_resource_group.spoke1-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke1-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke1-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke1}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-spoke1
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke1-peer" {
      name                      = "hub-spoke1-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke1-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = [azurerm_virtual_network.spoke1-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }
    ```

1. Speichern Sie die Datei, und beenden Sie den Editor.

1. Erstellen Sie eine neue Datei mit dem Namen `spoke2.tf`.

      ```bash
      code spoke2.tf
      ```
    
1. Fügen Sie den folgenden Code in den Editor ein:
    
    ```hcl
    locals {
      spoke2-location       = "eastus"
      spoke2-resource-group = "spoke2-vnet-rg"
      prefix-spoke2         = "spoke2"
    }

    resource "azurerm_resource_group" "spoke2-vnet-rg" {
      name     = local.spoke2-resource-group
      location = local.spoke2-location
    }

    resource "azurerm_virtual_network" "spoke2-vnet" {
      name                = "${local.prefix-spoke2}-vnet"
      location            = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name = azurerm_resource_group.spoke2-vnet-rg.name
      address_space       = ["10.2.0.0/16"]

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_subnet" "spoke2-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefixes     = ["10.2.0.64/27"]
    }

    resource "azurerm_subnet" "spoke2-workload" {
      name                 = "workload"
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.spoke2-vnet.name
      address_prefixes     = ["10.2.1.0/24"]
    }

    resource "azurerm_virtual_network_peering" "spoke2-hub-peer" {
      name                      = "${local.prefix-spoke2}-hub-peer"
      resource_group_name       = azurerm_resource_group.spoke2-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.spoke2-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.hub-vnet.id

      allow_virtual_network_access = true
      allow_forwarded_traffic = true
      allow_gateway_transit   = false
      use_remote_gateways     = true
      depends_on = [azurerm_virtual_network.spoke2-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }

    resource "azurerm_network_interface" "spoke2-nic" {
      name                 = "${local.prefix-spoke2}-nic"
      location             = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name  = azurerm_resource_group.spoke2-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-spoke2
        subnet_id                     = azurerm_subnet.spoke2-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_machine" "spoke2-vm" {
      name                  = "${local.prefix-spoke2}-vm"
      location              = azurerm_resource_group.spoke2-vnet-rg.location
      resource_group_name   = azurerm_resource_group.spoke2-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.spoke2-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-spoke2}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags = {
        environment = local.prefix-spoke2
      }
    }

    resource "azurerm_virtual_network_peering" "hub-spoke2-peer" {
      name                      = "hub-spoke2-peer"
      resource_group_name       = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name      = azurerm_virtual_network.hub-vnet.name
      remote_virtual_network_id = azurerm_virtual_network.spoke2-vnet.id
      allow_virtual_network_access = true
      allow_forwarded_traffic   = true
      allow_gateway_transit     = true
      use_remote_gateways       = false
      depends_on = [azurerm_virtual_network.spoke2-vnet, azurerm_virtual_network.hub-vnet, azurerm_virtual_network_gateway.hub-vnet-gateway]
    }
    ```
     
1. Speichern Sie die Datei, und beenden Sie den Editor.
  
[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Überprüfen eines Hub-Spoke-Netzwerks mit Terraform in Azure](./hub-spoke-validation.md)