---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 9fcde450a19515c38eb6653d75e644ca23d08964
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401549"
---
## <a name="create-a-base-terraform-configuration-file"></a>Erstellen einer grundlegenden Terraform-Konfigurationsdatei

Eine Terraform-Konfigurationsdatei beginnt mit der Angabe des Anbieters. Bei Verwendung von Azure wird der [Azure-Anbieter (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) im Block `provider` angegeben.

```terraform
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name = "<your_resource_group_name>"
  location = "<your_resource_group_location>"
}

# Your Terraform code goes here...

```

**Hinweise**:

- Das Attribut `version` ist zwar optional, HashiCorp empfiehlt jedoch, eine bestimmte Version des Anbieters festzulegen. 
- Bei Verwendung des Azure-Anbieters 1.x ist der Block `features` nicht zulässig.
- Bei Verwendung des Azure-Anbieters 2.x ist der Block `features` erforderlich.
- Die [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) von [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html) hat zwei Argumente: `name` und `location`. Legen Sie die Platzhalter auf die entsprechenden Werte für Ihre Umgebung fest.
- Der [lokale benannte Wert](https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values) `rg` für die Ressourcengruppe wird in sämtlichen Anleitungen und Tutorials bei Verweisen auf die Ressourcengruppe verwendet. Dies ist unabhängig vom Ressourcengruppennamen und bezieht sich nur auf den Variablennamen in Ihrem Code. Wenn Sie diesen Wert in der Ressourcengruppendefinition ändern, müssen Sie ihn auch in dem Code ändern, von dem darauf verwiesen wird.
