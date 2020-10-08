---
title: Konfigurieren eines Azure-VM-Clusters mithilfe von Terraform
description: Es wird beschrieben, wie Sie in Azure mit Terraform-Modulen einen Cluster mit virtuellen Windows-Computern erstellen.
keywords: Azure DevOps Terraform VM virtueller Computer Cluster Modul Registrierung
ms.topic: how-to
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 73f375090a2178b38b0fc7e0afd4eb8c6b514672
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401584"
---
# <a name="configure-an-azure-vm-cluster-using-terraform"></a>Konfigurieren eines Azure-VM-Clusters mithilfe von Terraform

Dieser Artikel enthält Terraform-Beispielcode zum Erstellen eines VM-Clusters in Azure.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-vm-cluster"></a>Konfigurieren eines Azure-VM-Clusters

```hcl
module "windowsservers" {
  source              = "Azure/compute/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  is_windows_image    = true
  vm_hostname         = "mywinvm"                         // Line can be removed if only one VM module per resource group
  admin_password      = "ComplxP@ssw0rd!"                 // See note following code about storing passwords in config files
  vm_os_simple        = "WindowsServer"
  public_ip_dns       = ["winsimplevmips"]                // Change to a unique name per data center region
  vnet_subnet_id      = module.network.vnet_subnets[0]
    
  depends_on = [azurerm_resource_group.rg]
}

module "network" {
  source              = "Azure/network/azurerm"
  resource_group_name = azurerm_resource_group.rg.name
  subnet_prefixes     = ["10.0.1.0/24"]
  subnet_names        = ["subnet1"]

  depends_on = [azurerm_resource_group.rg]
}

output "windows_vm_public_name" {
  value = module.windowsservers.public_ip_dns_name
}

output "vm_public_ip" {
  value = module.windowsservers.public_ip_address
}

output "vm_private_ips" {
  value = module.windowsservers.network_interface_private_ip
}
```

**Hinweise**:

- Im obigen Codebeispiel wird der Variablen `admin_password` der Einfachheit halber ein Literalwert zugewiesen. Es gibt viele Möglichkeiten, sensible Daten wie Kennwörter zu speichern. Die Entscheidung, wie Sie Ihre Daten schützen möchten, hängt von individuellen Aspekten der jeweiligen Umgebung sowie davon ab, wie wichtig es für Sie ist, dass diese Daten nicht offengelegt werden. Wenn Sie beispielsweise eine Datei wie diese in der Quellcodeverwaltung speichern, besteht die Gefahr, dass Dritte das Kennwort sehen. HashiCorp hat verschiedene Methoden zum [Deklarieren von Eingabevariablen](https://www.terraform.io/docs/configuration/variables.html) sowie Techniken zum [Verwalten sensibler Daten (beispielsweise Kennwörter)](https://www.terraform.io/docs/state/sensitive-data.html) dokumentiert.

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)
