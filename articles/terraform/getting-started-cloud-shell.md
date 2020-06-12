---
title: 'Schnellstart: Erste Schritte mit Terraform: Azure Cloud Shell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform für die Erstellung von Azure-Ressourcen installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Cloud Shell init planen anwenden Ausführung Portal anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: 184d2720e3e2259a6c909d0775ffee20c0f30419
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329908"
---
# <a name="quickstart-getting-started-with-terraform---azure-cloud-shell"></a>Schnellstart: Erste Schritte mit Terraform: Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel werden die ersten Schritte mit Terraform in der [Azure Cloud Shell](/azure/cloud-shell/overview)-Umgebung beschrieben.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="open-cloud-shell"></a>Öffnen von Cloud Shell

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Falls Sie noch nicht angemeldet sind, wird im Azure-Portal eine Liste verfügbarer Microsoft-Konten angezeigt. Wählen Sie ein Microsoft-Konto aus, das mit mindestens einem aktiven Azure-Abonnement verknüpft ist, und geben Sie Ihre Anmeldeinformationen ein, um fortzufahren.

1. Öffnen Sie Cloud Shell.

    ![Zugreifen auf Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Falls Sie Cloud Shell bislang noch nicht verwendet haben, konfigurieren Sie die Umgebungs- und Speichereinstellungen. In diesem Artikel wird die Bash-Umgebung verwendet.

## <a name="log-into-your-microsoft-account"></a>Anmelden bei Ihrem Microsoft-Konto

Cloud Shell wird automatisch unter dem Microsoft-Konto authentifiziert, mit dem Sie sich beim Azure-Portal angemeldet haben. Sollten Sie über mehrere Microsoft-Konten mit Azure-Abonnements verfügen, können Sie sich allerdings unter Verwendung von [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) bei einem dieser Konten anmelden. Hier sehen Sie zwei Beispiele für die Verwendung des Befehls `az login`:

Verwenden Sie abhängig von Ihrem Szenario eine der folgenden Vorgehensweisen:
    
- **Sie möchten sich als Benutzer anmelden:** Wenn Sie den Befehl `az login` ohne Parameter ausführen, werden eine URL und ein Code angezeigt. Navigieren Sie zu der URL, geben Sie den Code ein, und befolgen Sie die Anweisungen, um sich mit Ihrem Microsoft-Konto bei Azure anzumelden. Kehren Sie nach Abschluss der Anmeldung zum Portal zurück.

    ```azurecli-interactive
    az login
    ```

    **Hinweise:**
    - Nach erfolgreicher Anmeldung wird durch den Befehl `az login` eine Liste der Azure-Abonnements angezeigt, die mit dem angemeldeten Microsoft-Konto verknüpft sind.
    - Für die einzelnen verfügbaren Azure-Abonnements wird jeweils eine Liste mit Eigenschaften angezeigt. Die Eigenschaft `isDefault` gibt das verwendete Azure-Abonnement an. Im Abschnitt [Angeben des aktuellen Azure-Abonnements](#specify-the-current-azure-subscription) erfahren Sie, wie Sie zu einem anderen Azure-Abonnement wechseln.

- **Sie möchten einen (noch nicht vorhandenen) Dienstprinzipal verwenden:** Automatisierte Tools wie Terraform, die Azure-Dienste bereitstellen oder verwenden, sollten stets über eingeschränkte Berechtigungen verfügen. Azure bietet Dienstprinzipale, um zu vermeiden, dass sich Anwendungen als Benutzer mit uneingeschränkten Berechtigungen anmelden. Sollten Sie noch nicht über einen Dienstprinzipal für die Anmeldung verfügen, können Sie sich mit Ihren Benutzeranmeldeinformationen anmelden und anschließend einen Dienstprinzipal erstellen. Nach Erstellung des Dienstprinzipals können Sie dessen Informationen für zukünftige Anmeldungen verwenden.

    Beim [Erstellen eines Dienstprinzipals](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird ein Dienstprinzipal mit der Standardrolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Azure-Rollen](/azure/active-directory/role-based-access-built-in-roles). 
    
    Verwenden Sie den Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac), und ersetzen Sie `<subscription_id>` durch die ID des gewünschten Abonnementkontos:
    
    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
    ```

    **Hinweise:**
    - Nach erfolgreichem Abschluss werden durch den Befehl `az ad sp create-for-rbac` mehrere Werte angezeigt, unter anderem das automatisch generierte Kennwort. Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
    
- **Anmelden mit einem Azure-Dienstprinzipal:** Ersetzen Sie die Platzhalter im folgenden Befehl vom Typ `az login` durch Informationen Ihres Dienstprinzipals:

    ```azurecli-interactive
    az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
    ```

    **Hinweise:**
    - Nach erfolgreicher Anmeldung werden durch den Befehl `az login` verschiedene Eigenschaften für das Azure-Abonnement angezeigt, unter anderem `id` und `name`.

## <a name="specify-the-current-azure-subscription"></a>Angeben des aktuellen Azure-Abonnements

Wie bereits im vorherigen Abschnitt erläutert, stehen für die Anmeldung bei Azure unter anderem die beiden folgenden Szenarien zur Verfügung:

- **Anmelden mit einem Microsoft-Konto:** Ein Microsoft-Konto kann mehreren Azure-Abonnements zugeordnet werden, wobei eines davon als Standardabonnement fungiert. Das Standardabonnement wird verwendet, wenn Sie sich anmelden und nicht zu einem anderen Abonnement wechseln.
- **Anmelden mit einem Azure-Dienstprinzipal:** Ein Dienstprinzipal ist für ein Azure-Abonnement spezifisch. Wie bereits erwähnt, werden die Abonnementinformationen angezeigt, wenn Sie sich anmelden.

Die folgenden Schritte gelten für das erste Szenario und umfassen folgende Aufgaben:

- Überprüfen des aktuellen Azure-Abonnements
- Auflisten aller verfügbaren Azure-Abonnements für das aktuelle Microsoft-Konto
- Wechseln zu einem anderen Azure-Abonnement

1. Verwenden Sie zum Überprüfen des aktuellen Azure-Abonnements den Befehl [az account show](/cli/azure/account#az-account-show).

    ```azurecli-interactive
    az account show
    ```
    
1. Wenn Sie Zugriff auf mehrere verfügbare Azure-Abonnements haben, verwenden Sie [az account list](/cli/azure/account#az-account-list), um eine Liste mit Abonnementnamen/-IDs anzuzeigen:

    ```azurecli-interactive
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Wenn Sie für die aktuelle Cloud Shell-Sitzung ein bestimmtes Azure-Abonnement verwenden möchten, verwenden Sie den Befehl [az account set](/cli/azure/account#az-account-set). Ersetzen Sie den Platzhalter `<subscription_id>` durch die ID (oder den Namen) des gewünschten Abonnements:

    ```azurecli-interactive
    az account set --subscription="<subscription_id>"
    ```

    **Hinweise:**
    - Die Ergebnisse des Wechsels zum angegebenen Azure-Abonnement werden durch den Befehl `az account set` nicht angezeigt. Sie können sich jedoch mithilfe des Befehls `az account show` vergewissern, dass sich das aktuelle Azure-Abonnement geändert hat.

## <a name="create-a-terraform-configuration-file"></a>Erstellen einer Terraform-Konfigurationsdatei

In diesem Abschnitt wird der [Code Shell-Editor](/azure/cloud-shell/using-cloud-shell-editor) verwendet, um eine Terraform-Konfigurationsdatei zu definieren.

1. Wechseln Sie zum Verzeichnis der eingebundenen Dateifreigabe, in der Ihre Arbeit in Cloud Shell gespeichert wird. Weitere Informationen zur Dateispeicherung durch Cloud Shell finden Sie unter [Herstellen einer Verbindung mit dem Microsoft Azure Files-Speicher](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage).
    
    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis für die Terraform-Dateien aus dieser Demo.

    ```bash
    mkdir QuickstartTerraformTest
    ```

1. Wechseln Sie zu diesem Demoverzeichnis.

    ```bash
    cd QuickstartTerraformTest
    ```

1. Erstellen Sie mithilfe Ihres bevorzugten Editors eine Terraform-Konfigurationsdatei. In diesem Artikel wird der integrierte Cloud Shell-Editor verwendet.

    ```bash
    code QuickstartTerraformTest.tf
    ```
 
1. Fügen Sie den folgenden HCL-Code in die neue Datei ein:

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    **Hinweise:**
    - Der Block `provider` dient zum Angeben der Verwendung des [Azure-Anbieters (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html).
    - Innerhalb des Anbieterblocks `azurerm` werden die Attribute `version` und `features` festgelegt. Wie Sie dem Kommentar entnehmen können, ist deren Verwendung versionsspezifisch. Weitere Informationen zum Festlegen dieser Attribute für Ihre Umgebung finden Sie unter [Version 2.0 des AzureRM-Anbieters](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Die einzige [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) betrifft den Ressourcentyp [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Für `azure_resource_group` sind die Argumente `name` und `location` erforderlich.

1. Drücken Sie **STRG+S**, um die Datei zu speichern.

1. Drücken Sie **STRG+Q**, um den Editor zu beenden.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Erstellen und Anwenden eines Terraform-Ausführungsplans

Für Cloud Shell wird automatisch die neueste Version von Terraform installiert. Darüber hinaus werden von Terraform automatisch Informationen aus dem aktuellen Azure-Abonnement verwendet. Daher ist keine Installation oder Konfiguration erforderlich. Nachdem Sie Ihre Konfigurationsdateien erstellt haben, können Sie mit wenigen Terraform-Befehlen einen Ausführungsplan erstellen. Der erstellte Ausführungsplan kann dann überprüft und bereitgestellt werden.

1. Initialisieren Sie die Terraform-Bereitstellung mithilfe des Befehls [terraform init](https://www.terraform.io/docs/commands/init.html). Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```bash
    terraform init
    ```
    
1. Mithilfe des Terraform-Befehls [terraform plan](https://www.terraform.io/docs/commands/plan.html) können Sie eine Vorschau der auszuführenden Aktionen anzeigen.

    ```bash
    terraform plan
    ```

    **Hinweise:**
    - Durch den Befehl `terraform plan` wird ein Ausführungsplan erstellt, aber nicht ausgeführt. Stattdessen werden die Aktionen ermittelt, die erforderlich sind, um die in Ihren Konfigurationsdateien angegebene Konfiguration zu erstellen.
    - Mit dem Befehl `terraform plan` können Sie überprüfen, ob der Ausführungsplan Ihren Erwartungen entspricht, bevor Sie Änderungen an den eigentlichen Ressourcen vornehmen.
    - Der optionale Parameter `-out` ermöglicht die Angabe einer Ausgabedatei für den Plan. Weitere Informationen zur Verwendung des Parameters `-out` finden Sie im Abschnitt [Speichern eines Ausführungsplans zur späteren Bereitstellung](#persist-an-execution-plan-for-later-deployment).

1. Wenden Sie den Ausführungsplan mithilfe des Befehls [terraform apply](https://www.terraform.io/docs/commands/apply.html) an.

    ```bash
    terraform apply
    ```
    
1. Von Terraform wird gezeigt, was passiert, wenn Sie den Ausführungsplan anwenden, und Sie werden zur Bestätigung der Ausführung aufgefordert. Geben Sie zur Bestätigung des Befehls `yes` ein, und drücken Sie die **EINGABETASTE**.

1. Vergewissern Sie sich nach der Bestätigung der Planausführung mithilfe des Befehls [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show), dass die Ressourcengruppe erfolgreich wurde.

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    War der Vorgang erfolgreich, werden verschiedene Eigenschaften der neu erstellten Ressourcengruppe angezeigt.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Speichern eines Ausführungsplans zur späteren Bereitstellung

Im vorherigen Abschnitt wurde gezeigt, wie Sie einen [Terraform-Plan](https://www.terraform.io/docs/commands/plan.html) ausführen, um einen Ausführungsplan zu erstellen. Anschließend wurde gezeigt, dass der Plan mithilfe von [terraform apply](https://www.terraform.io/docs/commands/apply.html) angewendet wird. Dieses Muster eignet sich bestens, wenn die Schritte interaktiv und sequenziell sind.

In komplexeren Szenarien kann der Ausführungsplan in einer Datei gespeichert werden. Dieser Ausführungsplan kann dann später (oder sogar von einem anderen Computer aus) angewendet werden.

Falls Sie dieses Feature nutzen möchten, empfiehlt es sich, den Artikel [Ausführen von Terraform mit Automatisierung](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation) zu lesen.

Die folgenden Schritte veranschaulichen das grundlegende Muster für die Nutzung dieses Features:

1. Führen Sie [terraform init](https://www.terraform.io/docs/commands/init.html) aus.

    ```bash
    terraform init
    ```

1. Führen Sie `terraform plan` mit dem Parameter `-out` aus.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Führen Sie `terraform apply` aus, und geben Sie dabei den Namen der Datei aus dem vorherigen Schritt an.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

**Hinweise:**
- Um die Verwendung mit Automatisierung zu ermöglichen, kann `terraform apply <filename>` ohne Bestätigung ausgeführt werden.
- Wenn Sie dieses Feature verwenden möchten, lesen Sie den [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

1. Führen Sie [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) aus, um den aktuellen Ausführungsplan umzukehren.

    ```bash
    terraform destroy
    ```

1. Von Terraform wird gezeigt, was passiert, wenn Sie den Ausführungsplan umkehren, und Sie werden zur Bestätigung des Vorgangs aufgefordert. Geben Sie zur Bestätigung des Befehls `yes` ein, und drücken Sie die **EINGABETASTE**.

1. Die Ausgabe nach der Bestätigung der Planausführung sieht in etwa wie im folgenden Beispiel aus. Vergewissern Sie sich mithilfe des Befehls [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show), dass die Ressourcengruppe gelöscht wurde.

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Hinweise:**
    - War der Vorgang erfolgreich, wird vom Befehl `az group show` angezeigt, dass die Ressourcengruppe nicht vorhanden ist.

1. Wechseln Sie zum übergeordneten Verzeichnis, und entfernen Sie das Demoverzeichnis. Durch den Parameter `-r` wird vor dem Entfernen des Verzeichnisses der Inhalt des Verzeichnisses entfernt. Zu den Verzeichnisinhalten gehören die weiter oben erstellte Konfigurationsdatei sowie die Terraform-Zustandsdateien.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](create-linux-virtual-machine-with-infrastructure.md)