---
title: 'Schnellstart: Erste Schritte mit Terraform unter Windows'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform für die Erstellung von Azure-Ressourcen installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Windows init planen anwenden Ausführung anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript CLI PowerShell
ms.topic: quickstart
ms.date: 06/12/2020
ms.openlocfilehash: d28a79af9a59551153f297cebfb0c51ed2e72838
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783802"
---
# <a name="quickstart-getting-started-with-terraform-using-windows"></a>Schnellstart: Erste Schritte mit Terraform unter Windows
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. PowerShell 7.x (oder höher) ist die für die Verwendung mit Azure PowerShell auf allen Plattformen (einschließlich Windows) empfohlene PowerShell-Version. Wenn Sie PowerShell installiert haben, können Sie die Version überprüfen, indem Sie Folgendes an einer PowerShell-Eingabeaufforderung eingeben:

    ```powershell
    $PSVersionTable.PSVersion
    ```
    
1. Befolgen Sie die Anweisungen im Artikel [Installieren von PowerShell unter Windows](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7), um die neueste Version von PowerShell zu installieren.

1. Dieser Artikel verwendet das [Azure PowerShell-Az-Modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Befolgen Sie die Anweisungen im Artikel [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. Öffnen Sie eine Instanz der soeben installierten PowerShell-Version.

## <a name="log-into-azure"></a>Anmelden bei Azure

Es gibt mehrere Optionen, die es Ihnen ermöglichen, sich bei einem Azure-Abonnement anzumelden.

- [Anmelden bei Ihrem Microsoft-Konto](#log-into-your-microsoft-account)
- [Anmelden mit einem Azure-Dienstprinzipal](#log-into-azure-using-an-azure-service-principal)

### <a name="log-into-your-microsoft-account"></a>Anmelden bei Ihrem Microsoft-Konto

Wenn Sie [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) ohne Parameter ausführen, werden eine URL und ein Code angezeigt. Navigieren Sie zu der URL, geben Sie den Code ein, und befolgen Sie die Anweisungen, um sich mit Ihrem Microsoft-Konto bei Azure anzumelden. Sobald Sie angemeldet sind, kehren Sie zum Portal zurück.

```powershell
Connect-AzAccount
```

Hinweise:
- Nach erfolgreicher Anmeldung zeigt `Connect-AzAccount` die Azure-Standardabonnements an, die mit dem angemeldeten Microsoft-Konto verknüpft sind. Im Abschnitt [Angeben des aktuellen Azure-Abonnements](#specify-the-current-azure-subscription) erfahren Sie, wie Sie zu einem anderen Azure-Abonnement wechseln.

### <a name="log-into-azure-using-an-azure-service-principal"></a>Anmelden bei Azure mit einem Azure-Dienstprinzipal

**Erstellen Sie einen Azure-Dienstprinzipal**: Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, benötigen Sie zunächst Zugriff auf einen Dienstprinzipal. Wenn Sie bereits über einen Dienstprinzipal verfügen, können Sie diesen Teil des Abschnitts überspringen.

Automatisierte Tools wie Terraform, die Azure-Dienste bereitstellen oder verwenden, sollten stets über eingeschränkte Berechtigungen verfügen. Azure bietet Dienstprinzipale, um zu vermeiden, dass sich Anwendungen als Benutzer mit uneingeschränkten Berechtigungen anmelden. Sollten Sie noch nicht über einen Dienstprinzipal für die Anmeldung verfügen, können Sie sich mit Ihren Benutzeranmeldeinformationen anmelden und anschließend einen Dienstprinzipal erstellen. Nach Erstellung des Dienstprinzipals können Sie dessen Informationen für zukünftige Anmeldungen verwenden.

Beim [Erstellen eines Dienstprinzipals mit PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird ein Dienstprinzipal mit der Rolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** (die Standardrolle) verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Durch Aufrufen von [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) wird ein Dienstprinzipal für das angegebene Abonnement erstellt. Nach erfolgreichem Abschluss werden die Informationen des Dienstprinzipals (z. B. die Dienstprinzipalnamen und der Anzeigename) angezeigt. Wenn Sie `New-AzADServicePrincipal` ohne Angabe von Anmeldeinformationen für die Authentifizierung aufrufen, wird automatisch ein Kennwort generiert. Dieses Kennwort wird jedoch nicht angezeigt, weil es im Typ `SecureString` zurückgegeben wird. Daher müssen Sie `New-AzADServicePrincipal` aufrufen und dabei die Ergebnisse an eine Variable übergeben. Anschließend können Sie die Variable abfragen, um das Kennwort zu ermitteln.

1. Geben Sie den folgenden Befehl ein, und ersetzen Sie dabei `<subscription_id>` durch die ID des Abonnementkontos, das Sie verwenden möchten.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<subscription_id>
    ```

1. Geben Sie Folgendes ein, um die Namen des Dienstprinzipals anzuzeigen:

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Rufen Sie `ConvertFrom-SecureString` auf, um das Kennwort als Text anzuzeigen:

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

Hinweise:
- An diesem Punkt kennen Sie die Namen und das Kennwort des Dienstprinzipals. Diese Werte sind erforderlich, um sich mit Ihrem Dienstprinzipal beim Abonnement anzumelden.
- Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

**Verwenden eines Azure-Dienstprinzipals für die Anmeldung**: Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, rufen Sie `Connect-AzAccount` auf, und übergeben Sie ein Objekt vom Typ [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential). Es gibt zwei Optionen: interaktiv und Skript.

- **Interaktives Muster**: Sie rufen [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) auf und geben die Anmeldeinformationen ein, wenn Sie dazu aufgefordert werden. Der Aufruf von `Get-Credential` gibt ein `PsCredential`-Objekt zurück, das Sie dann an `Connect-AzAccount` übergeben.

    1. Rufen Sie `Get-Credential` auf, und geben Sie manuell einen Dienstprinzipalnamen und ein Kennwort ein:

        ```powershell
        $Credential = Get-Credential
        ```

    2. Rufen Sie `Connect-AzAccount` auf, und übergeben Sie das `PsCredential`-Objekt. (Ersetzen Sie den Platzhalter `<azureSubscriptionTenantId>` durch die Mandanten-ID des Azure-Abonnements.)

        ```powershell
        Connect-AzAccount -Credential $Credential -Tenant <azureSubscriptionTenantId> -ServicePrincipal
        ```

- **Skriptmuster**: Sie erstellen ein `PsCredential`-Objekt und übergeben es an `Connect-AzConnect`.

    1. Erstellen Sie ein `Get-Credential`. (Ersetzen Sie die Platzhalter durch die entsprechenden Werte für Ihr Azure-Abonnement und den Dienstprinzipal.)

        ```powershell
        $spName = "<servicePrincipalName>"
        $spPassword = ConvertTo-SecureString "<servicePrincipalPassword>" -AsPlainText -Force
        $psCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

    1. Rufen Sie `Connect-AzAccount` auf, und übergeben Sie das erstellte `PsCredential`-Objekt:

        ```powershell
        Connect-AzAccount -Credential $psCredential -TenantId "<azureSubscriptionTenantId>"  -ServicePrincipal
        ```

## <a name="specify-the-current-azure-subscription"></a>Angeben des aktuellen Azure-Abonnements

Wie bereits im vorherigen Abschnitt erläutert, stehen für die Anmeldung bei Azure unter anderem die beiden folgenden Szenarien zur Verfügung:

- **Anmelden mit einem Microsoft-Konto:** Ein Microsoft-Konto kann mehreren Azure-Abonnements zugeordnet werden, wobei eines davon als Standardabonnement fungiert. Das Standardabonnement wird verwendet, wenn Sie sich anmelden und nicht zu einem anderen Abonnement wechseln.
- **Anmelden mit einem Azure-Dienstprinzipal:** Ein Dienstprinzipal ist für ein Azure-Abonnement spezifisch. Wie bereits erwähnt, werden die Abonnementinformationen angezeigt, wenn Sie sich anmelden.

Die folgenden Schritte gelten für das erste Szenario und umfassen folgende Aufgaben:

- Anzeigen des aktuellen Azure-Abonnements
- Auflisten aller verfügbaren Azure-Abonnements für das aktuelle Microsoft-Konto
- Wechseln zu einem anderen Azure-Abonnement

1. Verwenden Sie [Get-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext), um das aktuelle Azure-Abonnement anzuzeigen.

    ```powershell
    Get-AzContext
    ```

1. Wenn Sie Zugriff auf mehrere verfügbare Azure-Abonnements besitzen, verwenden Sie `Get-AzContext -ListAvailable`:

    ```powershell
    Get-AzContext -ListAvailable | Select Name
    ```

1. Die automatisch generierten Kontextnamen können sperrig sein. Um die Kontextnamen lesbarer zu machen (und leichter als Parameter zu verwenden), können Sie die Kontexte umbenennen. Das Umbenennen eines Kontexts erfolgt über [Rename-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/rename-azcontext).

    ```powershell
    Rename-AzContext -SourceName <current_context_name> -TargetName <new_context_Name>
    ```

1. Um ein bestimmtes Azure-Abonnement für die aktuelle PowerShell-Sitzung zu verwenden, verwenden Sie [Select-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/select-azcontext).

    ```powershell
    Select-AzContext <context_name>
    ```

## <a name="configure-terraform"></a>Konfigurieren von Terraform

1. [Laden Sie Terraform herunter](https://www.terraform.io/downloads.html).

1. Extrahieren Sie die ausführbare Datei aus dem Download in ein Verzeichnis Ihrer Wahl.

1. [Aktualisieren Sie den globalen Pfad Ihres Systems](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) auf die ausführbare Datei.

1. Überprüfen Sie die Konfiguration des globalen Pfads mit dem Befehl `terraform`. Wenn die ausführbare Terraform-Datei gefunden wird, wird eine Liste mit den verfügbaren Terraform-Optionen angezeigt:

    ```powershell
    terraform
    ```

    Wenn die ausführbare Terraform-Datei gefunden wird, werden die Syntax und verfügbare Befehle aufgelistet:

    ```output
    Usage: terraform [-version] [-help] <command> [args]

    The available commands for execution are listed below.
    The most common, useful commands are shown first, followed by
    less common or more advanced commands. If you're just getting
    started with Terraform, stick with the common commands. For the
    other commands, please read the help and docs before usage.
    ...
    ```

## <a name="create-a-terraform-configuration-file"></a>Erstellen einer Terraform-Konfigurationsdatei

In diesem Abschnitt erfahren Sie, wie Sie eine Terraform-Konfigurationsdatei erstellen, die eine Azure-Ressourcengruppe erstellt.

1. Erstellen Sie ein Verzeichnis für die Terraform-Dateien aus dieser Demo.

1. Wechseln Sie zu diesem Demoverzeichnis.

1. Erstellen Sie mithilfe Ihres bevorzugten Editors eine Terraform-Konfigurationsdatei mit dem Namen `QuickstartTerraformTest.tf`.

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

    Hinweise:
    - Der Anbieterblock gibt an, dass der [Azure-Anbieter (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) verwendet wird.
    - Im azurerm-Anbieterblock werden die Version und die Featureattribute festgelegt. Wie Sie dem Kommentar entnehmen können, ist deren Verwendung versionsspezifisch. Weitere Informationen zum Festlegen dieser Attribute für Ihre Umgebung finden Sie unter [Version 2.0 des AzureRM-Anbieters](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Die einzige [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) betrifft den Ressourcentyp [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Die beiden erforderlichen Argumente für azure_resource_group sind der Name und der Speicherort.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Erstellen und Anwenden eines Terraform-Ausführungsplans

Nachdem Sie Ihre Konfigurationsdateien erstellt haben, wird in diesem Abschnitt erläutert, wie ein *Ausführungsplan* erstellt und auf Ihre Cloudinfrastruktur anwendet wird.

1. Initialisieren Sie die Terraform-Bereitstellung mithilfe des Befehls [terraform init](https://www.terraform.io/docs/commands/init.html). Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```powershell
    terraform init
    ```
    
1. Mithilfe des Terraform-Befehls [terraform plan](https://www.terraform.io/docs/commands/plan.html) können Sie eine Vorschau der auszuführenden Aktionen anzeigen.

    ```powershell
    terraform plan
    ```

    **Hinweise:**
    - Durch den Befehl `terraform plan` wird ein Ausführungsplan erstellt, aber nicht ausgeführt. Stattdessen werden die Aktionen ermittelt, die erforderlich sind, um die in Ihren Konfigurationsdateien angegebene Konfiguration zu erstellen.
    - Mit dem Befehl `terraform plan` können Sie überprüfen, ob der Ausführungsplan Ihren Erwartungen entspricht, bevor Sie Änderungen an den eigentlichen Ressourcen vornehmen.
    - Der optionale Parameter `-out` ermöglicht die Angabe einer Ausgabedatei für den Plan. Weitere Informationen zur Verwendung des Parameters `-out` finden Sie im Abschnitt [Speichern eines Ausführungsplans zur späteren Bereitstellung](#persist-an-execution-plan-for-later-deployment).

1. Wenden Sie den Ausführungsplan mithilfe des Befehls [terraform apply](https://www.terraform.io/docs/commands/apply.html) an.

    ```powershell
    terraform apply
    ```
    
1. Von Terraform wird gezeigt, was passiert, wenn Sie den Ausführungsplan anwenden, und Sie werden zur Bestätigung der Ausführung aufgefordert. Geben Sie zur Bestätigung des Befehls `yes` ein, und drücken Sie die **EINGABETASTE**.

1. Vergewissern Sie sich nach der Bestätigung der Planausführung mithilfe des Befehls [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show), dass die Ressourcengruppe erfolgreich wurde.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    War der Vorgang erfolgreich, werden verschiedene Eigenschaften der neu erstellten Ressourcengruppe angezeigt.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Speichern eines Ausführungsplans zur späteren Bereitstellung

Im vorherigen Abschnitt wurde gezeigt, wie Sie einen [Terraform-Plan](https://www.terraform.io/docs/commands/plan.html) ausführen, um einen Ausführungsplan zu erstellen. Anschließend wurde gezeigt, dass der Plan mithilfe von [terraform apply](https://www.terraform.io/docs/commands/apply.html) angewendet wird. Dieses Muster eignet sich bestens, wenn die Schritte interaktiv und sequenziell sind.

In komplexeren Szenarien kann der Ausführungsplan in einer Datei gespeichert werden. Dieser Ausführungsplan kann dann später (oder sogar von einem anderen Computer aus) angewendet werden.

Falls Sie dieses Feature nutzen möchten, empfiehlt es sich, den Artikel [Ausführen von Terraform mit Automatisierung](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation) zu lesen.

Die folgenden Schritte veranschaulichen das grundlegende Muster für die Nutzung dieses Features:

1. Führen Sie [terraform init](https://www.terraform.io/docs/commands/init.html) aus.

    ```powershell
    terraform init
    ```

1. Führen Sie `terraform plan` mit dem Parameter `-out` aus.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Führen Sie `terraform apply` aus, und geben Sie dabei den Namen der Datei aus dem vorherigen Schritt an.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

Hinweise:
- Um die Verwendung mit Automatisierung zu ermöglichen, kann `terraform apply <filename>` ohne Bestätigung ausgeführt werden.
- Wenn Sie dieses Feature verwenden möchten, lesen Sie den [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

1. Führen Sie [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) aus, um den aktuellen Ausführungsplan umzukehren.

    ```powershell
    terraform destroy
    ```

1. Von Terraform wird gezeigt, was passiert, wenn Sie den Ausführungsplan umkehren, und Sie werden zur Bestätigung des Vorgangs aufgefordert. Geben Sie zur Bestätigung des Befehls `yes` ein, und drücken Sie die **EINGABETASTE**.

1. Die Ausgabe nach der Bestätigung der Planausführung sieht in etwa wie im folgenden Beispiel aus. Vergewissern Sie sich mithilfe des Befehls [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show), dass die Ressourcengruppe gelöscht wurde.

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    Hinweise:
    - War der Vorgang erfolgreich, zeigt `Get-AzResourceGroup` an, dass die Ressourcengruppe nicht vorhanden ist.

1. Wechseln Sie zum übergeordneten Verzeichnis, und entfernen Sie das Demoverzeichnis. Durch den Parameter `-r` wird vor dem Entfernen des Verzeichnisses der Inhalt des Verzeichnisses entfernt. Zu den Verzeichnisinhalten gehören die weiter oben erstellte Konfigurationsdatei sowie die Terraform-Zustandsdateien.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](create-linux-virtual-machine-with-infrastructure.md)