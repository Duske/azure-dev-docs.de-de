---
title: Bereitstellen der Infrastruktur mit Azure-Bereitstellungsslots per Terraform
description: Es wird beschrieben, wie Sie Terraform mit Bereitstellungsslots für Azure-Anbieter verwenden.
keywords: Azure DevOps Terraform Bereitstellungsslots
ms.topic: how-to
ms.date: 03/09/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: bed68e64bb6948f4ab021035bd113ea9fb007950
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88241212"
---
# <a name="provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Bereitstellen der Infrastruktur mit Azure-Bereitstellungsslots per Terraform

Mit [Azure-Bereitstellungsslots](/azure/app-service/deploy-staging-slots) können Sie zwischen verschiedenen Versionen Ihrer App wechseln. Dadurch lassen sich die Auswirkungen fehlerhafter Bereitstellungen minimieren. 

Dieser Artikel enthält ein Beispiel für die Verwendung von Bereitstellungsslots sowie die Schritte zur Bereitstellung von zwei Apps über GitHub und Azure. Eine App wird in einem Produktionsslot gehostet. Die zweite App wird in einem Stagingslot gehostet. (Die Namen „production“ und „staging“ sind willkürlich gewählt. Sie können beliebige Namen verwenden, die für Ihr Szenario geeignet sind.) Nach dem Konfigurieren Ihrer Bereitstellungsslots verwenden Sie Terraform, um je nach Bedarf zwischen den beiden Slots zu wechseln.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **GitHub-Konto:** Zum Forken und Verwenden des GitHub-Testrepositorys wird ein [GitHub](https://www.github.com)-Konto benötigt.

## <a name="create-and-apply-the-terraform-plan"></a>Erstellen und Anwenden des Terraform-Plans

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/provision-infrastructure-using-azure-deployment-slots/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `deploy`.

    ```bash
    mkdir deploy
    ```

1. Erstellen Sie ein Verzeichnis namens `swap`.

    ```bash
    mkdir swap
    ```

1. Vergewissern Sie sich mithilfe des Bash-Befehls `ls`, dass beide Verzeichnisse erfolgreich erstellt wurden.

    ![Cloud Shell nach dem Erstellen von Verzeichnissen](./media/provision-infrastructure-using-azure-deployment-slots/cloud-shell-after-creating-dirs.png)

1. Wechseln Sie in das Verzeichnis `deploy`.

    ```bash
    cd deploy
    ```

1. Erstellen Sie in Cloud Shell eine Datei namens `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Überprüfen Sie nun den Inhalt der erstellten Datei.

    ```bash
    cat deploy.tf
    ```

1. Initialisieren Sie Terraform.

    ```bash
    terraform init
    ```

1. Erstellen Sie den Terraform-Plan.

    ```bash
    terraform plan
    ```

1. Stellen Sie die in der Konfigurationsdatei `deploy.tf` definierten Ressourcen bereit. (Bestätigen Sie die Aktion, indem Sie an der Eingabeaufforderung `yes` eingeben.)

    ```bash
    terraform apply
    ```

1. Schließen Sie das Cloud Shell-Fenster.

1. Klicken Sie im Hauptmenü des Azure-Portals auf **Ressourcengruppen**.

    ![Klicken auf „Ressourcengruppen“ im Portal](./media/provision-infrastructure-using-azure-deployment-slots/resource-groups-menu-option.png)

1. Klicken Sie auf der Registerkarte **Ressourcengruppen** auf **slotDemoResourceGroup**.

    ![Von Terraform erstellte Ressourcengruppe](./media/provision-infrastructure-using-azure-deployment-slots/resource-group.png)

Daraufhin werden alle von Terraform erstellten Ressourcen angezeigt.

![Von Terraform erstellte Ressourcen](./media/provision-infrastructure-using-azure-deployment-slots/resources.png)

## <a name="fork-the-test-project"></a>Forken des Testprojekts

Bevor Sie die Erstellung testen und zwischen den Bereitstellungsslots wechseln können, müssen Sie das Testprojekt über GitHub forken.

1. Navigieren Sie zum [Repository „awesome-terraform“ auf GitHub](https://github.com/Azure/awesome-terraform).

1. Forken Sie das Repository **awesome-terraform**.

    ![Forken des Repositorys „awesome-terraform“ auf GitHub](./media/provision-infrastructure-using-azure-deployment-slots/fork-repo.png)

1. Befolgen Sie alle Anweisungen zum Forken Ihrer Umgebung.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Bereitstellen über GitHub in Ihren Bereitstellungsslots

Konfigurieren Sie nach dem Forken des Repositorys mit dem Testprojekt die Bereitstellungsslots mithilfe der folgenden Schritte:

1. Klicken Sie im Hauptmenü des Azure-Portals auf **Ressourcengruppen**.

1. Klicken Sie auf **slotDemoResourceGroup**.

1. Klicken Sie auf **slotAppService**.

1. Klicken Sie auf **Bereitstellungsoptionen**.

    ![Bereitstellungsoptionen für eine App Service-Ressource](./media/provision-infrastructure-using-azure-deployment-slots/deployment-options.png)

1. Klicken Sie auf der Registerkarte **Bereitstellungsoption** auf **Quelle auswählen** und dann auf **GitHub**.

    ![Auswählen der Bereitstellungsquelle](./media/provision-infrastructure-using-azure-deployment-slots/select-source.png)

1. Wenn Azure die Verbindung hergestellt hat und alle Optionen anzeigt, klicken Sie auf **Autorisierung**.

1. Klicken Sie auf der Registerkarte **Autorisierung** auf **Autorisieren**, und geben Sie die Anmeldeinformationen ein, die Azure für den Zugriff auf Ihr GitHub-Konto benötigt. 

1. Nach der Überprüfung Ihrer GitHub-Anmeldeinformationen durch Azure wird eine Meldung mit dem Hinweis angezeigt, dass der Autorisierungsprozess abgeschlossen ist. Klicken Sie auf **OK**, um die Registerkarte **Autorisierung** zu schließen.

1. Klicken Sie auf **Organisation auswählen**, und wählen Sie Ihre Organisation aus.

1. Klicken Sie auf **Projekt auswählen**.

1. Wählen Sie auf der Registerkarte **Projekt auswählen** das Projekt **awesome-terraform** aus.

    ![Auswählen des Projekts „awesome-terraform“](./media/provision-infrastructure-using-azure-deployment-slots/choose-project.png)

1. Klicken Sie auf **Verzweigung auswählen**.

1. Klicken Sie auf der Registerkarte **Verzweigung auswählen** auf **master**.

    ![Auswählen der Masterverzweigung](./media/provision-infrastructure-using-azure-deployment-slots/choose-branch-master.png)

1. Klicken Sie auf der Registerkarte **Bereitstellungsoption** auf **OK**.

Nun haben Sie den Produktionsslot bereitgestellt. Führen Sie die obigen Schritte zum Bereitstellen des Stagingslots mit den folgenden Änderungen aus:

- Wählen Sie in Schritt 3 die Ressource **slotAppServiceSlotOne** aus.

- Wählen Sie in Schritt 13 anstelle der Masterverzweigung die Arbeitsverzweigung aus.

    ![Auswählen der Arbeitsverzweigung](./media/provision-infrastructure-using-azure-deployment-slots/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testen der App-Bereitstellungen

In den vorherigen Abschnitten haben Sie zwei Slots (**slotAppService** und **slotAppServiceSlotOne**) für die Bereitstellung über verschiedene Verzweigungen in GitHub eingerichtet. Sehen wir uns eine Vorschau der Web-Apps an, um sicherzustellen, dass sie erfolgreich bereitgestellt wurden.

1. Klicken Sie im Hauptmenü des Azure-Portals auf **Ressourcengruppen**.

1. Klicken Sie auf **slotDemoResourceGroup**.

1. Wählen Sie entweder **slotAppService** oder **slotAppServiceSlotOne** aus.

1. Klicken Sie auf der Übersichtsseite auf **URL**.

    ![Auswählen von „URL“ auf der Registerkarte „Übersicht“ zum Rendern der App](./media/provision-infrastructure-using-azure-deployment-slots/resource-url.png)

1. Je nach ausgewählter App werden die folgenden Ergebnisse angezeigt:
    - Web-App **slotAppService**: Blaue Seite mit dem Seitentitel **Slot Demo App 1**. 
    - Web-App **slotAppServiceSlotOne**: Grüne Seite mit dem Seitentitel **Slot Demo App 2**.

    ![Anzeigen einer Vorschau der Apps, um zu testen, ob sie richtig bereitgestellt wurden](./media/provision-infrastructure-using-azure-deployment-slots/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Wechseln zwischen den zwei Bereitstellungsslots

Führen Sie die folgenden Schritte aus, wenn Sie das Wechseln zwischen den beiden Bereitstellungsslots testen möchten:
 
1. Wechseln Sie zur Browserregisterkarte mit **slotAppService** (die App mit der blauen Seite). 

1. Öffnen Sie eine separate Registerkarte mit dem Azure-Portal.

1. Öffnen Sie Cloud Shell.

1. Wechseln Sie in das Verzeichnis **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Erstellen Sie in Cloud Shell eine Datei namens `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Initialisieren Sie Terraform.

    ```bash
    terraform init
    ```

1. Erstellen Sie den Terraform-Plan.

    ```bash
    terraform plan
    ```

1. Stellen Sie die in der Konfigurationsdatei `swap.tf` definierten Ressourcen bereit. (Bestätigen Sie die Aktion, indem Sie an der Eingabeaufforderung `yes` eingeben.)

    ```bash
    terraform apply
    ```

1. Wechseln Sie zurück in den Browser, nachdem Terraform die Slots gewechselt hat. Aktualisieren Sie die Seite. 

Mit der Web-App aus dem Stagingslot **slotAppServiceSlotOne** wurde in den Produktionsslot gewechselt, und sie wird nun grün gerendert. 

![Ausgetauschte Bereitstellungsslots](./media/provision-infrastructure-using-azure-deployment-slots/slots-swapped.png)

Wenn Sie zur ursprünglichen Produktionsversion der App zurückkehren möchten, wenden Sie erneut den Terraform-Plan an, den Sie auf der Grundlage der Konfigurationsdatei `swap.tf` erstellt haben.

```bash
terraform apply
```

Nach dem Wechsel der App wird wieder die ursprüngliche Konfiguration angezeigt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)