---
title: 'Schnellstart: Installieren von Ansible auf virtuellen Linux-Computern in Azure'
description: In diesem Schnellstart erfahren Sie, wie Sie Ansible für die Verwaltung von Azure-Ressourcen unter Ubuntu, CentOS und SLES installieren und konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, CloudShell, Playbook, Bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f577b9841375d63bfc88249da88e554c1464bde
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743590"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Schnellstart: Installieren von Ansible auf virtuellen Linux-Computern in Azure

Ansible ermöglicht die Automatisierung der Bereitstellung und Konfiguration von Ressourcen in Ihrer Umgebung. Dieser Artikel veranschaulicht das Konfigurieren von Ansible für einige der verbreitetsten Linux-Distributionen. Um Ansible auf anderen Distributionen zu installieren, passen Sie die installierten Pakete entsprechend an Ihre Plattform an. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Zugriff auf Linux oder einen virtuellen Linux-Computer:** Falls Sie keinen Linux-Computer besitzen, erstellen Sie einen [virtuellen Linux-Computer](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Installieren von Ansible auf einem virtuellen Azure Linux-Computer

Melden Sie sich beim Linux-Computer an, und wählen Sie eine der folgenden Distributionen aus, um Schritte zum Installieren von Ansible anzuzeigen:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

In diesem Abschnitt konfigurieren Sie CentOS für die Verwendung von Ansible.

1. Öffnen Sie ein Terminalfenster.

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für die Python-SDK-Module für Azure auf dem virtuellen Computer zu installieren:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für Ansible zu installieren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Erstellen Sie die Azure-Anmeldeinformationen](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

In diesem Abschnitt konfigurieren Sie Ubuntu für die Verwendung von Ansible.

1. Öffnen Sie ein Terminalfenster.

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für die Python-SDK-Module für Azure auf dem virtuellen Computer zu installieren:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für Ansible zu installieren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Erstellen Sie die Azure-Anmeldeinformationen](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

In diesem Abschnitt konfigurieren Sie SLES für die Verwendung von Ansible.

1. Öffnen Sie ein Terminalfenster.

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für die Python-SDK-Module für Azure auf dem virtuellen Computer zu installieren:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Geben Sie den folgenden Befehl ein, um die erforderlichen Pakete für Ansible zu installieren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Geben Sie den folgenden Befehl ein, um das Python-Kryptografiepaket zu entfernen, das den Konflikt verursacht:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Erstellen Sie die Azure-Anmeldeinformationen](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Erstellen von Azure-Anmeldeinformationen

Um die Anmeldeinformationen für Ansible zu konfigurieren, benötigen Sie die folgenden Informationen:

* ID Ihres Azure-Abonnements 
* Werte für den Dienstprinzipal

Wenn Sie Ansible Tower oder Jenkins verwenden, deklarieren Sie die Werte für den Dienstprinzipal als Umgebungsvariablen.

Konfigurieren Sie die Ansible-Anmeldeinformationen mithilfe einer der folgenden Methoden:

- [Erstellen einer Datei mit Ansible-Anmeldeinformationen](#file-credentials)
- [Verwenden von Ansible-Umgebungsvariablen](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Erstellen einer Datei mit Ansible-Anmeldeinformationen

In diesem Abschnitt erstellen Sie eine lokale Datei mit Anmeldeinformationen, um Anmeldeinformationen für Ansible bereitzustellen. 

Weitere Informationen zum Definieren von Ansible-Anmeldeinformationen finden Sie unter [Bereitstellen von Anmeldeinformationen für Azure-Module](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Erstellen Sie für eine Entwicklungsumgebung eine Datei mit dem Namen `credentials` auf dem virtuellen Hostcomputer:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Fügen Sie in der Datei die folgenden Zeilen ein. Ersetzen Sie die Platzhalter durch die Werte für den Dienstprinzipal.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Speichern und schließen Sie die Datei.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Verwenden von Ansible-Umgebungsvariablen

In diesem Abschnitt exportieren Sie die Werte für den Dienstprinzipal, um Ihre Ansible-Anmeldeinformationen zu konfigurieren.

1. Öffnen Sie ein Terminalfenster.

1. Exportieren Sie die Werte des Dienstprinzipals:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Überprüfen der Konfiguration

Erstellen Sie mit Ansible eine Azure-Ressourcengruppe, um zu überprüfen, ob die Konfiguration erfolgreich war.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Schnellstart: Konfigurieren eines virtuellen Linux-Computers in Azure mithilfe von Ansible](./vm-configure.md)