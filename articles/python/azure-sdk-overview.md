---
title: Verwenden des Azure SDK für Python
description: Übersicht über die Features und Funktionen des Azure SDK für Python, das Entwicklern zu mehr Produktivität beim Bereitstellen, Verwenden und Verwalten von Azure-Ressourcen verhilft.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 856487b06e7a84b0659126d8959ce45b5309a103
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631546"
---
# <a name="use-the-azure-sdk-for-python"></a>Verwenden des Azure SDK für Python

Das Open-Source-basierte Azure SDK für Python vereinfacht die Bereitstellung, Verwaltung und Verwendung von Azure-Ressourcen über Python-Anwendungscode.

## <a name="the-details-you-really-want-to-know"></a>Die Details, die Sie wirklich interessieren

- Das SDK unterstützt Python 2.7 und Python 3.5.3 oder höher und wurde außerdem mit PyPy 5.4+ getestet.

- Das SDK besteht aus über 180 einzelnen Python-Bibliotheken, die sich auf bestimmte Azure-Dienste beziehen.

- Sie installieren die benötigten Bibliotheken mit `pip install <library_name>`, indem Sie die Bibliotheksnamen aus der [Releaseliste](https://azure.github.io/azure-sdk/releases/latest/all/python.html) verwenden. Weitere Details finden Sie unter [Installieren von Azure SDK-Bibliotheken](azure-sdk-install.md).

- Es gibt getrennte „Verwaltungs“- und „Client“-Bibliotheken (manchmal auch als Bibliotheken der „Verwaltungsebene“ bzw. „Datenebene“ bezeichnet). Jede Gruppe dient unterschiedlichen Zwecken und wird von einer anderen Art von Code genutzt. Weitere Details finden Sie in den folgenden Abschnitten weiter unten in diesem Artikel:
  - [Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken](#provision-and-manage-azure-resources-with-management-libraries)
  - [Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken](#connect-to-and-use-azure-resources-with-client-libraries)

- Dokumentation für das SDK finden Sie in der [Referenz zum Azure SDK für Python](/python/api/overview/azure/?view=azure-python), das nach Azure-Diensten organisiert ist, oder im [Python-API-Browser](/python/api/?view=azure-python), der nach Paketnamen organisiert ist. Derzeit müssen Sie häufig auf eine Reihe von Ebenen klicken, um zu den Klassen und Methoden zu gelangen, die Sie interessieren. Wir möchten uns im Voraus für diese unterdurchschnittliche Erfahrung entschuldigen. Wir arbeiten an einer Verbesserung!

- Wenn Sie die Bibliotheken selbst ausprobieren möchten, beginnen Sie mit [Beispiel: Erstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md), gefolgt von [Beispiel: Verwenden von Azure Storage](azure-sdk-example-storage.md).

### <a name="non-essential-but-still-interesting-details"></a>Nicht wesentliche, aber immer noch interessante Details

- Da die Azure CLI unter Verwendung der SDK-Verwaltungsbibliotheken in Python geschrieben wurde, können Sie alles, was Sie mit Azure CLI-Befehlen machen können, auch mit einem Python-Skript durchführen. Dies bedeutet, dass CLI-Befehle viele hilfreiche Features bereitstellen, wie z. B. das gemeinsame Ausführen mehrerer Aufgaben, das automatische Verarbeiten asynchroner Vorgänge, das Formatieren von Ausgaben wie Verbindungszeichenfolgen usw. Folglich kann die Verwendung der CLI (oder ihres Äquivalents, der Azure PowerShell) für automatisierte Bereitstellungs- und Verwaltungsskripts wesentlich bequemer sein, als das Schreiben des entsprechenden Python-Codes, es sei denn, Sie möchten einen wesentlich höheren Grad der Kontrolle über den Prozess ausüben.

- Das Azure SDK für Python ist eine Python-Ebene, die auf der zugrunde liegenden Azure-REST-API basiert und es Ihnen gestattet, diese APIs über vertraute Python-Paradigmen zu verwenden. Sie können die REST-API aber auch immer direkt aus Python-Code heraus verwenden, wenn dies gewünscht ist.

- Sie finden den Quellcode für das SDK unter [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Da es sich um ein Open-Source-Projekt handelt, sind Beiträge herzlich willkommen!

- Auch wenn Sie das SDK mit anderen Interpretern wie IronPython und Jython verwenden können, mit denen wir es nicht getestet haben, kann es dabei zu vereinzelten Problemen und Inkompatibilitäten kommen.

- Das Quellrepository für die SDK-Dokumentation finden Sie unter [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/).

- Die Bibliotheken des Azure SDK für Python werden momentan mit allgemeinen Cloudmustern wie Authentifizierungsprotokollen, Protokollierung, Ablaufverfolgung, Transportprotokollen, gepufferten Antworten und Wiederholungen aktualisiert.

  - Diese gemeinsam genutzten Funktionen sind in der Bibliothek [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) enthalten.

  - Die Bibliotheken, die aktuell mit der Core-Bibliothek funktionieren, sind unter[Neueste Releases des Azure SDK für Python](https://azure.github.io/azure-sdk/releases/latest/#python) aufgeführt. Diese Bibliotheken, primär die Clientbibliotheken, werden manchmal als „Track 2“ bezeichnet.

  - Die Verwaltungsbibliotheken, die noch nicht aktualisiert wurden, werden mitunter als „Track 1“ bezeichnet.

- Ausführliche Informationen zu den von uns auf das SDK angewendeten Richtlinien finden Sie in den [Python-Richtlinien: Einführung](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken

Die *Verwaltungs*bibliotheken des SDK (oder die „Verwaltungsebene“), deren Namen alle mit `azure-mgmt-` beginnen, helfen Ihnen beim Erstellen, Bereitstellen und anderweitigen Verwalten von Azure-Ressourcen über Python-Skripts. Alle Azure-Dienste verfügen über entsprechende Verwaltungsbibliotheken.

Mit den Verwaltungsbibliotheken können Sie Konfigurations- und Bereitstellungsskripts schreiben, um dieselben Aufgaben auszuführen, die Sie auch über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [Azure CLI](/cli/azure/install-azure-cli) durchführen können. (Wie zuvor erwähnt, ist die Azure CLI in Python geschrieben und verwendet die Verwaltungsbibliotheken, um ihre verschiedenen Befehle zu implementieren.)

Ausführliche Informationen zur Verwendung der Verwaltungsbibliotheken finden Sie im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) des SDK in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](https://docs.microsoft.com/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken

Die *Client*bibliotheken (oder die „Datenebene“) helfen Ihnen beim Schreiben von Python-Anwendungscode, um mit bereits bereitgestellten Diensten zu interagieren. Das SDK stellt Clientbibliotheken nur für diese Dienste bereit, die eine Client-API unterstützen.

Ausführliche Informationen zur Verwendung der Clientbibliotheken finden Sie im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) des SDK in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](https://docs.microsoft.com/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="inline-json-pattern-for-object-arguments"></a>JSON-Inline-Muster für Objektargumente

Viele Vorgänge innerhalb des Azure SDK unterstützen ein gängiges Muster, das es Ihnen ermöglicht, Objektargumente als diskrete Objekte oder als JSON-Inline-Code auszudrücken.

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

Bei der Verwendung von JSON konvertiert das SDK den JSON-Inline-Code automatisch in den für das betreffende Argument geeigneten Objekttyp.

Objekte können auch geschachtelte Objektargumente besitzen, und in diesem Fall können Sie dann auch geschachtelten JSON-Code verwenden.

Angenommen, Sie haben beispielsweise eine Instanz des [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python)-Objekts und rufen seine [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)-Methode auf. In diesem Fall ist das dritte Argument vom Typ [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), das wiederum selbst ein Argument vom Typ [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python) enthält. `VaultProperties` wiederum enthält Objektargumente vom Typ [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) und [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). Eine `Sku` enthält ein [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python)-Objekt, und jeder `AccessPolicyEntry` enthält ein [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python)-Objekt.

Um `create_or_update` mit eingebetteten Objekten aufzurufen, verwenden Sie Code wie den folgenden (vorausgesetzt, `tenant_id` und `object_id` sind bereits definiert):

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

Da beide Formen vollständig äquivalent sind, können Sie ganz nach Belieben auswählen bzw. beide sogar mischen.

Wenn Ihr JSON-Code nicht ordnungsgemäß formatiert ist, erhalten Sie in der Regel den Fehler „DeserializationError: Deserialisierung in Objekt nicht möglich: Typ, AttributeError: ‚str‘-Objekt hat kein Attribut ‚get‘.". Eine häufige Ursache für diesen Fehler ist, dass Sie eine einzelne Zeichenfolge für eine Eigenschaft bereitstellen, wenn das SDK ein geschachteltes JSON-Objekt erwartet. Wenn Sie z. B. im vorherigen Beispiel `"sku": "standard"` verwenden, generiert dies diesen Fehler, weil der Parameter `sku` ein `Sku`-Objekt ist, das JSON-Inline-Objektcode erwartet, in diesem Fall `{ "name": "standard"}`, der dem erwarteten `SkuName`-Typ entspricht.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Installieren von SDK-Bibliotheken >>>](azure-sdk-install.md)

## <a name="get-help-and-connect-with-the-sdk-team"></a>Hilfe erhalten und das SDK-Team kontaktieren

- Sehen Sie sich die [Dokumentation des Azure SDK für Python](https://aka.ms/python-docs) an.
- Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python) Fragen an die Community.
- Dokumentieren Sie Probleme für das SDK auf [GitHub](https://github.com/Azure/azure-sdk-for-python/issues).
- Erwähnen Sie [@AzureSDK](https://twitter.com/AzureSdk/) auf Twitter.
