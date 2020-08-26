---
title: 'Schnellstart: Konfigurieren von Terraform mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform für die Erstellung von Azure-Ressourcen installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Windows init planen anwenden Ausführung anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript PowerShell
ms.topic: quickstart
ms.date: 08/18/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: e58c53876ed05416f16a40d0ee23344bcde43b39
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614519"
---
# <a name="quickstart-configure-terraform-using-azure-powershell"></a>Schnellstart: Konfigurieren von Terraform mithilfe von Azure PowerShell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel werden die ersten Schritte mit [Terraform in Azure](https://www.terraform.io/docs/providers/azurerm/index.html) unter Verwendung von PowerShell beschrieben.

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Installieren der neuesten PowerShell-Version
> * Installieren des neuen PowerShell Az-Moduls
> * Installieren der Azure CLI
> * Installieren von Terraform
> * Erstellen eines Azure-Dienstprinzipals für Authentifizierungszwecke
> * Anmelden bei Azure mit dem Dienstprinzipal 
> * Festlegen von Umgebungsvariablen, damit Terraform ordnungsgemäß bei Ihrem Azure-Abonnement authentifiziert wird
> * Schreiben eines Terraform-Skripts zum Erstellen einer Azure-Ressourcengruppe
> * Erstellen und Anwenden eines Terraform-Ausführungsplans
> * Verwenden des `terraform plan -destroy`-Flags zum Umkehren eines Ausführungsplans

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. Das aktuelle PowerShell-Modul, das die Interaktion mit Azure-Ressourcen ermöglicht, heißt [Azure PowerShell Az-Modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Wenn Sie das Azure PowerShell Az-Modul verwenden, wird auf allen Plattformen mindestens Version 7 von PowerShell empfohlen. Wenn Sie PowerShell installiert haben, können Sie die Version überprüfen, indem Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung eingeben:

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [Installieren Sie PowerShell.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7) Diese Demo wurde mithilfe von PowerShell 7.0.2 unter Windows 10 getestet.

1. Für die [Authentifizierung von Terraform bei Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html) müssen Sie die [Azure CLI installieren](/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Diese Demo wurde mit der Azure CLI-Version 2.9.1 getestet.

1. [Laden Sie Terraform herunter](https://www.terraform.io/downloads.html).

1. Extrahieren Sie die ausführbare Datei aus dem Download in ein Verzeichnis Ihrer Wahl.

1. [Aktualisieren Sie den globalen Pfad Ihres Systems](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) auf die ausführbare Datei.

1. Überprüfen Sie die Konfiguration des globalen Pfads mit dem Befehl `terraform`.

    ```powershell
    terraform
    ```

    **Hinweise**:
    - Wenn die ausführbare Terraform-Datei gefunden wird, werden die Syntax und verfügbare Befehle aufgelistet.

## <a name="create-an-azure-service-principal"></a>Erstellen eines Azure-Dienstprinzipals

Bei Verwendung von PowerShell und Terraform müssen Sie sich mithilfe eines Dienstprinzipals anmelden.

Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, benötigen Sie zunächst Zugriff auf einen Dienstprinzipal. Wenn Sie bereits einen Dienstprinzipal haben, können Sie diesen Abschnitt überspringen.

Beim [Erstellen eines Dienstprinzipals mit PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird ein Dienstprinzipal mit der Rolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** (die Standardrolle) verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Durch Aufrufen von [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) wird ein Dienstprinzipal für das angegebene Abonnement erstellt. Nach erfolgreichem Abschluss werden die Informationen des Dienstprinzipals (z. B. die Dienstprinzipalnamen und der Anzeigename) angezeigt. Wenn Sie `New-AzADServicePrincipal` ohne Angabe von Anmeldeinformationen für die Authentifizierung aufrufen, wird automatisch ein Kennwort generiert. Dieses Kennwort wird jedoch nicht angezeigt, weil es im Typ `SecureString` zurückgegeben wird. Daher müssen Sie `New-AzADServicePrincipal` aufrufen und dabei die Ergebnisse an eine Variable übergeben. Anschließend können Sie die Variable in Nur-Text konvertieren, um sie anzuzeigen.

1. Rufen Sie die Abonnement-ID für das zu verwendende Azure-Abonnement ab. Wenn Sie die Abonnement-ID nicht kennen, können Sie den Wert aus dem [Azure-Portal](https://portal.azure.com/) abrufen.

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
    1. Wählen Sie unter **Azure-Dienste** die Option **Abonnements** aus.
    1. Die Tabellenauflistung der Abonnements enthält eine Spalte mit den IDs der einzelnen Abonnements.

1. Starten Sie PowerShell.

1. Erstellen Sie mithilfe von [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) einen neuen Dienstprinzipal. Ersetzen Sie `<azure_subscription_id>` durch die ID des Azure-Abonnements, das Sie verwenden möchten:

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. Zeigen Sie die Namen des Dienstprinzipals an:

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Zeigen Sie das automatisch generierte Kennwort als Text ([ConvertFrom-SecureString](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/convertfrom-securestring)) an:

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**Hinweise**:

- Die Werte für Dienstprinzipalnamen und Kennwort sind erforderlich, um sich mit Ihrem Dienstprinzipal beim Abonnement anzumelden.
- Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

## <a name="log-in-to-azure-using-a-service-principal"></a>Anmelden bei Azure mithilfe eines Dienstprinzipals

Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, rufen Sie [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) auf, und geben Sie ein Objekt vom Typ [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential) an.

1. Starten Sie PowerShell.

1. Verwenden Sie eins der folgenden Verfahren, um ein Objekt vom Typ [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential) zu erhalten:

    1. Rufen Sie [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) auf, und geben Sie bei entsprechender Aufforderung Dienstprinzipalname und -kennwort ein:

        ```powershell
        $spCredential = Get-Credential
        ```

    1. Erstellen Sie ein `PsCredential`-Objekt im Arbeitsspeicher. Ersetzen Sie die Platzhalter durch die entsprechenden Werte Ihres Dienstprinzipals. Anhand dieses Musters melden Sie sich typischerweise über ein Skript an:

        ```powershell
        $spName = "<service_principal_name>"
        $spPassword = ConvertTo-SecureString "<service_principal_password>" -AsPlainText -Force
        $spCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

1. Rufen Sie `Connect-AzAccount` auf, und übergeben Sie das `PsCredential`-Objekt. Ersetzen Sie den Platzhalter `<azure_subscription_tenant_id>` durch die Mandanten-ID des Azure-Abonnements.

    ```powershell
    Connect-AzAccount -Credential $spCredential -Tenant "<azure_subscription_tenant_id>" -ServicePrincipal
    ```

## <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Legen Sie Umgebungsvariablen fest, damit Terraform das gewünschte Azure-Abonnement verwendet. Sie können die Umgebungsvariablen auf der Windows-Systemebene oder innerhalb einer bestimmten PowerShell-Sitzung festlegen. Wenn Sie die Umgebungsvariablen für eine bestimmte Sitzung festlegen möchten, verwenden Sie den folgenden Code. Ersetzen Sie die Platzhalter durch die entsprechenden Werte für Ihre Umgebung:

```powershell
$env:ARM_CLIENT_ID="<service_principal_app_id>"
$env:ARM_SUBSCRIPTION_ID="<azure_subscription_id>"
$env:ARM_TENANT_ID="<azure_subscription_tenant_id>"
```

## <a name="create-a-terraform-configuration-file"></a>Erstellen einer Terraform-Konfigurationsdatei

In diesem Abschnitt erstellen Sie eine Terraform-Konfigurationsdatei, die eine Azure-Ressourcengruppe erstellt.

1. Erstellen Sie ein Verzeichnis für die Terraform-Dateien aus dieser Demo.

    ```powershell
    mkdir QuickstartTerraformTest
    ```

1. Wechseln Sie zu diesem Demoverzeichnis.

    ```powershell
    cd QuickstartTerraformTest
    ```

1. Erstellen Sie mithilfe Ihres bevorzugten Editors eine Terraform-Konfigurationsdatei. In diesem Artikel wird [Visual Studio Code](https://code.visualstudio.com/Download) verwendet.

    ```powershell
    code QuickstartTerraformTest.tf
    ```

1. Fügen Sie den folgenden HCL-Code in die neue Datei ein. Weitere Informationen finden Sie in den Hinweisen nach dem Code.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you're using version 1.x, the "features" block isn't allowed.
      version = "~>2.0"
      features {}
    }

    resource "azurerm_resource_group" "rg" {
      name     = "QuickstartTerraformTest-rg"
      location = "eastus"
    }
    ```

    **Hinweise**:
    - Der Anbieterblock gibt an, dass der [Azure-Anbieter (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) verwendet wird.
    - Innerhalb des Anbieterblocks `azurerm` werden die Attribute `version` und `features` festgelegt. Wie Sie dem Kommentar entnehmen können, ist deren Verwendung versionsspezifisch. Weitere Informationen zum Festlegen dieser Attribute finden Sie unter [Version 2.0 des AzureRM-Anbieters](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Die einzige [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) betrifft den Ressourcentyp [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Die beiden erforderlichen Argumente für azure_resource_group sind der Name und der Speicherort.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Erstellen und Anwenden eines Terraform-Ausführungsplans

In diesem Abschnitt erstellen Sie einen *Ausführungsplan* und wenden ihn auf Ihre Cloudinfrastruktur an.

1. Initialisieren Sie die Terraform-Bereitstellung mithilfe des Befehls [terraform init](https://www.terraform.io/docs/commands/init.html). Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```powershell
    terraform init
    ```

1. Führen Sie [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus, um auf der Grundlage Ihrer Terraform-Konfigurationsdatei einen Ausführungsplan zu erstellen.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **Hinweise:**
    - Durch den Befehl `terraform plan` wird ein Ausführungsplan erstellt, aber nicht ausgeführt. Stattdessen werden die Aktionen ermittelt, die erforderlich sind, um die in Ihren Konfigurationsdateien angegebene Konfiguration zu erstellen. Mit diesem Muster können Sie überprüfen, ob der Ausführungsplan Ihren Erwartungen entspricht, bevor Sie Änderungen an den eigentlichen Ressourcen vornehmen.
    - Der optionale Parameter `-out` ermöglicht die Angabe einer Ausgabedatei für den Plan. Durch die Verwendung des Parameters `-out` wird sichergestellt, dass genau der von Ihnen überprüfte Plan angewendet wird.
    - Weitere Informationen zum Speichern von Ausführungsplänen und zur Sicherheit finden Sie im [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Führen Sie zum Anwenden des Ausführungsplans den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. Wenn der Ausführungsplan angewendet wurde, können Sie mithilfe von [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup) testen, ob die Ressourcengruppe erstellt wurde.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **Hinweise**:

    - War der Vorgang erfolgreich, werden verschiedene Eigenschaften der neu erstellten Ressourcengruppe angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

1. Führen Sie [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus, um einen Ausführungsplan zu erstellen und die in der Terraform-Konfigurationsdatei angegebenen Ressourcen zu zerstören.

    ```powershell
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **Hinweise:**
    - Durch den Befehl `terraform plan` wird ein Ausführungsplan erstellt, aber nicht ausgeführt. Stattdessen werden die Aktionen ermittelt, die erforderlich sind, um die in Ihren Konfigurationsdateien angegebene Konfiguration zu erstellen. Mit diesem Muster können Sie überprüfen, ob der Ausführungsplan Ihren Erwartungen entspricht, bevor Sie Änderungen an den eigentlichen Ressourcen vornehmen.
    - Mit dem Parameter `-destroy` wird ein Plan zum Zerstören der Ressourcen generiert.
    - Der optionale Parameter `-out` ermöglicht die Angabe einer Ausgabedatei für den Plan. Durch die Verwendung des Parameters `-out` wird sichergestellt, dass genau der von Ihnen überprüfte Plan angewendet wird.
    - Weitere Informationen zum Speichern von Ausführungsplänen und zur Sicherheit finden Sie im [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Führen Sie zum Anwenden des Ausführungsplans den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus.

    ```powershell
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. Überprüfen Sie mithilfe von [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup), ob die Ressourcengruppe gelöscht wurde.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **Hinweise**:
    - War der Vorgang erfolgreich, zeigt `Get-AzResourceGroup` an, dass die Ressourcengruppe nicht vorhanden ist.

1. Wechseln Sie zum übergeordneten Verzeichnis, und entfernen Sie das Demoverzeichnis. Durch den Parameter `-r` wird vor dem Entfernen des Verzeichnisses der Inhalt des Verzeichnisses entfernt. Zu den Verzeichnisinhalten gehören die weiter oben erstellte Konfigurationsdatei sowie die Terraform-Zustandsdateien.

    ```powershell
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](create-linux-virtual-machine-with-infrastructure.md)