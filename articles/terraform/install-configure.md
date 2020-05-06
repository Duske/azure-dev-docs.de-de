---
title: 'Schnellstart: Installieren und Konfigurieren von Terraform für die Bereitstellung von Azure-Ressourcen'
description: Es wird beschrieben, wie Sie Terraform für die Erstellung von Azure-Ressourcen installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren
ms.topic: quickstart
ms.date: 04/26/2020
ms.openlocfilehash: e395227171417ccf73ef073a6067732fb11a418d
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82169736"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Schnellstart: Installieren und Konfigurieren von Terraform, um Azure-Ressourcen bereitzustellen
 
Terraform bietet eine einfache Möglichkeit, Cloudinfrastruktur mit der [einfachen Vorlagensprache](https://www.terraform.io/docs/configuration/syntax.html) zu definieren, eine Vorschau anzuzeigen und bereitzustellen. In diesem Artikel werden die notwendigen Schritte zum Bereitstellen von Ressourcen in Azure mithilfe von Terraform beschrieben.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="install-terraform"></a>Installieren von Terraform

Standardmäßig ist die aktuelle Version von Terraform zur Verwendung in der [Azure Cloud Shell](/azure/cloud-shell/overview) installiert. Führen Sie diesen Schritt aus, wenn Sie Terraform lokal installieren. Fahren Sie andernfalls mit dem [Konfigurieren des Terraform-Zugriffs auf Azure](#configure-terraform-access-to-azure) fort.

1. [Installieren Sie Terraform](https://www.terraform.io/downloads.html), indem Sie das entsprechende Paket für Ihr Betriebssystem angeben.
1. Der Download enthält nur eine ausführbare Datei. Definieren Sie je nach Betriebssystem einen globalen Pfad zur ausführbaren Datei:
    - [Linux oder macOS](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)
    - [Windows](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).
1. Überprüfen Sie die Konfiguration des globalen Pfads mit dem Befehl `terraform`. Wenn Terraform gefunden und ausgeführt wird, wird eine Liste mit den verfügbaren Terraform-Optionen angezeigt:

    ```console
    azureuser@Azure:~$ terraform
    Usage: terraform [--version] [--help] <command> [args]
    ```
    
## <a name="configure-terraform-access-to-azure"></a>Konfigurieren des Terraform-Zugriffs auf Azure

Erstellen Sie einen [Azure AD-Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli), damit Terraform Ressourcen in Azure bereitstellen kann. Der Dienstprinzipal gewährt Ihren Terraform-Skripts Die Berechtigung, in Ihrem Azure-Abonnement Ressourcen bereitstellen zu können.

Wenn Sie über mehrere Azure-Abonnements verfügen, fragen Sie zuerst Ihr Konto mit [az account list](/cli/azure/account#az-account-list) ab, um eine Liste der Abonnement-ID- und Mandanten-ID-Werte zu erhalten:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Um ein ausgewähltes Abonnement zu verwenden, legen Sie das Abonnement für diese Sitzung mit [az account set](/cli/azure/account#az-account-set) fest. Legen Sie die `SUBSCRIPTION_ID`-Umgebungsvariable so fest, dass sie den Wert des zurückgegebenen `id`-Felds für das gewünschte Abonnement enthält:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Jetzt können Sie einen Dienstprinzipal für die Verwendung mit Terraform erstellen. Verwenden Sie [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac), und legen Sie den *Bereich* wie folgt auf Ihr Abonnement fest:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Die Werte für `appId`, `password`, `sp_name` und `tenant` werden zurückgegeben. Notieren Sie sich `appId` und `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurieren der Terraform-Umgebungsvariablen

Um Terraform für die Verwendung Ihres Azure AD-Dienstprinzipals zu konfigurieren, legen Sie die folgenden Umgebungsvariablen fest, die dann von den [Azure-Terraform-Modulen](https://registry.terraform.io/modules/Azure) verwendet werden. Sie können die Umgebung auch festlegen, wenn Sie mit einer anderen als der öffentlichen Azure-Cloud arbeiten.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Sie können das folgende Beispiel-Shell-Skript dazu verwenden, diese Variablen festzulegen:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Ausführen eines Beispielskripts

Erstellen Sie die Datei `test.tf` in einem leeren Verzeichnis, und fügen Sie das folgende Skript ein.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Speichern Sie die Datei, und initialisieren Sie die Terraform-Bereitstellung. Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

```bash
terraform init
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Sie können mit `terraform plan` eine Vorschau der Aktionen anzeigen, die vom Terraform-Skript ausgeführt werden sollen. Wenn Sie die Ressourcengruppe erstellen, wenden Sie Ihren Terraform-Plan folgendermaßen an:

```bash
terraform apply
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Terraform installiert oder die Cloud Shell verwendet, um Azure-Anmeldeinformationen zu konfigurieren und mit dem Erstellen von Ressourcen in Ihrem Azure-Abonnement zu beginnen. Informationen zum Erstellen einer umfassenderen Terraform-Bereitstellung in Azure finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](create-linux-virtual-machine-with-infrastructure.md)