---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 02/18/2021
ms.author: tarcher
ms.openlocfilehash: 2cecafe9581835adf23ffffcc98c63fb6529cb7c
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473734"
---
## <a name="create-a-base-terraform-configuration-file"></a>Erstellen einer grundlegenden Terraform-Konfigurationsdatei

Eine Terraform-Konfigurationsdatei beginnt mit der Angabe des Anbieters. Bei Verwendung von Azure wird der [Azure-Anbieter (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) im Block `provider` angegeben.

```terraform
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~>2.0"
    }
  }
}
provider "azurerm" {
  features {}
}
resource "azurerm_resource_group" "rg" {
  name = "<resource_group_name>"
  location = "<location>"
}

# Your Terraform code goes here...

```

**Hinweise**:

- Das Attribut `version` ist zwar optional, HashiCorp empfiehlt jedoch, eine bestimmte Version des Anbieters festzulegen. 
- Bei Verwendung des Azure-Anbieters 1.x ist der Block `features` nicht zulässig.
- Bei Verwendung des Azure-Anbieters 2.x ist der Block `features` erforderlich.
- Die [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) von [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) hat zwei Argumente: `name` und `location`. Legen Sie die Platzhalter auf die entsprechenden Werte für Ihre Umgebung fest.
- Der [lokale benannte Wert](https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values) `rg` für die Ressourcengruppe wird in sämtlichen Anleitungen und Tutorials bei Verweisen auf die Ressourcengruppe verwendet. Dieser Wert ist unabhängig vom Ressourcengruppennamen und bezieht sich nur auf den Variablennamen in Ihrem Code. Wenn Sie diesen Wert in der Ressourcengruppendefinition ändern, müssen Sie ihn auch in dem Code ändern, von dem darauf verwiesen wird.
