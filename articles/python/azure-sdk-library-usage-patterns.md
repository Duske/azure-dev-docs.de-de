---
title: Verwendungsmuster mit den Azure-Bibliotheken für Python
description: Hier finden Sie eine Übersicht über gängige Verwendungsmuster mit den Azure SDK-Bibliotheken für Python.
ms.date: 11/12/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 6f1a2c07bbda4ebe409722d2381e046ee45f7902
ms.sourcegitcommit: 6514a061ba5b8003ce29d67c81a9f0795c3e3e09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94601392"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Azure-Bibliotheken für Python: Verwendungsmuster

Das Azure SDK für Python besteht ausschließlich aus vielen unabhängigen Bibliotheken. Diese werden im [Paketindex für das Python SDK](azure-sdk-library-package-index.md) aufgeführt.

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

## <a name="asynchronous-operations"></a>Asynchrone Vorgänge

Bei vielen Vorgängen, die Sie über Client- und Verwaltungsclientobjekte aufrufen (z. B. [`ComputeManagementClient.virtual_machines.begin_create_or_update`](/python/api/azure-mgmt-compute/azure.mgmt.compute.v2020_06_01.operations.virtualmachinesoperations#begin-create-or-update-resource-group-name--vm-name--parameters----kwargs-) und [`WebSiteManagementClient.web_apps.create_or_update`](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.operations.webappsoperations#create-or-update-resource-group-name--name--site-envelope--custom-headers-none--raw-false--polling-true----operation-config-)), wird ein Objekt vom Typ `AzureOperationPoller[<type>]` zurückgegeben, wobei `<type>` für den betreffenden Vorgang spezifisch ist.

Beide Methoden sind asynchron. Der Unterschied bei den Methodennamen ist auf Versionsunterschiede zurückzuführen. Von älteren Bibliotheken, die nicht auf „azure.core“ basieren, werden in der Regel Namen wie `create_or_update` verwendet. Bei Bibliotheken, die auf „azure.core“ basieren, werden Methodennamen mit dem Präfix `begin_` versehen, um deutlicher zu machen, dass sie asynchron sind. Wenn Sie alten Code zu einer neueren, auf „azure.core“ basierenden Bibliothek migrieren möchten, müssen Sie in der Regel den Methodennamen das Präfix `begin_` hinzufügen.

In beiden Fällen bedeutet der Rückgabetyp [`AzureOperationPoller`](/python/api/msrestazure/msrestazure.azure_operation.azureoperationpoller) definitiv, dass der Vorgang asynchron ist. Dementsprechend muss die Methode `result` dieses Pollers so aufgerufen werden, dass auf den Abschluss des Vorgangs gewartet und dann das entsprechende Ergebnis abgerufen wird.

Der folgende Code stammt aus [Beispiel: Bereitstellen und Implementieren einer Web-App](azure-sdk-example-web-app.md) und zeigt ein Beispiel für die Verwendung des Pollers, damit auf ein Ergebnis gewartet wird:

```python
poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()
```

In diesem Fall ist der Rückgabewert von `create_or_update` vom Typ `AzureOperationPoller[Site]`. Dies bedeutet, dass der Rückgabewert von `poller.result()` ein [Site](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.models.site)-Objekt ist.

## <a name="exceptions"></a>Ausnahmen

Im Allgemeinen werden von den Azure-Bibliotheken Ausnahmen ausgelöst, wenn Vorgänge nicht wie vorgesehen ausgeführt werden, einschließlich fehlerhafter HTTP-Anforderungen an die Azure-REST-API. Für App-Code können Sie dann `try...except`-Blöcke um Bibliotheksvorgänge herum verwenden.

Weitere Informationen zum Typ der Ausnahmen, die ausgelöst werden können, finden Sie in der Dokumentation zum betreffenden Vorgang.

## <a name="logging"></a>Protokollierung

Die neuesten Azure-Bibliotheken verwenden die Python-`logging`-Standardbibliothek, um die Protokollausgabe zu generieren. Sie können den Protokolliergrad für einzelne Bibliotheken, Gruppen von Bibliotheken oder alle Bibliotheken festlegen. Nachdem Sie einen Protokollierungsdatenstrom-Handler registriert haben, können Sie die Protokollierung für ein bestimmtes Clientobjekt oder einen bestimmten Vorgang aktivieren. Weitere Informationen finden Sie unter [Protokollierung in den Azure-Bibliotheken](azure-sdk-logging.md).

## <a name="proxy-configuration"></a>Proxykonfiguration

Zum Angeben eines Proxys können Sie Umgebungsvariablen oder optionale Argumente verwenden. Weitere Informationen finden Sie unter [Konfigurieren von Proxys](azure-sdk-configure-proxy.md).

## <a name="optional-arguments-for-client-objects-and-methods"></a>Optionale Argumente für Clientobjekte und -methoden

In der Bibliotheksreferenzdokumentation tritt häufig ein `**kwargs`- oder `**operation_config**`-Argument in der Signatur eines Clientobjektkonstruktors oder einer bestimmten Vorgangsmethode auf. Diese Platzhalter geben an, dass das betreffende Objekt oder die betreffende Methode zusätzliche benannte Argumente unterstützen kann. In der Regel gibt die Referenzdokumentation die spezifischen Argumente an, die Sie verwenden können. Es gibt auch einige allgemeine Argumente, die häufig unterstützt werden, wie in den folgenden Abschnitten beschrieben.

### <a name="arguments-for-libraries-based-on-azurecore"></a>Argumente für Bibliotheken, die auf azure.core basieren

Diese Argumente gelten für die Bibliotheken, die unter [Python: Neue Bibliotheken](https://azure.github.io/azure-sdk/releases/latest/#python) aufgeführt werden.

| Name                       | type | Standard     | BESCHREIBUNG |
| ---                        | ---  | ---         | ---         |
| logging_enable             | bool | False       | Aktiviert die Protokollierung. Weitere Informationen finden Sie unter [Protokollierung in den Azure-Bibliotheken](azure-sdk-logging.md). |
| proxies                    | dict | {}          | Proxyserver-URLs. Weitere Informationen finden Sie unter [Konfigurieren von Proxys](azure-sdk-configure-proxy.md). |
| use_env_settings           | bool | True        | Wenn TRUE, ist die Verwendung von `HTTP_PROXY`- und `HTTPS_PROXY`-Umgebungsvariablen für Proxys zulässig. Wenn FALSE, werden Umgebungsvariablen ignoriert. Weitere Informationen finden Sie unter [Konfigurieren von Proxys](azure-sdk-configure-proxy.md). |
| connection_timeout         | INT  | 300         | Das Timeout in Sekunden für das Herstellen einer Verbindung mit Azure-REST-API-Endpunkten. |
| read_timeout               | INT  | 300         | Das Timeout (in Sekunden) für das Abschließen eines Azure-REST-API-Vorgangs (das heißt, das Warten auf eine Antwort). |
| retry_total                | INT  | 10          | Die Anzahl zulässiger Wiederholungsversuche für REST-API-Aufrufe. Verwenden Sie `retry_total=0`, um Wiederholungsversuche zu deaktivieren. |
| retry_mode                 | enum | exponential | Wendet Wiederholungsversuche auf lineare oder exponentielle Weise an. Bei „single“ werden Wiederholungsversuche in regelmäßigen Intervallen ausgeführt. Bei „exponential“ wartet jeder Wiederholungsversuch doppelt so lange wie der vorherige Wiederholungsversuch. |

Einzelne Bibliotheken sind nicht verpflichtet, diese Argumente zu unterstützen, daher sollten Sie sich stets in der Referenzdokumentation für jede Bibliothek über die genauen Details informieren.

### <a name="arguments-for-non-core-libraries"></a>Argumente für Bibliotheken, die keine Kernbibliothekensind

| Name               | type | Standard | BESCHREIBUNG |
| ---                | ---  | ---     | ---         |
| Überprüfen             | bool | True    | Überprüft das SSL-Zertifikat. |
| cert               | str  | Keine    | Pfad zum lokalen Zertifikat für die clientseitige Überprüfung |
| timeout            | INT  | 30      | Zeitlimit für das Herstellen einer Serververbindung in Sekunden. |
| allow_redirects    | bool | False   | Aktiviert Umleitungen. |
| max_redirects      | INT  | 30      | Maximale Anzahl zulässiger Umleitungen |
| proxies            | dict | {}      | Proxyserver-URL. Weitere Informationen finden Sie unter [Konfigurieren von Proxys](azure-sdk-configure-proxy.md). |
| use_env_proxies    | bool | False   | Aktiviert das Lesen von Proxyeinstellungen aus lokalen Umgebungsvariablen. |
| retries            | INT  | 10      | Die Gesamtanzahl der zulässigen Wiederholungsversuche. |
| enable_http_logger | bool | False   | Aktiviert Protokolle von HTTP im Debugmodus. |

## <a name="inline-json-pattern-for-object-arguments"></a>JSON-Inline-Muster für Objektargumente

Bei vielen Vorgängen innerhalb der Azure-Bibliotheken können Objektargumente als diskrete Objekte oder als JSON-Inline-Code ausgedrückt werden.

Angenommen, Sie verfügen über ein [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient)-Objekt, über das Sie eine Ressourcengruppe mit seiner [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-)-Methode erstellen. Das zweite Argument für diese Methode ist vom Typ [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup).

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

Angenommen, Sie haben beispielsweise eine Instanz des [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient)-Objekts und rufen seine [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)-Methode auf. In diesem Fall ist das dritte Argument vom Typ [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters), das wiederum selbst ein Argument vom Typ [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties) enthält. `VaultProperties` wiederum enthält Objektargumente vom Typ [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku) und [`list[AccessPolicyEntry]`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry). Eine `Sku` enthält ein [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname)-Objekt, und jeder `AccessPolicyEntry` enthält ein [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions)-Objekt.

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
- [Beispiel: Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md)

Da diese Beispiele weder sequenziell noch voneinander abhängig sind, gibt es keine vorgegebene Reihenfolge.
