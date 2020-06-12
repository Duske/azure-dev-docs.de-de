---
title: Verwendungsmuster mit den Azure-Bibliotheken für Python
description: Hier finden Sie eine Übersicht über gängige Verwendungsmuster mit den Azure SDK-Bibliotheken für Python.
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: f712dc41233b8301e370c9eb63786d8e2d7f8c70
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256275"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Azure-Bibliotheken für Python: Verwendungsmuster

Das Azure SDK für Python besteht ausschließlich aus vielen unabhängigen Bibliotheken. Diese sind auf der [Indexseite des Azure SDK für Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html) aufgeführt.

Alle Bibliotheken verfügen über bestimmte allgemeine Merkmale und Verwendungsmuster. Hierzu zählen beispielsweise die Installation und die Verwendung von JSON-Inline-Code für Objektargumente.

## <a name="library-installation"></a>Bibliotheksinstallation

Verwenden Sie zum Installieren eines bestimmten Bibliothekspakets einfach `pip install`:

```cmd
# Install the management library for Azure Storage
pip install azure-mgmt-storage
```

```cmd
# Install the client library for Azure Storage
pip install azure-storage-blob
```

Von `pip install` wird die neueste Version einer Bibliothek in Ihrer aktuellen Python-Umgebung abgerufen.

`pip` kann auch zum Deinstallieren von Bibliotheken sowie zum Installieren bestimmter Versionen (einschließlich Vorschauversionen) verwendet werden. Weitere Informationen finden Sie unter [Installieren von Azure-Bibliothekspaketen für Python](azure-sdk-install.md).

## <a name="inline-json-pattern-for-object-arguments"></a>JSON-Inline-Muster für Objektargumente

Bei vielen Vorgängen innerhalb der Azure-Bibliotheken können Objektargumente als diskrete Objekte oder als JSON-Inline-Code ausgedrückt werden.

Angenommen, Sie verfügen über ein [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient?view=azure-python)-Objekt, über das Sie eine Ressourcengruppe mit seiner [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-)-Methode erstellen. Das zweite Argument für diese Methode ist vom Typ [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup?view=azure-python).

Um `create_or_update` aufzurufen, können Sie eine diskrete Instanz von `ResourceGroup` direkt mit den erforderlichen Argumenten erstellen (in diesem Fall`location`):

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    ResourceGroup(location="centralus")
)
```

Alternativ können Sie dieselben Parameter als JSON-Inline-Code übergeben:

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    {
      "location": "centralus"
    }
)
```

Bei Verwendung von JSON wird der JSON-Inline-Code durch die Azure-Bibliotheken automatisch in den für das betreffende Argument geeigneten Objekttyp konvertiert.

Objekte können auch geschachtelte Objektargumente besitzen, und in diesem Fall können Sie dann auch geschachtelten JSON-Code verwenden.

Angenommen, Sie haben beispielsweise eine Instanz des [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python)-Objekts und rufen seine [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)-Methode auf. In diesem Fall ist das dritte Argument vom Typ [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), das wiederum selbst ein Argument vom Typ [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python) enthält. `VaultProperties` wiederum enthält Objektargumente vom Typ [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) und [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). Eine `Sku` enthält ein [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python)-Objekt, und jeder `AccessPolicyEntry` enthält ein [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python)-Objekt.

Wenn Sie `create_or_update` mit eingebetteten Objekten aufrufen möchten, müssen Sie Code wie den folgenden verwenden (vorausgesetzt, `tenant_id` und `object_id` sind bereits definiert). Die erforderlichen Objekte können auch vor dem Funktionsaufruf erstellt werden.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    VaultCreateOrUpdateParameters(
        location="centralus",
        properties=VaultProperties(
            tenant_id=tenant_id,
            sku=Sku(name="standard"),
            access_policies=[
                AccessPolicyEntry(
                    tenant_id=tenant_id,
                    object_id=object_id,
                    permissions=Permissions(keys=['all'], secrets=['all'])
                )
            ]
        )
    )
)
```

Derselbe Aufruf unter Verwendung von JSON-Inline-Code sieht wie folgt aus:

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    {
        'location': 'centralus',
        'properties': {
            'sku': {
                'name': 'standard'
            },
            'tenant_id': tenant_id,
            'access_policies': [{
                'object_id': object_id,
                'tenant_id': tenant_id,
                'permissions': {
                    'keys': ['all'],
                    'secrets': ['all']
                }
            }]
        }
    }
)
```

Da beide Formen äquivalent sind, können Sie die von Ihnen bevorzugte Form und sogar eine Kombination verwenden.

Wenn Ihr JSON-Code nicht ordnungsgemäß formatiert ist, erhalten Sie in der Regel den Fehler „DeserializationError: Deserialisierung in Objekt nicht möglich: Typ, AttributeError: ‚str‘-Objekt hat kein Attribut ‚get‘.". Eine häufige Ursache für diesen Fehler ist die Angabe einer einzelnen Zeichenfolge für eine Eigenschaft, obwohl von der Bibliothek ein geschachteltes JSON-Objekt erwartet wird. Wenn Sie z. B. im vorherigen Beispiel `"sku": "standard"` verwenden, generiert dies diesen Fehler, weil der Parameter `sku` ein `Sku`-Objekt ist, das JSON-Inline-Objektcode erwartet, in diesem Fall `{ "name": "standard"}`, der dem erwarteten `SkuName`-Typ entspricht.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den allgemeinen Mustern für die Verwendung der Azure-Bibliotheken für Python vertraut sind, können Sie sich als Nächstes anhand der folgenden eigenständigen Beispiele mit spezifischen Verwaltungs- und Clientbibliothekszenarien beschäftigen:

- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)

Da diese Beispiele weder sequenziell noch voneinander abhängig sind, gibt es keine vorgegebene Reihenfolge.
