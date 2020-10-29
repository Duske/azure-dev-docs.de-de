---
title: 'Schnellstart: Konfigurieren von Terraform mit Azure Cloud Shell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Terraform mithilfe von Azure Cloud Shell installieren und konfigurieren.
keywords: Azure DevOps Terraform installieren konfigurieren Cloud Shell init planen anwenden Ausführung Portal anmelden Anmeldung RBAC Dienstprinzipal automatisiertes Skript
ms.topic: quickstart
ms.date: 09/27/2020
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 70a7c1dc9db76c51d5923fc3b82200eca2976b2c
ms.sourcegitcommit: 1ddcb0f24d2ae3d1f813ec0f4369865a1c6ef322
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688914"
---
# <a name="quickstart-configure-terraform-using-azure-cloud-shell"></a>Schnellstart: Konfigurieren von Terraform mithilfe von Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel werden die ersten Schritte mit [Terraform in Azure](https://www.terraform.io/docs/providers/azurerm/index.html) beschrieben.

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Authentifizierung bei Azure
> * Erstellen eines Azure-Dienstprinzipals mit der Azure CLI
> * Authentifizieren bei Azure mit einem Dienstprinzipal
> * Festlegen des aktuellen Azure-Abonnements für die Verwendung, wenn Sie über mehrere Abonnements verfügen
> * Erstellen einer grundlegenden Terraform-Konfigurationsdatei
> * Erstellen und Anwenden eines Terraform-Ausführungsplans
> * Umkehren eines Ausführungsplans

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Falls Sie noch nicht angemeldet sind, wird im Azure-Portal eine Liste verfügbarer Microsoft-Konten angezeigt. Wählen Sie ein Microsoft-Konto aus, das mit mindestens einem aktiven Azure-Abonnement verknüpft ist, und geben Sie Ihre Anmeldeinformationen ein, um fortzufahren.

1. Öffnen Sie Cloud Shell.

    ![Zugreifen auf Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Falls Sie Cloud Shell bislang noch nicht verwendet haben, konfigurieren Sie die Umgebungs- und Speichereinstellungen. In diesem Artikel wird die Bash-Umgebung verwendet.

**Hinweise** :
- Für Cloud Shell wird automatisch die neueste Version von Terraform installiert. Darüber hinaus werden von Terraform automatisch Informationen aus dem aktuellen Azure-Abonnement verwendet. Daher ist keine Installation oder Konfiguration erforderlich.

## <a name="authenticate-to-azure"></a>Authentifizierung bei Azure

Cloud Shell wird automatisch unter dem Microsoft-Konto authentifiziert, mit dem Sie sich beim Azure-Portal angemeldet haben. Wenn Ihr Konto mehrere Azure-Abonnements enthält, können Sie [zu einem anderen Abonnements wechseln](#set-the-current-azure-subscription).

Terraform unterstützt verschiedene Optionen für die Authentifizierung bei Azure. In diesem Artikel werden die folgenden Methoden behandelt:

- Wenn Sie Terraform interaktiv verwenden, wird die [Authentifizierung über ein Microsoft-Konto](#authenticate-via-microsoft-account) empfohlen.
- Bei der Verwendung von Terraform über Code ist die [Authentifizierung über den Azure-Dienstprinzipal](#authenticate-via-azure-service-principal) eine empfohlene Methode.

### <a name="authenticate-via-microsoft-account"></a>Authentifizierung über ein Microsoft-Konto

Wenn Sie `az login` ohne Parameter ausführen, werden eine URL und ein Code angezeigt. Navigieren Sie zu der URL, geben Sie den Code ein, und befolgen Sie die Anweisungen, um sich mit Ihrem Microsoft-Konto bei Azure anzumelden. Sobald Sie angemeldet sind, kehren Sie zum Portal zurück.

```azurecli
az login
```

**Hinweise** :

- Nach erfolgreicher Anmeldung zeigt `az login` eine Liste der Azure-Abonnements an, die mit dem angemeldeten Microsoft-Konto verknüpft sind.
- Für die einzelnen verfügbaren Azure-Abonnements wird jeweils eine Liste mit Eigenschaften angezeigt. Die Eigenschaft `isDefault` gibt das verwendete Azure-Abonnement an. Im Abschnitt [Festlegen des aktuellen Azure-Abonnements](#set-the-current-azure-subscription) erfahren Sie, wie Sie zu einem anderen Azure-Abonnement wechseln.

### <a name="authenticate-via-azure-service-principal"></a>Authentifizieren über den Azure-Dienstprinzipal

**Erstellen Sie einen Azure-Dienstprinzipal** : Wenn Sie sich mithilfe eines Dienstprinzipals bei einem Azure-Abonnement anmelden möchten, benötigen Sie zunächst Zugriff auf einen Dienstprinzipal. Wenn Sie bereits über einen Dienstprinzipal verfügen, können Sie diesen Teil des Abschnitts überspringen.

Automatisierte Tools wie Terraform, die Azure-Dienste bereitstellen oder verwenden, sollten stets über eingeschränkte Berechtigungen verfügen. Azure bietet Dienstprinzipale, um zu vermeiden, dass sich Anwendungen als Benutzer mit uneingeschränkten Berechtigungen anmelden. Sollten Sie noch nicht über einen Dienstprinzipal für die Anmeldung verfügen, können Sie sich mit Ihren Benutzeranmeldeinformationen anmelden und anschließend einen Dienstprinzipal erstellen. Nach Erstellung des Dienstprinzipals können Sie dessen Informationen für zukünftige Anmeldungen verwenden.

Beim [Erstellen eines Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?) stehen zahlreiche Optionen zur Verfügung. In diesem Artikel wird mit [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) ein Dienstprinzipal mit einer Rolle **Mitwirkender** erstellt. Die Rolle **Mitwirkender** (die Standardeinstellung) verfügt über uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Integrierte Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Geben Sie den folgenden Befehl ein, und ersetzen Sie dabei `<subscription_id>` durch die ID des Abonnementkontos, das Sie verwenden möchten.

```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

**Hinweise** :

- Nach dem erfolgreichen Abschluss werden von `az ad sp create-for-rbac` verschiedene Werte angezeigt. Die Werte `name`, `password` und `tenant` werden im nächsten Schritt verwendet.
- Dieses Kennwort kann nicht erneut abgerufen werden. Es empfiehlt sich daher, das Kennwort an einem sicheren Ort zu speichern. Sollten Sie Ihr Kennwort vergessen, müssen Sie die [Anmeldeinformationen des Dienstprinzipals zurücksetzen](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).

**Anmelden mit einem Azure-Dienstprinzipal:** Ersetzen Sie im folgenden Aufruf von `az login` die Platzhalter durch Informationen Ihres Dienstprinzipals.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

## <a name="set-the-current-azure-subscription"></a>Festlegen des aktuellen Azure-Abonnements

Ein Microsoft-Konto kann mehreren Azure-Abonnements zugeordnet sein. Die folgenden Schritte beschreiben, wie Sie zwischen Ihren Abonnements wechseln können:

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

    **Hinweise:**

    - Die Ergebnisse des Wechsels zum angegebenen Azure-Abonnement werden durch den Aufruf von `az account set` nicht angezeigt. Sie können sich jedoch mithilfe von `az account show` vergewissern, dass sich das aktuelle Azure-Abonnement geändert hat.

[!INCLUDE [terraform-create-base-config-file.md](includes/terraform-create-base-config-file.md)]

[!INCLUDE [terraform-create-and-apply-execution-plan.md](includes/terraform-create-and-apply-execution-plan.md)]

[!INCLUDE [terraform-reverse-execution-plan.md](includes/terraform-reverse-execution-plan.md)]

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Linux-VM mit Infrastruktur in Azure mit Terraform](create-linux-virtual-machine-with-infrastructure.md)
