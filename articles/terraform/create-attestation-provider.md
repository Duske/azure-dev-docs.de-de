---
title: Konfigurieren eines Azure-Nachweisanbieters mit Terraform
description: Hier erfahren Sie, wie Sie Terraform zum Erstellen eines Nachweisanbieters in Azure verwenden.
keywords: Azure DevOps Terraform-Nachweis
ms.topic: how-to
ms.date: 11/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: c42f2a00886bedf3e9f26566cb619c69bb5073b7
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609397"
---
# <a name="configure-an-azure-attestation-policy-using-terraform"></a>Konfigurieren einer Azure-Nachweisrichtlinie mit Terraform

Dieser Artikel enthält Terraform-Beispielcode zum Erstellen eines [Nachweisanbieters](https://docs.microsoft.com/azure/attestation/overview) in Azure.

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
