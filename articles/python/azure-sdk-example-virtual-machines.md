---
title: Bereitstellen eines virtuellen Computers mithilfe des Azure SDK für Python
description: So stellen Sie einen virtuellen Azure-Computer mittels Python und den Azure SDK-Verwaltungsbibliotheken bereit.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 816419d99647ed23173d6c9ff707c256b384968f
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404983"
---
# <a name="example-use-the-azure-sdk-to-provision-a-virtual-machine"></a>Beispiel: Verwenden des Azure SDK zum Bereitstellen eines virtuellen Computers

In diesem Beispiel wird veranschaulicht, wie die Azure SDK-Verwaltungsbibliotheken in einem Python-Skript verwendet werden, um eine Ressourcengruppe zu erstellen, die einen virtuellen Linux-Computer enthält.

In diesem Beispiel gibt es keine Clientbibliotheken, da virtuelle Computer nur über eine Verwaltungsschnittstelle verfügen.

Alle Befehle in diesem Artikel funktionieren in Linux-/Mac OS-bash- und Windows-Befehlsshells identisch, sofern nicht anders angegeben.

> [!NOTE]
> Das Bereitstellen eines virtuellen Computers mittels Code ist ein mehrstufiger Prozess, bei dem eine Reihe anderer Ressourcen bereitgestellt wird, die für den virtuellen Computer erforderlich sind. Wenn Sie solchen Code einfach über die Befehlszeile ausführen, ist es wesentlich einfacher, den Befehl [`az vm create`](/cli/azure/vm?view=azure-cli-latest#az-vm-create) zu verwenden, der diese sekundären Ressourcen automatisch mit Standardwerten für jede Einstellung bereitstellt, die Sie auslassen. Die einzigen erforderlichen Argumente sind eine Ressourcengruppe, ein VM-Name, ein Imagename und Anmeldeinformationen. Weitere Informationen finden Sie unter [Schnelles Erstellen einer VM mit der Azure CLI](/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm-quick-create).

## <a name="1-set-up-your-local-development-environment"></a>1: Einrichten Ihrer lokalen Entwicklungsumgebung

Wenn Sie dies noch nicht getan haben, befolgen Sie sämtliche der Anweisungen unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md).

Stellen Sie sicher, dass Sie einen Dienstprinzipal für die lokale Entwicklung erstellen und eine virtuelle Umgebung für dieses Projekt erstellen und aktivieren.

## <a name="2-install-the-needed-sdk-libraries"></a>2: Benötigte SDK-Bibliotheken installieren

1. Erstellen Sie eine Datei *requirements.txt*, in der die in diesem Beispiel verwendeten Verwaltungsbibliotheken aufgeführt sind:

    ```txt
    azure-mgmt-resource
    azure-mgmt-network
    azure-mgmt-compute
    azure-core-cli
    ```

1. Installieren Sie in Ihrem Terminal oder in der Eingabeaufforderung bei aktivierter virtueller Umgebung die in *requirements.txt* aufgeführten Verwaltungsbibliotheken:

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-a-virtual-machine"></a>3: Code zum Bereitstellen eines virtuellen Computers schreiben

Erstellen Sie eine Python-Datei mit dem Namen *provision_vm.py* und dem folgenden Code. Die Details werden in den Kommentaren erläutert:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute import ComputeManagementClient

print(f"Provisioning a virtual machine...some operations might take a minute or two.")

# Step 1: Provision a resource group

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonSDKExample-VM-rg"
LOCATION = "centralus"

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    {
        "location": LOCATION
    }
)


print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: provision a virtual network

# A virtual machine requires a network interface client (NIC). A NIC requires
# a virtual network and subnet along with an IP address. Therefore we must provision
# these downstream components first, then provision the NIC, after which we
# can provision the VM.

# Network and IP address names
VNET_NAME = "python-example-vnet"
SUBNET_NAME = "python-example-subnet"
IP_NAME = "python-example-ip"
IP_CONFIG_NAME = "python-example-ip-config"
NIC_NAME = "python-example-nic"

# Obtain the management object for networks
network_client = get_client_from_cli_profile(NetworkManagementClient)

# Provision the virtual network and wait for completion
poller = network_client.virtual_networks.create_or_update(RESOURCE_GROUP_NAME,
    VNET_NAME,
    {
        "location": LOCATION,
        "address_space": {
            "address_prefixes": ["10.0.0.0/16"]
        }
    }
)

vnet_result = poller.result()

print(f"Provisioned virtual network {vnet_result.name} with address prefixes {vnet_result.address_space.address_prefixes}")

# Step 3: Provision the subnet and wait for completion
poller = network_client.subnets.create_or_update(RESOURCE_GROUP_NAME, 
    VNET_NAME, SUBNET_NAME,
    { "address_prefix": "10.0.0.0/24" }
)
subnet_result = poller.result()

print(f"Provisioned virtual subnet {subnet_result.name} with address prefix {subnet_result.address_prefix}")

# Step 4: Provision an IP address and wait for completion
poller = network_client.public_ip_addresses.create_or_update(RESOURCE_GROUP_NAME,
    IP_NAME,
    {
        "location": LOCATION,
        "sku": { "name": "Standard" },
        "public_ip_allocation_method": "Static",
        "public_ip_address_version" : "IPV4"
    }
)

ip_address_result = poller.result()

print(f"Provisioned public IP address {ip_address_result.name} with address {ip_address_result.ip_address}")

# Step 5: Provision the network interface client
poller = network_client.network_interfaces.create_or_update(RESOURCE_GROUP_NAME,
    NIC_NAME, 
    {
        "location": LOCATION,
        "ip_configurations": [ {
            "name": IP_CONFIG_NAME,
            "subnet": { "id": subnet_result.id },
            "public_ip_address": {"id": ip_address_result.id }
        }]
    }
)

nic_result = poller.result()

print(f"Provisioned network interface client {nic_result.name}")

# Step 6: Provision the virtual machine

# Obtain the management object for virtual machines
compute_client = get_client_from_cli_profile(ComputeManagementClient)

VM_NAME = "ExampleVM"
USERNAME = "azureuser"
PASSWORD = "ChangePa$$w0rd24"

print(f"Provisioning virtual machine {VM_NAME}; this operation might take a few minutes.")

# Provision the VM specifying only minimal arguments, which defaults to an Ubuntu 18.04 VM
# on a Standard DS1 v2 plan with a public IP address and a default virtual network/subnet.

poller = compute_client.virtual_machines.create_or_update(RESOURCE_GROUP_NAME, VM_NAME,
    {
        "location": LOCATION,
        "storage_profile": {
            "image_reference": {
                "publisher": 'Canonical',
                "offer": "UbuntuServer",
                "sku": "16.04.0-LTS",
                "version": "latest"
            }
        },
        "hardware_profile": {
            "vm_size": "Standard_DS1_v2"
        },
        "os_profile": {
            "computer_name": VM_NAME,
            "admin_username": USERNAME,
            "admin_password": PASSWORD
        },
        "network_profile": {
            "network_interfaces": [{
                "id": nic_result.id,
            }]
        }
    }
)

vm_result = poller.result()

print(f"Provisioned virtual machine {vm_result.name}")
```

Dieser Code verwendet die CLI-basierten Authentifizierungsmethoden (`get_client_from_cli_profile`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden (z. B. zum Automatisieren der VM-Verwaltung), verwenden Sie `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md) beschrieben.

## <a name="4-run-the-script"></a>4. Führen Sie das Skript aus.

```bash
python provision_vm.py
```

Der Bereitstellungsprozess dauert einige Minuten.

## <a name="5-verify-the-resources"></a>5. Ressourcen überprüfen

Öffnen Sie das [Azure-Portal](https://portal.azure.com), navigieren Sie zur Ressourcengruppe „PythonSDKExample-VM-rg“, und notieren Sie sich den virtuellen Computer, den virtuellen Datenträger, die Netzwerksicherheitsgruppe, die öffentliche IP-Adresse, die Netzwerkschnittstelle und das virtuelle Netzwerk:

![Die Azure-Portal Seite für die neue Ressourcengruppe mit dem virtuellen Computer und den zugehörigen Ressourcen](media/azure-sdk-example-virtual-machines/portal-vm-resources.png)

### <a name="for-reference-equivalent-azure-cli-commands"></a>Zur Referenz: äquivalente Azure CLI-Befehle

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet \
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet \
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip \
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet \
    --subnet python-example-subnet -n python-example-nic \
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" \
    --nics python-example-nic --image UbuntuLTS \
    --admin-username azureuser --admin-password ChangePa$$w0rd24

```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonSDKExample-VM-rg -n python-example-vnet ^
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet ^
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonSDKExample-VM-rg -n python-example-ip ^
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonSDKExample-VM-rg --vnet-name python-example-vnet ^
    --subnet python-example-subnet -n python-example-nic ^
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonSDKExample-VM-rg -n ExampleVM -l "centralus" ^
    --nics python-example-nic --image UbuntuLTS ^
    --admin-username azureuser --admin-password ChangePa$$w0rd24
```

---

## <a name="6-clean-up-resources"></a>6: Bereinigen von Ressourcen

```azurecli
az group delete -n PythonSDKExample-VM-rg
```

Führen Sie diesen Befehl aus, wenn Sie die in diesem Beispiel erstellten Ressourcen nicht behalten müssen, und Sie in Ihrem Abonnement laufende Gebühren vermeiden möchten.

## <a name="see-also"></a>Weitere Informationen

Die folgenden Ressourcen enthalten weitere umfassende Beispiele für die Verwendung von Python zum Erstellen eines virtuellen Computers:

- [Erstellen und Verwalten von Windows-VMs in Azure mithilfe von Python](/azure/virtual-machines/windows/python). Sie können dieses Beispiel verwenden, um virtuelle Linux-Computer zu erstellen, indem Sie den `storage_profile`-Parameter ändern.
- [Beispiele für die Verwaltung von Azure Virtual Machines – Python](https://github.com/Azure-Samples/virtual-machines-python-manage) (GitHub). Das Beispiel veranschaulicht zusätzliche Verwaltungsvorgänge wie das Starten und Neustarten eines virtuellen Computers, das Beenden und Löschen eines virtuellen Computers, das Erhöhen der Datenträgergröße sowie das Verwalten von Datenträgern.
