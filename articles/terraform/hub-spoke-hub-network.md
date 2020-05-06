---
title: 'Tutorial: Erstellen eines virtuellen Hubnetzwerks in Azure unter Verwendung von Terraform'
description: Es wird beschrieben, wie Sie ein virtuelles Hubnetzwerk in Azure erstellen, das als allgemeiner Verbindungspunkt zwischen anderen Netzwerken fungiert.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 00e8e16588c86f85c4d5a0613de6e94efd2ec6fc
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82171006"
---
# <a name="tutorial-create-a-hub-virtual-network-in-azure-by-using-terraform"></a>Tutorial: Erstellen eines virtuellen Hubnetzwerks in Azure unter Verwendung von Terraform

Das virtuelle Hubnetzwerk fungiert als zentraler Konnektivitätspunkt für Ihr lokales Netzwerk. In dem virtuellen Netzwerk werden gemeinsame Dienste gehostet, die von in den virtuellen Spoke-Netzwerken gehosteten Workloads genutzt werden. Zu Demonstrationszwecken werden in diesem Tutorial keine gemeinsamen Dienste implementiert.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Implementieren des virtuellen Hubnetzwerks in einer Hub-and-Spoke-Topologie unter Verwendung der HashiCorp Configuration Language (HCL)
> * Erstellen eines virtuellen Hub-Jumpbox-Computers unter Verwendung von Terraform
> * Erstellen eines virtuellen privaten Hubnetzwerkgateways unter Verwendung von Terraform
> * Herstellen von Verbindungen mit hubbasierten und lokalen Gateways unter Verwendung von Terraform

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen Sie eine hybride Hub-and-Spoke-Netzwerktopologie in Azure mit Terraform.](./hub-spoke-introduction.md)
1. [Erstellen Sie ein lokales virtuelles Netzwerk in Azure mit Terraform.](./hub-spoke-on-prem.md)

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

Das Hubnetzwerk umfasst die folgenden Komponenten:

- Ein virtuelles Hubnetzwerk
- Ein Hubgateway für virtuelle Netzwerke
- Hub-Gatewayverbindungen 

In der folgenden Terraform-Konfigurationsdatei sind die Ressourcen definiert:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/common/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Wechseln Sie zu dem neuen Verzeichnis.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-virtual-network"></a>Deklarieren des virtuellen Hubnetzwerks

Erstellen Sie die Terraform-Konfigurationsdatei, die das virtuelle Hubnetzwerk deklariert.

1. Erstellen Sie in Cloud Shell eine Datei namens `hub-vnet.tf`.

    ```bash
    code hub-vnet.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = local.hub-resource-group
      location = local.hub-location
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub
        subnet_id                     = azurerm_subnet.hub-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-hub
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name   = azurerm_resource_group.hub-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nic.id]
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
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.hub-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.hub-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = azurerm_virtual_network_gateway.hub-vnet-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.onprem-vpn-gateway.id

      shared_key = local.shared-key
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = azurerm_virtual_network_gateway.onprem-vpn-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.hub-vnet-gateway.id

      shared_key = local.shared-key
    }
    ```
    
3. Speichern Sie die Datei, und beenden Sie den Editor.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Tutorial: Erstellen einer virtuellen Hub-Netzwerkappliance in Azure mit Terraform](./hub-spoke-hub-nva.md)