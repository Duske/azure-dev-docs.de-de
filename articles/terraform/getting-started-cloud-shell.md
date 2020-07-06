---
title: 'Schnellstart: Erste Schritte mit Terraform mit Azure Cloud Shell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform für die Erstellung von Azure-Ressourcen installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Cloud Shell init planen anwenden Ausführung Portal anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: 4b0f6802673d886cecdc9523d99886c19fbad94a
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84779662"
---
# <a name="quickstart-getting-started-with-terraform-using-azure-cloud-shell"></a>Schnellstart: Erste Schritte mit Terraform mit Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel werden die ersten Schritte mit [Terraform in Azure](https://www.terraform.io/docs/providers/azurerm/index.html) in der [Azure Cloud Shell](/azure/cloud-shell/overview)-Umgebung beschrieben.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-azure-cloud-shell"></a>Konfigurieren von Azure Cloud Shell

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Falls Sie noch nicht angemeldet sind, wird im Azure-Portal eine Liste verfügbarer Microsoft-Konten angezeigt. Wählen Sie ein Microsoft-Konto aus, das mit mindestens einem aktiven Azure-Abonnement verknüpft ist, und geben Sie Ihre Anmeldeinformationen ein, um fortzufahren.

1. Öffnen Sie Cloud Shell.

    ![Zugreifen auf Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Falls Sie Cloud Shell bislang noch nicht verwendet haben, konfigurieren Sie die Umgebungs- und Speichereinstellungen. In diesem Artikel wird die Bash-Umgebung verwendet.

## <a name="log-into-azure"></a>Anmelden bei Azure

Cloud Shell wird automatisch unter dem Microsoft-Konto authentifiziert, mit dem Sie sich beim Azure-Portal angemeldet haben.

Außerdem werden Sie nach der Anmeldung bei Ihrem Microsoft-Konto automatisch beim Azure-Standardabonnement für dieses Konto angemeldet. Wenn das aktuelle Microsoft-Konto richtig ist und Sie Abonnements wechseln möchten, lesen Sie den Abschnitt [Angeben des aktuellen Azure-Abonnements](#specify-the-current-azure-subscription).

Sollten Sie über mehrere Microsoft-Konten mit Azure-Abonnements verfügen, können Sie sich mit einer der folgenden Optionen bei einem dieser Konten anmelden:

- [Anmelden bei Ihrem Microsoft-Konto](#log-into-your-microsoft-account)
- [Anmelden mit einem Azure-Dienstprinzipal](#log-into-azure-using-an-azure-service-principal)

### <a name="log-into-your-microsoft-account"></a>Anmelden bei Ihrem Microsoft-Konto

Wenn Sie `az login` ohne Parameter ausführen, werden eine URL und ein Code angezeigt. Navigieren Sie zu der URL, geben Sie den Code ein, und befolgen Sie die Anweisungen, um sich mit Ihrem Microsoft-Konto bei Azure anzumelden. Sobald Sie angemeldet sind, kehren Sie zum Portal zurück.

```azurecli
az login
```

Hinweise:
- Nach erfolgreicher Anmeldung zeigt `az login` eine Liste der Azure-Abonnements an, die mit dem angemeldeten Microsoft-Konto verknüpft sind.
- Für die einzelnen verfügbaren Azure-Abonnements wird jeweils eine Liste mit Eigenschaften angezeigt. Die Eigenschaft `isDefault` gibt das verwendete Azure-Abonnement an. Im Abschnitt [Angeben des aktuellen Azure-Abonnements](#specify-the-current-azure-subscription) erfahren Sie, wie Sie zu einem anderen Azure-Abonnement wechseln.

### <a name="log-into-azure-using-an-azure-service-principal"></a>Anmelden bei Azure mit einem Azure-Dienstprinzipal

**Erstellen Sie einen Azure-Dienstprinzipal**: Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, benötigen Sie zunächst Zugriff auf einen Dienstprinzipal. Wenn Sie bereits über einen Dienstprinzipal verfügen, können Sie diesen Teil des Abschnitts überspringen.

Automatisierte Tools wie Terraform, die Azure-Dienste bereitstellen oder verwenden, sollten stets über eingeschränkte Berechtigungen verfügen. Azure bietet Dienstprinzipale, um zu vermeiden, dass sich Anwendungen als Benutzer mit uneingeschränkten Berechtigungen anmelden. Sollten Sie noch nicht über einen Dienstprinzipal für die Anmeldung verfügen, können Sie sich mit Ihren Benutzeranmeldeinformationen anmelden und anschließend einen Dienstprinzipal erstellen. Nach Erstellung des Dienstprinzipals können Sie dessen Informationen für zukünftige Anmeldungen verwenden.

Beim [Erstellen eines Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird mit [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) ein Dienstprinzipal mit einer Rolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** (die Standardeinstellung) verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Geben Sie den folgenden Befehl ein, und ersetzen Sie dabei `<subscription_id>` durch die ID des Abonnementkontos, das Sie verwenden möchten.
    
```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

Hinweise:
- Nach erfolgreichem Abschluss zeigt `az ad sp create-for-rbac` mehrere Werte an, z. B. das automatisch generierte Kennwort. Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
    
**Anmelden mit einem Azure-Dienstprinzipal:** Ersetzen Sie im folgenden Aufruf von `az login` die Platzhalter durch Informationen Ihres Dienstprinzipals.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

Hinweise:
- Nach erfolgreicher Anmeldung zeigt `az login` verschiedene Eigenschaften für das Azure-Abonnement an, z. B. `id` und `name`.

## <a name="specify-the-current-azure-subscription"></a>Angeben des aktuellen Azure-Abonnements

Wie bereits im vorherigen Abschnitt erläutert, stehen für die Anmeldung bei Azure unter anderem die beiden folgenden Szenarien zur Verfügung:

- **Anmelden mit einem Microsoft-Konto:** Ein Microsoft-Konto kann mehreren Azure-Abonnements zugeordnet werden, wobei eines davon als Standardabonnement fungiert. Das Standardabonnement wird verwendet, wenn Sie sich anmelden und nicht zu einem anderen Abonnement wechseln.
- **Anmelden mit einem Azure-Dienstprinzipal:** Ein Dienstprinzipal ist für ein Azure-Abonnement spezifisch. Wie bereits erwähnt, werden die Abonnementinformationen angezeigt, wenn Sie sich anmelden.

Die folgenden Schritte gelten für das erste Szenario und umfassen folgende Aufgaben:

- Anzeigen des aktuellen Azure-Abonnements
- Auflisten aller verfügbaren Azure-Abonnements für das aktuelle Microsoft-Konto
- Wechseln zu einem anderen Azure-Abonnement

1. Verwenden Sie [az account show](/cli/azure/account#az-account-show), um das aktuelle Azure-Abonnement anzuzeigen.

    ```azurecli
    az account show
    ```
    
1. Wenn Sie Zugriff auf mehrere verfügbare Azure-Abonnements haben, verwenden Sie [az account list](/cli/azure/account#az-account-list), um eine Liste mit Abonnementnamen/-IDs anzuzeigen:

    ```azurecli
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Wenn Sie für die aktuelle Cloud Shell-Sitzung ein bestimmtes Azure-Abonnement verwenden möchten, verwenden Sie [az account set](/cli/azure/account#az-account-set). Ersetzen Sie den Platzhalter `<subscription_id>` durch die ID (oder den Namen) des gewünschten Abonnements:

    ```azurecli
    az account set --subscription="<subscription_id>"
    ```

    Hinweise:
    - Die Ergebnisse des Wechsels zum angegebenen Azure-Abonnement werden durch den Aufruf von `az account set` nicht angezeigt. Sie können sich jedoch mithilfe von `az account show` vergewissern, dass sich das aktuelle Azure-Abonnement geändert hat.

## <a name="configure-terraform"></a>Konfigurieren von Terraform

Für Cloud Shell wird automatisch die neueste Version von Terraform installiert. Darüber hinaus werden von Terraform automatisch Informationen aus dem aktuellen Azure-Abonnement verwendet. Daher ist keine Installation oder Konfiguration erforderlich.

## <a name="create-a-terraform-configuration-file"></a>Erstellen einer Terraform-Konfigurationsdatei

In diesem Abschnitt erfahren Sie, wie Sie eine Terraform-Konfigurationsdatei erstellen, die eine Azure-Ressourcengruppe erstellt.

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

1. Erstellen Sie mithilfe Ihres bevorzugten Editors eine Terraform-Konfigurationsdatei. In diesem Artikel wird der integrierte [Cloud Shell-Editor](/azure/cloud-shell/using-cloud-shell-editor) verwendet.

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

    Hinweise:
    - Der Block `provider` dient zum Angeben der Verwendung des [Azure-Anbieters (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html).
    - Innerhalb des Anbieterblocks `azurerm` werden die Attribute `version` und `features` festgelegt. Wie Sie dem Kommentar entnehmen können, ist deren Verwendung versionsspezifisch. Weitere Informationen zum Festlegen dieser Attribute für Ihre Umgebung finden Sie unter [Version 2.0 des AzureRM-Anbieters](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - Die einzige [Ressourcendeklaration](https://www.terraform.io/docs/configuration/resources.html) betrifft den Ressourcentyp [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Für `azure_resource_group` sind die Argumente `name` und `location` erforderlich.

1. Drücken Sie **STRG+S**, um die Datei zu speichern.

1. Drücken Sie **STRG+Q**, um den Editor zu beenden.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Erstellen und Anwenden eines Terraform-Ausführungsplans

Nachdem Sie Ihre Konfigurationsdateien erstellt haben, wird in diesem Abschnitt erläutert, wie ein *Ausführungsplan* erstellt und auf Ihre Cloudinfrastruktur anwendet wird.

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

1. Vergewissern Sie sich nach der Bestätigung der Planausführung mithilfe des Befehls [az group show](/cli/azure/group?#az-group-show), dass die Ressourcengruppe erfolgreich wurde.

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    War der Vorgang erfolgreich, zeigt `az group show` verschiedene Eigenschaften der neu erstellten Ressourcengruppe an.

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

Hinweise:
- Um die Verwendung mit Automatisierung zu ermöglichen, kann `terraform apply <filename>` ohne Bestätigung ausgeführt werden.
- Wenn Sie dieses Feature verwenden möchten, lesen Sie den [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

1. Führen Sie [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) aus, um den aktuellen Ausführungsplan umzukehren.

    ```bash
    terraform destroy
    ```

1. Von Terraform wird gezeigt, was passiert, wenn Sie den Ausführungsplan umkehren, und Sie werden zur Bestätigung des Vorgangs aufgefordert. Geben Sie zur Bestätigung `yes` ein, und drücken Sie die **EINGABETASTE**.

1. Die Ausgabe nach der Bestätigung der Planausführung sieht in etwa wie im folgenden Beispiel aus. Vergewissern Sie sich mithilfe des Befehls [az group show](/cli/azure/group?#az-group-show), dass die Ressourcengruppe gelöscht wurde.

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    Hinweise:
    - War der Vorgang erfolgreich, zeigt `az group show` an, dass die Ressourcengruppe nicht vorhanden ist.

1. Wechseln Sie zum übergeordneten Verzeichnis, und entfernen Sie das Demoverzeichnis. Durch den Parameter `-r` wird vor dem Entfernen des Verzeichnisses der Inhalt des Verzeichnisses entfernt. Zu den Verzeichnisinhalten gehören die weiter oben erstellte Konfigurationsdatei sowie die Terraform-Zustandsdateien.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](create-linux-virtual-machine-with-infrastructure.md)