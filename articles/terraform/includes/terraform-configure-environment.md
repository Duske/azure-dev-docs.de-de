---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: daa57dfc18cae3250c42265ebe8cbf7722e4235b
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401585"
---
## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

Installieren und konfigurieren Sie Terraform basierend auf Ihrer Umgebung:

- [Schnellstart: Konfigurieren von Terraform mithilfe von Azure Cloud Shell](../get-started-cloud-shell.md)
- [Konfigurieren von Terraform mithilfe von Azure PowerShell](../get-started-powershell.md)

In den Konfigurationsartikeln werden außerdem folgende Aufgaben erläutert:

- Erstellen einer grundlegenden Terraform-Konfigurationsdatei. Die Datei enthält den [Azure-Anbieter (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) im Block `provider` und definiert eine [Azure-Ressourcengruppe](/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group).
- Erstellen und Anwenden eines Terraform-Ausführungsplans zum Ausführen Ihres Codes
- Umkehren eines Ausführungsplans, wenn Sie die Ressourcen nicht mehr benötigen und sie löschen möchten
