---
title: 'Schnellstart: Bereitstellen der Ansible-Lösungsvorlage für Azure in CentOS'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Ansible-Lösungsvorlage zusammen mit Tools, die für die Arbeit mit Azure konfiguriert sind, auf einem in Azure gehosteten virtuellen CentOS-Computer bereitstellen.
keywords: Ansible, Azure, DevOps, Lösungsvorlage, virtueller Computer, verwaltete Identitäten für Azure-Ressourcen, CentOS, Red Hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: ac6d5f550447c11a463fb2d002c95a1242c08afb
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80741058"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Schnellstart: Bereitstellen der Ansible-Lösungsvorlage für Azure in CentOS

Die Ansible-Lösungsvorlage für Azure dient dazu, eine Ansible-Instanz auf einem virtuellen CentOS-Computer zu konfigurieren – zusammen mit Ansible und einer Reihe von Tools, die für die Arbeit mit Azure konfiguriert sind. Zu diesen Tools zählen:

- **Ansible-Module für Azure:** Bei den [Ansible-Modulen für Azure](./module-version-matrix.md) handelt es sich um eine Sammlung von Modulen, mit denen Sie Ihre Infrastruktur in Azure erstellen und verwalten können. Standardmäßig wird die neueste Version dieser Module bereitgestellt. Im Rahmen des Bereitstellungsprozesses für die Lösungsvorlage kann jedoch auch eine für Ihre Umgebung geeignete Versionsnummer angegeben werden.
- **Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI) 2.0:** Die [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) ist eine plattformübergreifende Befehlszeilenumgebung von Microsoft für die Verwaltung von Azure-Ressourcen. 
- **Verwaltete Identitäten für Azure-Ressourcen:** Die [verwalteten Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) dienen zum Schutz der Anmeldeinformationen für Cloudanwendungen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Bereitstellen der Ansible-Lösungsvorlage

1. Navigieren Sie zur [Ansible-Lösungsvorlage im Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Wählen Sie **Jetzt herunterladen** aus.

1. Daraufhin wird ein Fenster mit den Nutzungsbedingungen, der Datenschutzrichtlinie und den Azure Marketplace-Nutzungsbedingungen angezeigt. Wählen Sie **Weiter**.

1. Im Azure-Portal wird die Ansible-Seite mit einer Beschreibung der Lösungsvorlage angezeigt. Klicken Sie auf **Erstellen**.

1. Auf der Seite **Ansible erstellen** werden mehrere Registerkarten angezeigt. Geben Sie auf der Registerkarte **Grundeinstellungen** die erforderlichen Informationen ein:

   - **Name**: Geben Sie den Namen Ihrer Ansible-Instanz an. Zu Demonstrationszwecken wird der Name `ansiblehost` verwendet.
   - **Benutzername**: Geben Sie den Benutzernamen ein, der Zugriff auf die Ansible-Instanz haben soll. Zu Demonstrationszwecken wird der Name `ansibleuser` verwendet.
   - **Authentifizierungstyp**: Wählen Sie entweder **Kennwort** oder **Öffentlicher SSH-Schlüssel** aus. Zu Demonstrationszwecken wird **Öffentlicher SSH-Schlüssel** ausgewählt.
   - **Kennwort** und **Kennwort bestätigen**: Falls Sie unter **Authentifizierungstyp** die Option **Kennwort** ausgewählt haben, geben Sie hier jeweils Ihr Kennwort ein.
   - **Öffentlicher SSH-Schlüssel**: Falls Sie unter **Authentifizierungstyp** die Option **Öffentlicher SSH-Schlüssel** ausgewählt haben, geben Sie hier Ihren öffentlichen RSA-Schlüssel als einzelne Zeile ein (beginnend mit `ssh-rsa`).
   - **Abonnement**: Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe an. Zu Demonstrationszwecken wird eine neue Ressourcengruppe namens `ansiblerg` verwendet.
   - **Standort**: Wählen Sie in der Dropdownliste einen für Ihr Szenario geeigneten Standort aus.

     ![Registerkarte mit Ansible-Grundeinstellungen im Azure-Portal](./media/solution-template-deploy/portal-ansible-setup-tab-1.png)

1. Klicken Sie auf **OK**.

1. Geben Sie auf der Registerkarte **Zusätzliche Einstellungen** die erforderlichen Informationen ein:

   - **Größe**: Im Azure-Portal wird eine Standardgröße verwendet. Falls Sie für Ihr spezifisches Szenario eine andere Größe angeben möchten, wählen Sie den Pfeil aus, um eine Liste mit verschiedenen Größen anzuzeigen.
   - **VM-Datenträgertyp**: Wählen Sie entweder **SSD** (Premium-Solid State Drive) oder **HDD** (Festplattenlaufwerk) aus. Zu Demonstrationszwecken wird die leistungsfähigere Option **SSD** ausgewählt. Weitere Informationen zu diesen Datenträgertypen finden Sie in den folgenden Artikeln:
       - [Storage Premium-Hochleistungsspeicher und verwaltete Datenträger für VMs](/azure/virtual-machines/windows/premium-storage)
       - [Verwaltete Standard-SSD-Datenträger für Workloads virtueller Azure-Computer](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Öffentliche IP-Adresse**: Geben Sie diese Einstellung an, wenn Sie von außerhalb des virtuellen Computers mit dem virtuellen Computer kommunizieren möchten. Standardmäßig wird eine neue öffentliche IP-Adresse namens `ansible-pip` verwendet. Wenn Sie eine andere IP-Adresse angeben möchten, wählen Sie den Pfeil aus, und geben Sie Attribute wie Name, SKU und Zuweisung für diese IP-Adresse an. 
   - **Domänennamenbezeichnung**: Geben Sie den öffentlichen Domänennamen des virtuellen Computers ein. Der Name muss eindeutig sein und die Benennungsanforderungen erfüllen. Weitere Informationen zum Angeben eines Namens für den virtuellen Computer finden Sie unter [Namenskonventionen für Azure-Ressourcen](/azure/architecture/best-practices/resource-naming).
   - **Ansible-Version**: Geben Sie entweder eine Versionsnummer oder den Wert `latest` an, um die neueste Version bereitzustellen. Wählen Sie das Informationssymbol neben **Ansible-Version** aus, um weitere Informationen zu verfügbaren Versionen zu erhalten.

     ![Registerkarte mit zusätzlichen Ansible-Einstellungen im Azure-Portal](./media/solution-template-deploy/portal-ansible-setup-tab-2.png)

1. Klicken Sie auf **OK**.

1. Geben Sie auf der Registerkarte mit den **Integrationseinstellungen für Ansible**den Authentifizierungstyp an. Weitere Informationen zum Schutz von Azure-Ressourcen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Registerkarte mit Ansible-Integrationseinstellungen im Azure-Portal](./media/solution-template-deploy/portal-ansible-setup-tab-3.png)

1. Klicken Sie auf **OK**.

1. Daraufhin wird die Seite **Zusammenfassung** angezeigt. Diese enthält den Überprüfungsprozess und eine Liste mit den angegebenen Kriterien für die Ansible-Bereitstellung. Über den Link am unteren Rand der Registerkarte können Sie**die Vorlage und die Parameter für die Verwendung** mit unterstützten Azure-Sprachen und -Plattformen herunterladen. 

     ![Registerkarte mit der Ansible-Zusammenfassung im Azure-Portal](./media/solution-template-deploy/portal-ansible-setup-tab-4.png)

1. Klicken Sie auf **OK**.

1. Wenn die Registerkarte **Erstellen** angezeigt wird, wählen Sie **OK** aus, um Ansible bereitzustellen.

1. Wählen Sie am oberen Rand der Portalseite das **Benachrichtigungssymbol**aus, um die Ansible-Bereitstellung nachzuverfolgen. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressourcengruppe wechseln** aus. 

     ![Registerkarte mit der Ansible-Zusammenfassung im Azure-Portal](./media/solution-template-deploy/portal-ansible-setup-complete.png)

1. Verwenden Sie die auf der Ressourcengruppenseite angegebene IP-Adresse Ihres Ansible-Hosts, um sich anzumelden und Ihre Azure-Ressourcen mit Ansible zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Schnellstart: Konfigurieren eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./vm-configure.md)
