---
title: Bereitstellen einer Azure Cosmos DB-Instanz für Azure Container Instances
description: Hier erfahren Sie, wie Sie mithilfe von Terraform eine Azure Cosmos DB-Instanz in Azure Container Instances bereitstellen.
ms.topic: how-to
ms.date: 04/26/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 75ee5e19d3cd42d10bd68e2550c391589c5c2d39
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401520"
---
# <a name="deploy-an-azure-cosmos-db-to-azure-container-instances"></a>Bereitstellen einer Azure Cosmos DB-Instanz für Azure Container Instances

In diesem Artikel erstellen Sie Azure-Ressourcen mit Terraform. Sie erfahren Schritt für Schritt, wie Sie folgende Ressourcen erstellen:

> [!div class="checklist"]
> * Azure Cosmos DB-Instanz
> * Azure Container Instances
> * App, die übergreifend für diese beiden Ressourcen funktioniert

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="create-first-configuration"></a>Erstellen der ersten Konfiguration

In diesem Abschnitt wird die Konfiguration für eine Azure Cosmos DB-Instanz erstellt.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Öffnen Sie Azure Cloud Shell.

1. Starten Sie den Cloud Shell-Editor:

    ```bash
    code main.tf
    ```

1. Die Konfiguration in diesem Schritt modelliert einige Azure-Ressourcen. Zu diesen Ressourcen zählen eine Azure-Ressourcengruppe und eine Azure Cosmos DB-Instanz. Zur Erstellung eines eindeutigen Cosmos DB-Instanznamens wird eine zufällige ganze Zahl verwendet. Außerdem werden mehrere Cosmos DB-Einstellungen konfiguriert. Weitere Informationen finden Sie in der [Terraform-Referenz für Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Kopieren Sie die folgende Terraform-Konfiguration, und fügen Sie sie in den Editor ein:

    ```hcl
    resource "azurerm_resource_group" "vote-resource-group" {
      name     = "vote-resource-group"
      location = "westus"
    }

    resource "random_integer" "ri" {
      min = 10000
      max = 99999
    }

    resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
      name                = "tfex-cosmos-db-${random_integer.ri.result}"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      offer_type          = "Standard"
      kind                = "GlobalDocumentDB"

      consistency_policy {
        consistency_level       = "BoundedStaleness"
        max_interval_in_seconds = 10
        max_staleness_prefix    = 200
      }

      geo_location {
        location          = "westus"
        failover_priority = 0
      }
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

## <a name="run-the-configuration"></a>Ausführen der Konfiguration

In diesem Abschnitt werden mehrere Terraform-Befehle verwendet, um die Konfiguration auszuführen.

1. Der Befehl [terraform init](https://www.terraform.io/docs/commands/init.html) initialisiert das Arbeitsverzeichnis. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform init
    ```

1. Mit dem Befehl [terraform plan](https://www.terraform.io/docs/commands/plan.html) kann die Konfigurationssyntax überprüft werden. Durch den Parameter `-out` werden die Ergebnisse an eine Datei weitergeleitet. Die Ausgabedatei kann später zum Anwenden der Konfiguration verwendet werden. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform plan --out plan.out
    ```

1. Der Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) dient zum Anwenden der Konfiguration. Die Ausgabedatei aus dem vorherigen Schritt wird angegeben. Dieser Befehl bewirkt die Erstellung der Azure-Ressourcen. Führen Sie den folgenden Befehl in Cloud Shell aus:

    ```bash
    terraform apply plan.out
    ```

1. Navigieren Sie zu der neuen Ressourcengruppe, um die Ergebnisse im Azure-Portal zu überprüfen. Die neue Azure Cosmos DB-Instanz befindet sich in der neuen Ressourcengruppe.

## <a name="update-configuration"></a>Aktualisieren der Konfiguration

In diesem Abschnitt erfahren Sie, wie Sie die Konfiguration aktualisieren, um eine Azure-Containerinstanz einzuschließen. In dem Container wird eine Anwendung ausgeführt, die Daten liest und in Cosmos DB schreibt.

1. Starten Sie den Cloud Shell-Editor:

    ```bash
    code main.tf
    ```

1. Mit der Konfiguration in diesem Schritt werden zwei Umgebungsvariablen festgelegt: `COSMOS_DB_ENDPOINT` und `COSMOS_DB_MASTERKEY`. Diese Variablen enthalten den Standort und den Schlüssel für den Datenbankzugriff. Die Werte für diese Variablen werden aus der im vorherigen Schritt erstellten Datenbankinstanz abgerufen. Dieser Prozess wird als Interpolation bezeichnet. Weitere Informationen zur Terraform-Interpolation finden Sie unter [Interpolation Syntax](https://www.terraform.io/docs/configuration/interpolation.html) (Syntax für die Interpolation). Die Konfiguration enthält auch einen Ausgabeblock, der den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der Containerinstanz zurückgibt. Kopieren Sie den folgenden Code, und fügen Sie ihn in den Editor ein:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
      ip_address_type     = "public"
      dns_name_label      = "vote-aci"
      os_type             = "linux"

      container {
        name   = "vote-aci"
        image  = "microsoft/azure-vote-front:cosmosdb"
        cpu    = "0.5"
        memory = "1.5"
        ports {
          port     = 80
          protocol = "TCP"
        }

        secure_environment_variables = {
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Führen Sie wie im vorherigen Abschnitt den folgenden Befehl aus, um die vorgenommenen Änderungen zu visualisieren:

    ```bash
    terraform plan --out plan.out
    ```

1. Führen Sie den Befehl `terraform apply` aus, um die Konfiguration anzuwenden.

    ```bash
    terraform apply plan.out
    ```

1. Notieren Sie sich den FQDN der Containerinstanz.

## <a name="test-application"></a>Testen der Anwendung

Navigieren Sie zum Testen der Anwendung zum FQDN der Containerinstanz. Die Ergebnisse sollten in etwa wie in der folgenden Ausgabe aussehen:

![Azure-Abstimmungsanwendung](media/deploy-azure-cosmos-db-to-azure-container-instances/azure-vote.jpg)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

Führen Sie den Befehl [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) aus, um die in diesem Artikel erstellten Azure-Ressourcen zu entfernen:

```bash
terraform destroy -auto-approve
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Installieren und Konfigurieren von Terraform, um Azure-Ressourcen bereitzustellen](get-started-cloud-shell.md)