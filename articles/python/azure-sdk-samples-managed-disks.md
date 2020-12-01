---
title: Verwenden verwalteter Azure-Datenträger über die Azure-Bibliotheken für Python
description: Hier erfahren Sie, wie Sie das Azure SDK verwenden, um verwaltete Datenträger zu erstellen, zu ändern und zu aktualisieren.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python
ms.openlocfilehash: fe2378bcb836dbfc52ad1d5d3e88f048d6ef117e
ms.sourcegitcommit: b70a38d46616f5e519d5b9c1a1eaf3fe0ecb9605
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932414"
---
# <a name="use-azure-managed-disks-with-the-azure-libraries-sdk-for-python"></a>Verwenden verwalteter Azure-Datenträger mit den Azure-Bibliotheken (SDK) für Python

Verwaltete Azure-Datenträger bieten eine vereinfachte Datenträgerverwaltung, erweiterte Skalierbarkeit, mehr Sicherheit und eine bessere Skalierung, ohne direkt mit Speicherkonten arbeiten zu müssen.

Verwaltete Datenträger werden mithilfe der Bibliothek [`azure-mgmt-compute`](/python/api/overview/azure/virtualmachines) verwaltet. (Ein Beispiel für die Bereitstellung eines virtuellen Computers mit der Bibliothek `azure-mgmt-compute` finden Sie unter [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md).)

## <a name="standalone-managed-disks"></a>Eigenständige verwaltete Datenträger

Eigenständige verwaltete Datenträger können auf verschiedene Arten erstellt werden. Diese werden in den folgenden Abschnitten beschrieben.

### <a name="create-an-empty-managed-disk"></a>Erstellen eines leeren verwalteten Datenträgers

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'disk_size_gb': 20,
        'creation_data': {
            'create_option': DiskCreateOption.empty
        }
    }
)
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-from-blob-storage"></a>Erstellen eines verwalteten Datenträgers über den Blobspeicher

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.import_enum,
            'source_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd'
        }
    }
)
disk_resource = poller.result()
```

### <a name="create-a-managed-disk-image-from-blob-storage"></a>Erstellen eines Images für einen verwalteten Datenträger über den Blobspeicher

```python
from azure.mgmt.compute.models import DiskCreateOption

poller = compute_client.images.begin_create_or_update(
    'my_resource_group',
    'my_image_name',
    {
        'location': 'eastus',
        'storage_profile': {
           'os_disk': {
              'os_type': 'Linux',
              'os_state': "Generalized",
              'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
              'caching': "ReadWrite",
           }
        }
    }
)
image_resource = poller.result()
```

### <a name="create-a-managed-disk-from-your-own-image"></a>Erstellen eines verwalteten Datenträgers anhand eines eigenen Images

```python
from azure.mgmt.compute.models import DiskCreateOption

# If you don't know the id, do a 'get' like this to obtain it
managed_disk = compute_client.disks.get(self.group_name, 'myImageDisk')

poller = compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.copy,
            'source_resource_id': managed_disk.id
        }
    }
)

disk_resource = poller.result()
```

## <a name="virtual-machine-with-managed-disks"></a>Virtuelle Computer mit verwalteten Datenträgern

Sie können einen virtuellen Computer mit einem impliziten verwalteten Datenträger für ein spezifisches Datenträgerimage erstellen. In diesem Fall müssen nicht sämtliche Details angegeben werden.

Ein verwalteter Datenträger wird implizit erstellt, wenn ein virtueller Computer anhand eines Betriebssystemimages in Azure erstellt wird. Im Parameter `storage_profile` ist `os_disk` optional, und die Erstellung eines Speicherkontos ist keine Voraussetzung für die Erstellung eines virtuellen Computers.

```python
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        publisher='Canonical',
        offer='UbuntuServer',
        sku='16.04-LTS',
        version='latest'
    )
)
```

Ein umfassendes Beispiel zum Erstellen eines virtuellen Computers unter Verwendung der Azure-Verwaltungsbibliotheken für Python finden Sie unter [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md).

Sie können `storage_profile` auch mithilfe eines eigenen Images erstellen:

```python
# If you don't know the id, do a 'get' like this to obtain it
image = compute_client.images.get(self.group_name, 'myImageDisk')

storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        id = image.id
    )
)
```

Ein zuvor bereitgestellter verwalteter Datenträger kann ganz einfach angefügt werden:

```python
vm = compute.virtual_machines.get(
    'my_resource_group',
    'my_vm'
)
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')

vm.storage_profile.data_disks.append({
    'lun': 12, # You choose the value, depending of what is available for you
    'name': managed_disk.name,
    'create_option': DiskCreateOptionTypes.attach,
    'managed_disk': {
        'id': managed_disk.id
    }
})

async_update = compute_client.virtual_machines.begin_create_or_update(
    'my_resource_group',
    vm.name,
    vm,
)
async_update.wait()
```

## <a name="virtual-machine-scale-sets-with-managed-disks"></a>VM-Skalierungsgruppen mit Managed Disks

Vor der Einführung von verwalteten Datenträgern mussten Sie manuell ein Speicherkonto für alle virtuellen Computer erstellen, die in der Skalierungsgruppe enthalten sein sollten, und anschließend mit dem Auflistungsparameter `vhd_containers` den vollständigen Speicherkontonamen an die REST-API der Skalierungsgruppe übergeben. (Einen Migrationsleitfaden finden Sie unter [Konvertieren einer Skalierungsgruppenvorlage in eine Skalierungsgruppenvorlage für verwaltete Datenträger](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md).)

Da Sie bei verwalteten Azure-Datenträgern keine Speicherkonten verwalten müssen, können Sie nun genau das gleiche Speicherprofil (`storage_profile`) verwenden wie bei der VM-Erstellung:

```python
'storage_profile': {
    'image_reference': {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
    }
},
```

Das vollständige Beispiel sieht wie folgt aus:

```python
naming_infix = "PyTestInfix"

vmss_parameters = {
    'location': self.region,
    "overprovision": True,
    "upgrade_policy": {
        "mode": "Manual"
    },
    'sku': {
        'name': 'Standard_A1',
        'tier': 'Standard',
        'capacity': 5
    },
    'virtual_machine_profile': {
        'storage_profile': {
            'image_reference': {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "16.04-LTS",
                "version": "latest"
            }
        },
        'os_profile': {
            'computer_name_prefix': naming_infix,
            'admin_username': 'Foo12',
            'admin_password': 'BaR@123!!!!',
        },
        'network_profile': {
            'network_interface_configurations' : [{
                'name': naming_infix + 'nic',
                "primary": True,
                'ip_configurations': [{
                    'name': naming_infix + 'ipconfig',
                    'subnet': {
                        'id': subnet.id
                    }
                }]
            }]
        }
    }
}

# Create VMSS test
result_create = compute_client.virtual_machine_scale_sets.begin_create_or_update(
    'my_resource_group',
    'my_scale_set',
    vmss_parameters,
)
vmss_result = result_create.result()
```

## <a name="other-operations-with-managed-disks"></a>Andere Vorgänge mit Managed Disks

### <a name="resizing-a-managed-disk"></a>Ändern der Größe eines verwalteten Datenträgers

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.disk_size_gb = 25

async_update = self.compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="update-the-storage-account-type-of-the-managed-disks"></a>Aktualisieren des Speicherkontotyps der verwalteten Datenträger

```python
from azure.mgmt.compute.models import StorageAccountTypes

managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.account_type = StorageAccountTypes.standard_lrs

async_update = self.compute_client.disks.begin_create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="create-an-image-from-blob-storage"></a>Erstellen eines Images über den Blobspeicher

```python
async_create_image = compute_client.images.create_or_update(
    'my_resource_group',
    'myImage',
    {
        'location': 'westus',
        'storage_profile': {
            'os_disk': {
                'os_type': 'Linux',
                'os_state': "Generalized",
                'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
                'caching': "ReadWrite",
            }
        }
    }
)
image = async_create_image.result()
```

### <a name="create-a-snapshot-of-a-managed-disk-that-is-currently-attached-to-a-virtual-machine"></a>Erstellen einer Momentaufnahme eines verwalteten Datenträgers, der derzeit an einen virtuellen Computer angefügt ist

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')

async_snapshot_creation = self.compute_client.snapshots.begin_create_or_update(
        'my_resource_group',
        'mySnapshot',
        {
            'location': 'westus',
            'creation_data': {
                'create_option': 'Copy',
                'source_uri': managed_disk.id
            }
        }
    )
snapshot = async_snapshot_creation.result()
```

## <a name="see-also"></a>Weitere Informationen

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)
