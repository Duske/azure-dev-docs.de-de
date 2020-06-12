---
title: 'Schnellstart: Erste Schritte mit Ansible: Azure Cloud Shell'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie verschiedene Ansible-Aufgaben mit Bash in Azure Cloud Shell durchführen.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, Playbook, Bash
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: 2d53c2c5e3eb834e0296be167104e484d144fbd9
ms.sourcegitcommit: 367217792f3b16c769e2c39372358bc6b9c9c044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301151"
---
# <a name="quickstart-getting-started-with-ansible---azure-cloud-shell"></a>Schnellstart: Erste Schritte mit Ansible: Azure Cloud Shell

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

In diesem Artikel werden die ersten Schritte mit Ansible in der [Azure Cloud Shell](/azure/cloud-shell/overview)-Umgebung beschrieben.

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Konfigurieren von Azure Cloud Shell**: Wenn Sie mit Azure Cloud Shell noch nicht vertraut sind, helfen Ihnen die Informationen unter [Schnellstart für Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) weiter.

[!INCLUDE [open-cloud-shell.md](../includes/open-cloud-shell.md)]

## <a name="automatic-credential-configuration"></a>Automatische Konfiguration von Anmeldeinformationen

Wenn Sie bei Cloud Shell angemeldet sind, führt Ansible eine Authentifizierung bei Azure durch, um die Infrastruktur ohne zusätzliche Konfiguration verwalten zu können. 

Geben Sie bei Verwendung von mehreren Abonnements das von Ansible genutzte Abonnement an, indem Sie die Umgebungsvariable `AZURE_SUBSCRIPTION_ID` exportieren. 

Zum Auflisten all Ihrer Azure-Abonnements führen Sie den folgenden Befehl aus:

```azurecli-interactive
az account list
```

Legen Sie die `AZURE_SUBSCRIPTION_ID` wie folgt fest, indem Sie Ihre Azure-Abonnement-ID verwenden:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Überprüfen der Konfiguration
Erstellen Sie mit Ansible eine Azure-Ressourcengruppe, um zu überprüfen, ob die Konfiguration erfolgreich war.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Schnellstart: Konfigurieren eines virtuellen Computers in Azure mit Ansible](./vm-configure.md)