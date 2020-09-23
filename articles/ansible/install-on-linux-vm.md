---
title: 'Schnellstart: Konfigurieren von Ansible über die Azure-Befehlszeilenschnittstelle'
description: In diesem Schnellstart erfahren Sie, wie Sie Ansible für die Verwaltung von Azure-Ressourcen unter Ubuntu, CentOS und SLES installieren und konfigurieren.
keywords: Ansible, Azure, DevOps, Bash, Cloud Shell, Playbook, Azure CLI
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-ansible,devx-track-cli
ms.openlocfilehash: 50fbcb4d086679265d728f14061a5c4c649fa48d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682040"
---
# <a name="quickstart-configure-ansible-using-azure-cli"></a>Schnellstart: Konfigurieren von Ansible über die Azure-Befehlszeilenschnittstelle

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie [Ansible](https://docs.ansible.com/) mit der Azure-Befehlszeilenschnittstelle (Azure CLI) installieren.

In diesem Schnellstart führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen eines SSH-Schlüsselpaars
> * Erstellen einer Ressourcengruppe
> * Erstellen eines virtuellen CentOS-Computers 
> * Installieren von Ansible auf dem virtuellen Computer
> * Herstellen einer Verbindung mit dem virtuellen Computer über SSH
> * Konfigurieren von Ansible auf dem virtuellen Computer

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Zugriff auf Linux oder einen virtuellen Linux-Computer:** Falls Sie keinen Linux-Computer besitzen, erstellen Sie einen [virtuellen Linux-Computer](/azure/virtual-network/quick-create-cli).

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Bei der Verbindungsherstellung mit Linux-VMs können Sie die Kennwortauthentifizierung oder die schlüsselbasierte Authentifizierung nutzen. Die schlüsselbasierte Authentifizierung ist sicherer als die Verwendung von Kennwörtern. Daher wird in diesem Artikel die schlüsselbasierte Authentifizierung verwendet.

Bei der schlüsselbasierten Authentifizierung gibt es zwei Schlüssel:

- **Öffentlicher Schlüssel**: Der öffentliche Schlüssel wird auf dem Host gespeichert, z. B. auf Ihrer VM (wie in diesem Artikel).
- **Privater Schlüssel**: Mit dem privaten Schlüssel können Sie eine sichere Verbindung mit Ihrem Host herstellen. Der private Schlüssel ist quasi Ihr Kennwort und sollte entsprechend geschützt werden.
        
Mit den folgenden Schritten wird die Erstellung eines SSH-Schlüsselpaars beschrieben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview), und wechseln Sie, falls noch nicht geschehen, zu **Bash**.

1. Erstellen Sie einen SSH-Schlüssel, indem Sie [ssh-keygen](https://www.ssh.com/ssh/keygen/) verwenden.

    ```bash
    ssh-keygen -m PEM -t rsa -b 2048 -C "azureuser@azure" -f ~/.ssh/ansible_rsa -N ""
    ```

    **Hinweise**:

    - Mit dem Befehl `ssh-keygen` wird der Speicherort der generierten Schlüsseldateien angezeigt. Sie benötigen diesen Verzeichnisnamen beim Erstellen des virtuellen Computers.
    - Der öffentliche Schlüssel wird in `ansible_rsa.pub` und der private Schlüssel in `ansible_rsa` gespeichert.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Möglicherweise müssen Sie den Parameter `--location` durch den entsprechenden Wert für Ihre Umgebung ersetzen.

    ```azurecli
    az group create --name QuickstartAnsible-rg --location eastus
    ```

1. Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer.

    ```azurecli
    az vm create \
    --resource-group QuickstartAnsible-rg \
    --name QuickstartAnsible-vm \
    --image OpenLogic:CentOS:7.7:latest \
    --admin-username azureuser \
    --ssh-key-values <ssh_public_key_filename>
    ```

1. Überprüfen Sie die Erstellung (und den Zustand) des neuen virtuellen Computers mit [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartAnsible-vm']"
    ```

    **Hinweise**:

    - Die Ausgabe des Befehls `az vm list` enthält die öffentliche IP-Adresse, die zum Herstellen einer Verbindung mit dem virtuellen Computer per SSH genutzt wird.

## <a name="install-ansible-on-the-virtual-machine"></a>Installieren von Ansible auf dem virtuellen Computer

Führen Sie das Skript für die Ansible-Installation aus, indem Sie [az vm extension set](/cli/azure/vm/extension?#az-vm-extension-set) verwenden.

```azurecli
az vm extension set \
 --resource-group QuickstartAnsible-rg \
 --vm-name QuickstartAnsible-vm \
 --name customScript \
 --publisher Microsoft.Azure.Extensions \
 --version 2.1 \
 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-ansible-control-machine/master/configure-ansible-centos.sh"]}' \
 --protected-settings '{"commandToExecute": "./configure-ansible-centos.sh"}'
```

**Hinweise:**

- Nach Abschluss des Vorgangs werden mit dem Befehl `az vm extension` die Ergebnisse der Ausführung des Installationsskripts angezeigt.

## <a name="connect-to-your-virtual-machine-via-ssh"></a>Herstellen einer Verbindung mit Ihrem virtuellen Computer per SSH

Verwenden Sie den SSH-Befehl, um eine Verbindung mit Ihrem virtuellen Computer herzustellen.

```azurecli
ssh -i <ssh_private_key_filename> azureuser@<vm_ip_address>
```

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

## <a name="test-ansible-installation"></a>Testen der Ansible-Installation

Sie verfügen nun über einen virtuellen Computer, auf dem Ansible installiert und konfiguriert ist!

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Ansible unter Azure](/azure/developer/Ansible)