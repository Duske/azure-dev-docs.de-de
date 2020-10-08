---
title: 'Schnellstart: Konfigurieren von Terraform mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform mithilfe von Azure PowerShell installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Windows init planen anwenden Ausführung anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript PowerShell
ms.topic: quickstart
ms.date: 09/27/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f95d0bb09d7e9e7ea789b90a27178cdf5426d74
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401546"
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
> * Erstellen einer grundlegenden Terraform-Konfigurationsdatei
> * Erstellen und Anwenden eines Terraform-Ausführungsplans
> * Umkehren eines Ausführungsplans

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. Das aktuelle PowerShell-Modul, das die Interaktion mit Azure-Ressourcen ermöglicht, heißt [Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az). Wenn Sie das Azure PowerShell Az-Modul verwenden, wird auf allen Plattformen mindestens Version 7 von PowerShell empfohlen. Wenn Sie PowerShell installiert haben, können Sie die Version überprüfen, indem Sie den folgenden Befehl an einer PowerShell-Eingabeaufforderung eingeben:

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [Installieren Sie PowerShell.](/powershell/scripting/install/installing-powershell-core-on-windows) Diese Demo wurde mithilfe von PowerShell 7.0.2 unter Windows 10 getestet.

1. Für die [Authentifizierung von Terraform bei Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html) müssen Sie die [Azure CLI installieren](/cli/azure/install-azure-cli-windows). Diese Demo wurde mit der Azure CLI-Version 2.9.1 getestet.

1. [Laden Sie Terraform herunter](https://www.terraform.io/downloads.html).

1. Extrahieren Sie die ausführbare Datei aus dem Download in ein Verzeichnis Ihrer Wahl.

1. [Aktualisieren Sie den globalen Pfad Ihres Systems](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) auf die ausführbare Datei.

1. Überprüfen Sie die Konfiguration des globalen Pfads mit dem Befehl `terraform`.

    ```powershell
    terraform
    ```

    **Hinweise**:
    - Wenn die ausführbare Terraform-Datei gefunden wird, werden die Syntax und verfügbare Befehle aufgelistet.

## <a name="authenticate-to-azure"></a>Authentifizierung bei Azure

Bei Verwendung von PowerShell und Terraform müssen Sie sich mithilfe eines Dienstprinzipals anmelden. In den nächsten beiden Abschnitten werden die folgenden Aufgaben veranschaulicht:

- [Erstellen eines Azure-Dienstprinzipals](#create-an-azure-service-principal)
- [Anmelden bei Azure mithilfe eines Dienstprinzipals](#log-in-to-azure-using-a-service-principal)


### <a name="span-idcreate-an-azure-service-principalcreate-an-azure-service-principal"></a><span id="create-an-azure-service-principal"/>Erstellen eines Azure-Dienstprinzipals

Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, benötigen Sie zunächst Zugriff auf einen Dienstprinzipal. Wenn Sie bereits einen Dienstprinzipal haben, können Sie diesen Abschnitt überspringen.

Beim [Erstellen eines Dienstprinzipals mit PowerShell](/powershell/azure/create-azure-service-principal-azureps) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird ein Dienstprinzipal mit der Rolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** (die Standardrolle) verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Durch Aufrufen von [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) wird ein Dienstprinzipal für das angegebene Abonnement erstellt. Nach erfolgreichem Abschluss werden die Informationen des Dienstprinzipals (z. B. die Dienstprinzipalnamen und der Anzeigename) angezeigt. Wenn Sie `New-AzADServicePrincipal` ohne Angabe von Anmeldeinformationen für die Authentifizierung aufrufen, wird automatisch ein Kennwort generiert. Dieses Kennwort wird jedoch nicht angezeigt, weil es im Typ `SecureString` zurückgegeben wird. Daher müssen Sie `New-AzADServicePrincipal` aufrufen und dabei die Ergebnisse an eine Variable übergeben. Anschließend können Sie die Variable in Nur-Text konvertieren, um sie anzuzeigen.

1. Rufen Sie die Abonnement-ID für das zu verwendende Azure-Abonnement ab. Wenn Sie die Abonnement-ID nicht kennen, können Sie den Wert aus dem [Azure-Portal](https://portal.azure.com/) abrufen.

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
    1. Wählen Sie unter **Azure-Dienste** die Option **Abonnements** aus.
    1. Die Tabellenauflistung der Abonnements enthält eine Spalte mit den IDs der einzelnen Abonnements.

1. Starten Sie PowerShell.

1. Erstellen Sie mithilfe von [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) einen neuen Dienstprinzipal. Ersetzen Sie `<azure_subscription_id>` durch die ID des Azure-Abonnements, das Sie verwenden möchten:

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. Zeigen Sie die Namen des Dienstprinzipals an:

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Zeigen Sie das automatisch generierte Kennwort als Text ([ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)) an:

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**Hinweise**:

- Die Werte für Dienstprinzipalnamen und Kennwort sind erforderlich, um sich mit Ihrem Dienstprinzipal beim Abonnement anzumelden.
- Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

### <a name="span-idlog-in-to-azure-using-a-service-principallog-in-to-azure-using-a-service-principal"></a><span id="log-in-to-azure-using-a-service-principal"/>Anmelden bei Azure mithilfe eines Dienstprinzipals

Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, rufen Sie [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) auf, und geben Sie ein Objekt vom Typ [PsCredential](/dotnet/api/system.management.automation.pscredential) an.

1. Starten Sie PowerShell.

1. Verwenden Sie eins der folgenden Verfahren, um ein Objekt vom Typ [PsCredential](/dotnet/api/system.management.automation.pscredential) zu erhalten:

    1. Rufen Sie [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) auf, und geben Sie bei entsprechender Aufforderung Dienstprinzipalname und -kennwort ein:

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

[!INCLUDE [terraform-create-base-config-file.md](includes/terraform-create-base-config-file.md)]

[!INCLUDE [terraform-create-and-apply-execution-plan.md](includes/terraform-create-and-apply-execution-plan.md)]

[!INCLUDE [terraform-reverse-execution-plan.md](includes/terraform-reverse-execution-plan.md)]

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Linux-VM mit Infrastruktur in Azure mit Terraform](create-linux-virtual-machine-with-infrastructure.md)
