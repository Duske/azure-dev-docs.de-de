---
title: Konfigurieren eines Azure-Nachweisanbieters mit Terraform
description: Hier erfahren Sie, wie Sie Terraform zum Erstellen eines Nachweisanbieters in Azure verwenden.
keywords: Azure DevOps Terraform-Nachweis
ms.topic: how-to
ms.date: 11/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: c79c472da4604458475bd230a844e2d308c2bda1
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561546"
---
# <a name="configure-an-azure-attestation-policy-using-terraform"></a>Konfigurieren einer Azure-Nachweisrichtlinie mit Terraform

Dieser Artikel enthält Terraform-Beispielcode zum Erstellen eines [Nachweisanbieters](/azure/attestation/overview) in Azure.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- *Richtliniensignaturzertifikat:* Eine Datei, die eine Gruppe vertrauenswürdiger Signaturschlüssel in Form einer PEM-Datei festlegt.

[!INCLUDE [terraform-configure-environment.md](includes/terraform-configure-environment.md)]

## <a name="configure-an-azure-attestation-provider"></a>Konfigurieren eines Azure-Nachweisanbieters

```hcl
resource "azurerm_resource_group" "corpAttestation" {
  name                        = "corp-attestation"
  location                    = "westus"
}

resource "azurerm_attestation_provider" "corpAttestation" {
  name                              = "attestationprovider007"
  resource_group_name               = azurerm_resource_group.corpAttestation.name
  location                          = azurerm_resource_group.corpAttestation.location

  policy_signing_certificate_data   = file("./certs/cert.pem")
}
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)