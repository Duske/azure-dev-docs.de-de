---
title: 'Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Linux-Computer in Ansible'
description: Erfahren Sie, wie Sie Ansible zum Konfigurieren der VM-Sicherheit mithilfe von Azure Key Vault verwenden können
keywords: Ansible, Azure, DevOps, Schlüsseltresor, Sicherheit, Anmeldeinformationen, Geheimnisse, Schlüssel, Zertifikate, Ansible-Module für Azure, Ressourcengruppe, azure_rm_resourcegroup,
ms.topic: tutorial
ms.date: 04/20/2020
ms.openlocfilehash: 84ed514e742c8a8fa3a9acc9328fc71743dfc5cb
ms.sourcegitcommit: 79890367158a9931909f11da1c894daa11188cba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84145968"
---
# <a name="tutorial-use-azure-key-vault-with-a-linux-virtual-machine-in-ansible"></a>Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Linux-Computer in Ansible

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

In diesem Tutorial wird gezeigt, wie Sie die Ansible-Sammlung für Azure-Module zusammen mit [Azure Key Vault](/azure/key-vault/general/overview) verwenden. Azure Key Vault ermöglicht es Ihnen, die Speicherung von Anmeldeinformationen zu zentralisieren, etwa für Anwendungsgeheimnisse, Schlüssel und Zertifikate. Die Entkopplung von Anmeldeinformationen und Anwendungscode verbessert die Sicherheit Ihres Systems. Außerdem wird die Implementierung eines rollierenden Musters für die Verwaltung von Anwendungsmustern mit automatisch eingerichteten Ablaufdaten viel einfacher.

> [!div class="checklist"]
>
> * Verwenden der Azure-Befehlszeilenschnittstelle zum Abrufen von Werten für Azure-Abonnements und Dienstprinzipale
> * Speichern von Schlüsselwerten als Linux-Umgebungsvariablen
> * Abrufen von Linux-Umgebungsvariablen aus einem Ansible-Playbook
> * Erstellen eines Schlüsseltresors
> * Festlegen einer Zugriffsrichtlinie für einen Schlüsseltresor
> * Verwenden des Azure-Portals zum Hinzufügen einer Zugriffsrichtlinie zu einem Schlüsseltresor
> * Erstellen eines Geheimnisses im Schlüsseltresor
> * Verwenden des Ansible-Shellmoduls zum Abrufen eines Geheimnisses eines Schlüsseltresors
> * Erstellen eines virtuellen Computers zusammen mit allen seinen konstituierenden Komponenten

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
[!INCLUDE [ansible-configure-azure-collection.md](includes/ansible-configure-azure-collection.md)]
    
## <a name="get-azure-subscription-info"></a>Abrufen von Azure-Abonnementinformationen

Verwenden Sie die Azure-Befehlszeilenschnittstelle, um die erforderlichen Azure-Abonnementinformationen abzurufen, die zum Verwenden der Ansible-Module für Azure erforderlich sind. 

1. Rufen Sie die Azure-Abonnement-ID und die ID des Azure-Abonnementmandanten mithilfe des Befehls `az account show` ab. Geben Sie für den `<Subscription>`-Platzhalter entweder den Namen des Azure-Abonnements oder die Azure-Abonnement-ID an. Der Befehl zeigt viele der Schlüsselwerte an, die dem Azure-Standardabonnement zugeordnet sind. Wenn Sie über mehrere Abonnements verfügen, müssen Sie möglicherweise das aktuelle Abonnement mithilfe des Befehls [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) festlegen. Notieren Sie sich sowohl den **ID**- als auch den **tenantID**-Wert aus der Ausgabe des Befehls.

    ```azurecli
    az account show --subscription "<Subscription>" --query tenantId
    ```

1. Wenn Sie nicht über einen Dienstprinzipal für das Azure-Abonnement verfügen. [Erstellen eines Azure-Dienstprinzipals mit der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Notieren Sie sich den Wert von **appid** aus der Befehlsausgabe.

1. Rufen Sie mit dem Befehl `az ad sp show` die Objekt-ID des Dienstprinzipals ab. Geben Sie für den `<ApplicationID>`-Platzhalter die appId des Dienstprinzipals an. Der `--query`-Parameter gibt an, welcher Wert an *stdout* gedruckt werden soll. In diesem Fall handelt es sich um die Objekt-ID des Dienstprinzipals.

    ```azurecli
    az ad sp show --id <ApplicationID> --query objectId
    ```
    
1. Speichern Sie die folgenden Werte als Umgebungsvariablen: Azure-Abonnement-ID, Azure-Abonnementmandanten-ID und Objekt-ID des Dienstprinzipals. Wie Sie das Playbook ausführen, wo Sie die Werte für Abonnement und Anmeldeinformationen speichern und wie Sie diese Werte abrufen, hängt von Ihrer spezifischen Umgebung ab. Für die Zwecke dieser Demo habe ich Azure Cloud Shell verwendet und die erforderlichen Azure-Werte in `~/.bashrc` gespeichert, indem ich am Ende der Datei die folgenden Zeilen hinzugefügt habe:

    ```bash
    export AZ_SUBSCRIPTION_ID="<subscriptionID>"
    export AZ_TENANT_ID="<tenantID>"
    export AZ_OBJECT_ID="<objectID>"
    export AZ_CLIENT_ID="<appID>"
    ```

## <a name="declare-the-azure-collection"></a>Deklarieren der Azure-Sammlung

Nach dem [Herunterladen der aktuellsten Azure-Sammlung](#prerequisites) geben Sie ihre Verwendung mithilfe des `collections`-Schlüssels an.

```yml
- hosts: all
  collections:
    - azure.azcollection
```

## <a name="create-azure-resource-group-for-the-key-vault"></a>Erstellen von Azure-Ressourcengruppen für den Schlüsseltresor

Der folgende Codeausschnitt aus einem Playbook erstellt eine eindeutig benannte Ressourcengruppe, in der der Schlüsseltresor erstellt wird. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"

    - name: Create a resource group to hold the key vault
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"
```

**Hinweise:**

- In dieser Demo wird der Schlüsseltresor als einzige Ressource in einer Ressourcengruppe erstellt. Es ist üblich, den Schlüsseltresor von den Ressourcen zu trennen, die ihn verwenden. Dieses Muster hilft dabei, beim Löschen anderer Ressourcen die versehentliche Löschung des Schlüsseltresors zu verhindern.
- Da der Name des Schlüsseltresors in Azure eindeutig sein muss, erstellt die Demo einen zufälligen *postfix*-Wert. Dieser Wert wird an den Namen der Schlüsseltresor-Ressourcengruppe und des Schlüsseltresors (die im nächsten Abschnitt erstellt werden) angefügt. Der Code in der Aufgabe `Prepare random postfix` generiert den zufälligen Postfix-Wert, der der `rpfx`-Variablen zugewiesen wird.
- In der Aufgabe `Set facts` wird der `lookup`-Befehl verwendet, um die Azure-Abonnement-ID abzurufen, die als Umgebungsvariable gespeichert ist.
- Das [azure_rm_resourcegroup-Modul](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html) wird verwendet, um die neue Ressourcengruppe zu erstellen.
- Eine `debug`-Aufgabe am Ende des Playbooks zeigt den Namen der neuen Ressourcengruppe an.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Wie bereits im vorherigen Abschnitt erwähnt, muss der Name des Schlüsseltresors in Azure eindeutig sein. Daher weist der folgende Playbook-Codeausschnitt der Variablen `kv` die Verkettung der literalen Werte `kv` und `rpfx` zu.

```yml
vars:
  kv: "kv{{ rpfx }}"
  kv_rg: "kv_rg_{{ rpfx }}"

tasks:
  - name: Set facts
    set_fact:
      az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
      az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"

  - name: Create a key vault
    azure_rm_keyvault:
      subscription_id: "{{ az_sub_id }}"
      resource_group: "{{ kv_rg }}"
      vault_name: "{{ kv }}"
      vault_tenant: "{{ az_tenant_id }}"
      enabled_for_deployment: yes
      sku:
        name: standard
        family: A
      access_policies:
        - object_id: "{{ az_object_id }}"
          tenant_id: "{{ az_tenant_id }}"
          secrets:
            - get
            - list
            - set
  - debug:
      msg: "New key vault name = {{ kv }} within the {{ kv_rg }} resource group"

```

**Hinweise:**

- Das [azure_rm_keyvault-Modul](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvault_module.html) wird zum Erstellen des Schlüsseltresors verwendet.
- Beim Erstellen der Zugriffsrichtlinie als Teil des Schlüsseltresors wurden eine Objekt-ID und eine Mandanten-ID angegeben. Diese Werte definieren eine Zugriffsrichtlinie für einen bestimmten Dienstprinzipal (der einem Azure-Abonnement zugeordnet ist). Wenn Sie jedoch im Azure-Portal suchen und versuchen, die Geheimnisse des Schlüsseltresors anzuzeigen, sehen Sie sich vielleicht einer Reihe von Fehlermeldungen gegenüber. Diese Meldungen können etwa „Der Vorgang ‚Auflisten‘ ist in der Zugriffsrichtlinie dieses Schlüsseltresors nicht aktiviert“ oder „Sie sind nicht zum Anzeigen dieser Inhalte berechtigt“ lauten. Wenn Sie diese Nachrichten empfangen, bedeutet das, dass Sie als Active Directory-Benutzer keinen Zugriff besitzen. Im nächsten Abschnitt erfahren Sie, wie Sie dem Schlüsseltresor eine Zugriffsrichtlinie für sich hinzufügen.
- Eine `debug`-Aufgabe am Ende des Playbooks zeigt den Namen des neuen Schlüsseltresors an.

## <a name="add-yourself-to-key-vault-access-policy"></a>Hinzufügen der eigenen Person zu einer Schlüsseltresor-Zugriffsrichtlinie

Wenn Sie die Geheimnisse des Schlüsseltresors anzeigen möchten oder sich durch die Schritte der Demo arbeiten, müssen Sie eine Zugriffsrichtlinie für Ihre Azure Active Directory-ID hinzufügen. Die folgenden Schritte leiten Sie durch das Hinzufügen einer Zugriffsrichtlinie für Sie selbst als Benutzer zum Schlüsseltresor:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Geben Sie im Hauptsuchfeld der Seite `key vaults` ein, und wählen Sie unter **Dienste** **Schlüsseltresore** aus.

1. Wählen Sie den im vorherigen Abschnitt erstellten Schlüsseltresor aus. (Der Name wurde vom Playbook an stdout ausgegeben.)

1. Klicken Sie auf **Zugriffsrichtlinien**.

1. Es wird eine einzelne Zugriffsrichtlinie mit Ihrer Azure Active Directory-ID angezeigt, die den von Ihnen angegebenen Dienstprinzipal darstellt.

1. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.

1. Wählen Sie **Prinzipal auswählen** aus.

1. Geben Sie auf der Registerkarte **Prinzipal** im Suchfeld Ihre E-Mail-Adresse ein.

1. Wählen Sie in der gefilterten Liste den entsprechenden Eintrag aus.

1. Die Informationen für den ausgewählten Benutzer werden in die Liste **Ausgewähltes Mitglied** kopiert. Wählen Sie **Auswählen**.

1. Wählen Sie die passenden Optionen für **Schlüsselberechtigungen**, **Geheimnisberechtigungen** und **Zertifikatberechtigungen** aus. Für die Zwecke dieser Demo ist es ausreichend, **Geheimnisberechtigungen** und dann **Get** (Abrufen), **List** (Auflisten) und **Set** (Festlegen) auszuwählen.

1. Wählen Sie **Hinzufügen**.

1. Die neue Zugriffsrichtlinie für den ausgewählten Benutzer wird jetzt auf der Seite **Zugriffsrichtlinien** angezeigt.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie die **Benachrichtigungen** in der oberen rechten Ecke des Portals aus. Warten Sie, bis die Zugriffsrichtlinie aktualisiert wurde, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="create-a-key-vault-secret"></a>Erstellen eines Geheimnisses im Schlüsseltresor

Der folgende Codeausschnitt aus einem Ansible-Playbook zeigt das Erstellen eines Geheimnisses im Schlüsseltresor:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

  tasks:
    - name: Set facts
      set_fact:
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"
```

**Hinweise:**

- Das [azure_rm_keyvaultsecret-Modul](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvaultsecret_module.html) wird verwendet, um das Geheimnis des Schlüsseltresors zu erstellen.
- Aus Gründen der Einfachheit beinhaltet die Demo den `secret_name` sowie den `secret_value`. Playbooks stellen allerdings IaC-Dateien (Infrastructure-as-Code) dar, genau wie jeder andere Quellcode für Ihr Projekt. Daher sollten Werte wie diese für den Einsatz in Produktionsumgebungen niemals in unverschlüsselten Textdateien gespeichert werden.
- Nach der Ausführung dieses Codes wird das neu erstellte Geheimnis mit dem Namen `testsecret` auf der Registerkarte **Geheimnisse** des Schlüsseltresors aufgelistet. Um es anzuzeigen, wählen Sie das Geheimnis, die aktuelle Version und dann **Geheimniswert anzeigen** aus.

## <a name="get-a-key-vault-secret"></a>Abrufen eines Geheimnisses für einen Schlüsseltresor

Der folgende Codeausschnitt aus einem Ansible-Playbook zeigt, wie die letzte Version des Geheimnisses eines Schlüsseltresors abgerufen wird:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

tasks:
    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']
```

**Hinweise:**

- Das **azure_rm_keyvaultsecret_info-Modul** wird verwendet, um das Geheimnis des Schlüsseltresors abzurufen. Dieses Modul ist nur verfügbar, wenn die Ansible-Sammlung für Azure-Module verwendet wird. 
- Wenn Sie beim Ausführen dieses Codeausschnitts einen Fehler empfangen, vergewissern Sie sich, dass Sie alle Anweisungen im Abschnitt [Voraussetzungen](#prerequisites) erfüllt haben.
- Aus Gründen der Einfachheit beinhaltet die Demo den `secret_name` sowie den `secret_value`. Playbooks stellen allerdings IaC-Dateien (Infrastructure-as-Code) dar, genau wie jeder andere Quellcode für Ihr Projekt. Daher sollten Werte wie diese für den Einsatz in Produktionsumgebungen niemals in unverschlüsselten Textdateien gespeichert werden.

## <a name="run-the-complete-playbook"></a>Ausführen des vollständigen Playbooks

Nach der Einrichtung des Schlüsseltresors und seines Geheimnisses können Sie diese Informationen verwenden, um Azure-Ressourcen wie virtuelle Computer zu schützen. Das folgende Ansible-Playbook führt die im Verlauf dieses Tutorials gezeigten Aufgaben aus und erstellt einen vollständigen virtuellen Computer. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  collections:
    - azure.azcollection
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus
    kv: "kv{{ rpfx }}"
    kv_uri: "https://{{ kv }}.vault.azure.net"
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

    # Test VM vars
    test_vm_rg: kv_test_vm_rg
    test_vm_rg_loc: eastus
    test_vm: kvtestvm
    test_vm_vnet: "kv_test_vm_vnet"
    test_vm_subnet: kv_test_vm_subnet
    test_vm_public_ip: kv_test_vm_public_ip
    test_vm_nsg: kv_test_vm_nsg
    test_vm_nsg_list: 
      - name: Allow-SSH
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 300
        port: 22 
        source_address_prefix: Internet
      - name: Allow-HTTP
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 100
        port: 80
        source_address_prefix: Internet 
    test_vm_nic: kv_test_vnic
    admin_username: testadmin

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"
        az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
        az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a resource group to hold the Key Vault instance
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"

    - name: Create instance of Key Vault
      azure_rm_keyvault:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ kv_rg }}"
        vault_name: "{{ kv }}"
        vault_tenant: "{{ az_tenant_id }}"
        enabled_for_deployment: yes
        sku:
          name: standard
          family: A
        access_policies:
          - object_id: "{{ az_object_id }}"
            tenant_id: "{{ az_tenant_id }}"
            secrets:
              - get
              - list
              - set

    - debug:
        msg: "New Key Vault instance name = {{ kv }} within the {{ kv_rg }} resource group"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"

    - name: Register Key Vault provider.
      shell:
        az provider register -n Microsoft.KeyVault

    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']

    - name: Create resource group for test VM.
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ test_vm_rg }}"
        location: "{{ test_vm_rg_loc }}"

    - name: Create virtual network.
      azure_rm_virtualnetwork:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_vnet }}"
        address_prefixes: "172.16.0.0/16"

    - name: Create subset within virtual network.
      azure_rm_subnet:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        virtual_network_name: "{{ test_vm_vnet }}"
        name: "{{ test_vm_subnet }}"
        address_prefix_cidr:  "172.16.10.0/24"

    - name: Create public IP address.
      azure_rm_publicipaddress:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        allocation_method: Static
        name: "{{ test_vm_public_ip }}"

    - name: Create Network Security Group and rules.
      azure_rm_securitygroup:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nsg}}"
        rules:
          - name: "{{ item.name }}"
            access: "{{ item.access }}"
            protocol: "{{ item.protocol }}"
            direction: "{{ item.direction }}"
            destination_port_range: "{{ item.port }}"
            priority: "{{ item.priority }}"
            source_address_prefix: "{{ item.source_address_prefix }}"
      loop: "{{ test_vm_nsg_list }}"

    - name: Create virtual network interface card (NIC).
      azure_rm_networkinterface:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nic }}"
        virtual_network: "{{ test_vm_vnet }}"
        subnet: "{{ test_vm_subnet }}"
        ip_configurations:
          - name: ipconfig
            public_ip_address_name: "{{ test_vm_public_ip }}"
            primary: yes
        security_group: "{{ test_vm_nsg }}"

    - name: Create virtual machine.
      azure_rm_virtualmachine:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm }}"
        admin_username: " {{ admin_username }} "
        admin_password: " {{ output['secrets'][0]['secret'] }}"
        vm_size: Standard_B1ms
        network_interfaces: "{{ test_vm_nic }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest

```

**Hinweise:**

- Das *Administratorkennwort* für den virtuellen Computer wird auf das Geheimnis des Schlüsseltresors festgelegt.
- Die Möglichkeit, das gesamte Playbook in einem Durchgang auszuführen, hängt von Ihrer Testumgebung ab. Möglicherweise müssen Sie sich der Zugriffsrichtlinie des Schlüsseltresors manuell hinzufügen, bevor Sie den Schlüssel erstellen. Diese Aufgabe wird in den Abschnitten [Erstellen eines Schlüsseltresors](#create-a-key-vault) und [Hinzufügen der eigenen Person zu einer Schlüsseltresor-Zugriffsrichtlinie](#add-yourself-to-key-vault-access-policy) erläutert.
- Wie Sie sehen können, werden viele verschiedene Ansible-Module verwendet, um einen virtuellen Azure-Computer und alle seine konstituierenden Komponenten zu erstellen. Weitere Informationen zu den verschiedenen Ansible-Modulen, die zum Erstellen eines virtuellen Computers verwendet werden, finden Sie in der folgenden Liste:
    - [Azure-Ressourcengruppenmodul (azure_rm_resourcegroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html)
    - [Azure Virtual Network-Modul (azure_rm_virtualnetwork)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualnetwork_module.html)
    - [Azure Virtual Network-Subnetzmodul (azure_rm_subnet)](https://docs.ansible.com/ansible/latest/modules/azure_rm_subnet_module.html)
    - [Azure Public IP-Modul (azure_rm_publicipaddress)](https://docs.ansible.com/ansible/latest/modules/azure_rm_publicipaddress_module.html)
    - [Azure-Netzwerksicherheitsgruppen-Modul (azure_rm_securitygroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_securitygroup_module.html)
    - [Azure-Netzwerkschnittstelle (azure_rm_networkinterface)](https://docs.ansible.com/ansible/latest/modules/azure_rm_networkinterface_module.html)
    - [Virtueller Azure-Computer (azure_rm_virtualmachine)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualmachine_module.html)
    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Artikel erstellten Ressourcen, wenn Sie sie nicht mehr benötigen. Ersetzen Sie den `<kv_rg>`-Platzhalter durch die Ressourcengruppe, die für den Schlüsseltresor dieser Demo verwendet wird.

```yml
- hosts: localhost
  vars:
    kv_rg: <kv_rg>
    test_vm_rg: kv_test_vm_rg
  tasks:
    - name: Delete the key vault resource group
      azure_rm_resourcegroup:
        name: "{{ kv_rg }}"
        force_delete_nonempty: yes
        state: absent
    - name: Delete the test vm resource group
      azure_rm_resourcegroup:
        name: "{{ test_vm_rg }}"
        force_delete_nonempty: yes
        state: absent
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Azure Key Vault-Sicherheitsübersicht](/azure/key-vault/general/overview-security)
