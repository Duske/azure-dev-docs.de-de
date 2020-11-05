---
title: 'Tutorial: Speichern des Terraform-Zustands in Azure Storage'
description: Es wird beschrieben, wie Sie den Terraform-Zustand in Azure Storage speichern.
ms.topic: tutorial
ms.date: 11/07/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: d3d2ab0ff605883926260928d3e7174a5c526781
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278415"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Tutorial: Speichern des Terraform-Status in Azure Storage

Der Terraform-Status wird verwendet, um bereitgestellte Ressourcen auf Terraform-Konfigurationen abzustimmen. Anhand des Zustands kann Terraform bestimmen, welche Azure-Ressourcen hinzugefügt, aktualisiert oder gelöscht werden sollen. Standardmäßig wird der Terraform-Zustand lokal gespeichert, wenn Sie den Befehl `terraform apply` ausführen. Diese Konfiguration ist aus folgenden Gründen nicht ideal:

- Der lokale Zustand eignet sich nicht besonders für eine team- oder zusammenarbeitsorientierte Umgebung.
- Der Terraform-Zustand kann sensible Informationen enthalten.
- Das lokale Speichern des Zustands erhöht das Risiko einer versehentlichen Löschung.

Terraform unterstützt die Speicherung des Zustands in einem Remotespeicher. Ein solches unterstütztes Back-End ist Azure Storage. In diesem Dokument erfahren Sie, wie Sie Azure Storage zu diesem Zweck konfigurieren und verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-storage-account"></a>Konfigurieren des Speicherkontos

Um Azure Storage als Back-End verwenden zu können, müssen Sie zunächst ein Speicherkonto erstellen. Das Speicherkonto kann mit dem Azure-Portal, PowerShell, der Azure CLI oder Terraform selbst erstellt werden. Verwenden Sie das folgende Beispiel, um das Speicherkonto mit der Azure CLI zu konfigurieren.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Notieren Sie sich den Speicherkontonamen, den Containernamen und den Speicherzugriffsschlüssel. Diese Werte werden beim Konfigurieren des Remotezustands benötigt.

## <a name="configure-state-back-end"></a>Konfigurieren des Zustands-Back-Ends

Das Terraform-Zustands-Back-End wird konfiguriert, wenn Sie den Befehl `terraform init` ausführen. Zum Konfigurieren des Zustands-Back-Ends benötigen Sie folgende Daten:

- **storage_account_name** : Der Name des Azure Storage-Kontos.
- **container_name** : Der Name des Blobcontainers.
- **key** : Der Name der Zustandsspeicherdatei, die erstellt werden soll.
- **access_key** : Der Speicherzugriffsschlüssel.

Jeder dieser Werte kann in der Terraform-Konfigurationsdatei oder über die Befehlszeile angegeben werden. Wir empfehlen, für den Wert `access_key` eine Umgebungsvariable zu verwenden. Durch die Verwendung einer Umgebungsvariablen wird verhindert, dass der Schlüssel auf den Datenträger geschrieben wird.

Erstellen Sie eine Umgebungsvariable namens `ARM_ACCESS_KEY` mit dem Wert des Azure Storage-Zugriffsschlüssels.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Um den Zugriffsschlüssel des Azure Storage-Kontos noch stärker zu schützen, speichern Sie ihn in Azure Key Vault. Die Umgebungsvariable kann dann mit einem Befehl wie dem Folgenden festgelegt werden. Weitere Informationen zu Azure Key Vault finden Sie in der [Dokumentation zu Azure Key Vault](/azure/key-vault/secrets/quick-create-cli).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Um Terraform für die Verwendung des Back-Ends zu konfigurieren, müssen folgende Schritte ausgeführt werden:
- Fügen Sie einen Konfigurationsblock namens `backend` mit dem Typ `azurerm` hinzu.
- Fügen Sie dem Konfigurationsblock einen Wert vom Typ `storage_account_name` hinzu.
- Fügen Sie dem Konfigurationsblock einen Wert vom Typ `container_name` hinzu.
- Fügen Sie dem Konfigurationsblock einen Wert vom Typ `key` hinzu.

Im folgenden Beispiel wird ein Terraform-Back-End konfiguriert und eine Azure-Ressourcengruppe erstellt.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Gehen Sie zum Initialisieren der Konfiguration wie folgt vor:

1. Führen Sie den Befehl `terraform init` aus.
1. Führen Sie den Befehl `terraform apply` aus.

Nun befindet sich die Zustandsdatei im Azure Storage-Blob.

## <a name="state-locking"></a>Statussperre

Azure Storage-Blobs werden vor Zustandsschreibvorgängen automatisch gesperrt. Diese Konfiguration verhindert die gleichzeitige Ausführung mehrerer Zustandsvorgänge, da dies zu Beschädigungen führen kann. 

Weitere Informationen finden Sie in der Terraform-Dokumentation unter [Zustandssperre](https://www.terraform.io/docs/state/locking.html).

Die Sperre wird angezeigt, wenn Sie das Blob über das Azure-Portal oder in anderen Azure-Verwaltungstools untersuchen.

![Azure-Blob mit Sperre](media/store-state-in-azure-storage/lock.png)

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

In einem Azure-Blob gespeicherte Daten werden vor dem Speichern verschlüsselt. Bei Bedarf ruft Terraform den Zustand vom Back-End ab und speichert ihn im lokalen Arbeitsspeicher. Bei Verwendung dieses Musters wird der Zustand nie auf Ihren lokalen Datenträger geschrieben.

Weitere Informationen zur Azure Storage-Verschlüsselung finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](/azure/storage/common/storage-service-encryption).

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)
