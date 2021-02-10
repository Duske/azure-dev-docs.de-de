---
title: Verwenden des Azure SDK für Java
description: Übersicht über die Features und Funktionen des Azure SDK für Java, das Entwicklern zu mehr Produktivität beim Bereitstellen, Verwenden und Verwalten von Azure-Ressourcen verhilft.
author: jonathangiles
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: jogiles
ms.openlocfilehash: 64a13c99f58b109a066acd322aa90f39a74ea0e9
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528484"
---
# <a name="use-the-azure-sdk-for-java"></a>Verwenden des Azure SDK für Java

Das Open-Source-basierte Azure SDK für Java vereinfacht die Bereitstellung, Verwaltung und Verwendung von Azure-Ressourcen über Java-Anwendungscode.

## <a name="important-details"></a>Wichtige Details

* Über die Azure-Bibliotheken kommunizieren Sie mit Azure-Diensten über Java-Code, den Sie entweder lokal oder in der Cloud ausführen.
* Die Bibliotheken unterstützen Java 8 und höher und werden für die Java 8-Baseline und die neueste Java-Version („langfristige Unterstützung“) getestet.
* Die Bibliotheken enthalten vollständige Unterstützung für Java-Module, was bedeutet, dass sie vollständig mit den Anforderungen eines Java-Moduls kompatibel sind und alle relevanten Pakete für die Verwendung exportieren.
* Im Azure SDK für Java sind zahlreiche einzelne Java-Bibliotheken enthalten, die sich auf bestimmte Azure-Dienste beziehen. Das SDK enthält keine weiteren Tools.
* Es gibt getrennte „Verwaltungs“- und „Client“-Bibliotheken (manchmal auch als Bibliotheken der „Verwaltungsebene“ bzw. „Datenebene“ bezeichnet). Jede Gruppe dient unterschiedlichen Zwecken und wird von einer anderen Art von Code genutzt. Weitere Informationen finden Sie in den folgenden Abschnitten dieses Artikels:
  * [Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken.](#connect-to-and-use-azure-resources-with-client-libraries)
  * [Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken.](#provision-and-manage-azure-resources-with-management-libraries)
* Dokumentationsmaterial zu den Bibliotheken finden Sie in der nach Azure-Dienst strukturierten [Azure für Java-Referenz](/java/api/overview/azure/) oder im nach Paketname strukturierten [Java-API-Browser](/java/api/).

## <a name="other-details"></a>Weitere Details

* Da das SDK für Java-Bibliotheken auf der zugrunde liegenden Azure-REST-API basiert, können diese APIs mit vertrauten Java-Paradigmen verwendet werden. Sie können die REST-API aber auch immer direkt aus Java-Code heraus verwenden, wenn dies gewünscht wird.
* Der Quellcode für die Azure-Bibliotheken steht im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-java) zur Verfügung. Da es sich um ein Open-Source-Projekt handelt, sind Beiträge herzlich willkommen.
* Die Bibliotheken des Azure SDK für Java werden momentan mit allgemeinen Cloudmustern wie Authentifizierungsprotokollen, Protokollierung, Ablaufverfolgung, Transportprotokollen, gepufferten Antworten und Wiederholungen aktualisiert.
  * Diese gemeinsam genutzten Funktionen sind in der Bibliothek [azure-core](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core/azure-core) enthalten.
* Weitere Informationen zu den Richtlinien, die wir auf die Bibliotheken anwenden, finden Sie in den [Java Azure SDK-Entwurfsrichtlinien](https://azure.github.io/azure-sdk/java_introduction.html).

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken

Die Clientbibliotheken (oder die „Datenebene“) helfen Ihnen beim Schreiben von Java-Anwendungscode, um mit bereits bereitgestellten Diensten zu interagieren. Clientbibliotheken sind nur für Dienste mit Client-API-Unterstützung vorhanden. Sie können sie identifizieren, weil ihre Maven-Gruppen-ID `com.azure` lautet.

Alle Azure Java-Clientbibliotheken befolgen das gleiche API-Entwurfsmuster wie eine Java-Generator-Klasse, die für das Erstellen einer Instanz eines Clients zuständig ist. Dieses Muster trennt die Definition und Instanziierung des Clients von seinem Vorgang, sodass der Client unveränderlich und somit einfacher zu verwenden ist. Außerdem folgen alle Clientbibliotheken einigen wichtigen Mustern:

* Clientbibliotheken, die synchrone und asynchrone APIs unterstützen, müssen diese APIs in separaten Klassen anbieten. Dies bedeutet, dass in diesen Fällen beispielsweise ein `KeyVaultClient` für synchrone APIs und ein `KeyVaultAsyncClient` für asynchrone APIs vorhanden ist.

* Es gibt eine einzige Generator-Klasse, die für die Erstellung der synchronen und asynchronen APIs zuständig ist. Der Generator wird ähnlich wie die synchrone Clientklasse benannt, wobei `Builder` enthalten ist. Beispiel: `KeyVaultClientBuilder`. Dieser Generator verfügt über `buildClient()`- und `buildAsyncClient()`-Methoden zum Erstellen von entsprechenden Clientinstanzen.

Aufgrund dieser Konventionen sind alle Klassen, die mit `Client` enden, unveränderlich und stellen Vorgänge für die Interaktion mit einem Azure-Dienst bereit. Alle Klassen, die mit `ClientBuilder` enden, stellen Vorgänge bereit, um eine Instanz eines bestimmten Clienttyps zu konfigurieren und zu erstellen.

### <a name="client-libraries-example"></a>Clientbibliotheksbeispiel

Das folgende Codebeispiel zeigt, wie ein synchroner Schlüsseltresor `KeyClient` erstellt wird:

```java
KeyClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildClient();
```

Das folgende Codebeispiel zeigt, wie ein asynchroner Schlüsseltresor `KeyAsyncClient` erstellt wird:

```java
KeyAsyncClient client = new KeyClientBuilder()
        .endpoint(<your Key Vault URL>)
        .credential(new DefaultAzureCredentialBuilder().build())
        .buildAsyncClient();
```

Weitere Informationen zum Arbeiten mit den einzelnen Clientbibliotheken finden Sie in der Datei *README.md*, die sich im Projektverzeichnis der Bibliothek im [GitHub-Repository des SDK](https://github.com/Azure/azure-sdk-for-java) befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/java/api) sowie in den [Azure-Beispielen](/samples/browse/?products=azure&languages=java) zur Verfügung.

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken

Die Verwaltungsbibliotheken (oder die „Verwaltungsebene“) helfen Ihnen beim Erstellen, Bereitstellen und anderweitigen Verwalten von Azure-Ressourcen über Java-Anwendungscode. Diese Bibliotheken finden Sie in der Maven-Gruppen-ID `com.azure.resourcemanager`. Alle Azure-Dienste verfügen über entsprechende Verwaltungsbibliotheken.

Mit den Verwaltungsbibliotheken können Sie Konfigurations- und Bereitstellungsskripts schreiben, um dieselben Aufgaben auszuführen, die Sie auch über das [Azure-Portal](https://portal.azure.com/) oder mithilfe der [Azure CLI](/cli/azure/install-azure-cli) durchführen können.

Alle Azure Java-Verwaltungsbibliotheken stellen eine `*Manager`-Klasse als Dienst-API bereit, z. B. `ComputeManager` für den Azure-Computedienst oder `AzureResourceManager` für die Aggregation beliebter Dienste.

### <a name="management-libraries-example"></a>Verwaltungsbibliotheksbeispiel

Das folgende Codebeispiel zeigt, wie Sie einen `ComputeManager` erstellen:

```java
ComputeManager computeManager = ComputeManager
    .authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE));
```

Im folgenden Codebeispiel wird gezeigt, wie Sie einen neuen virtuellen Computer bereitstellen:

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .define(<your virtual machine>)
    .withRegion(Region.US_WEST)
    .withExistingResourceGroup(<your resource group>)
    .withNewPrimaryNetwork("10.0.0.0/28")
    .withPrimaryPrivateIPAddressDynamic()
    .withoutPrimaryPublicIPAddress()
    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_18_04_LTS)
    .withRootUsername(<virtual-machine username>)
    .withSsh(<virtual-machine SSH key>)
    .create();
```

Im folgenden Codebeispiel wird gezeigt, wie Sie einen vorhandenen virtuellen Computer abrufen:

```java
VirtualMachine virtualMachine = computeManager.virtualMachines()
    .getByResourceGroup(<your resource group>, <your virtual machine>);
```

Im folgenden Codebeispiel wird gezeigt, wie der virtuelle Computer aktualisiert und ein neuer Datenträger hinzugefügt wird:

```java
virtualMachine.update()
    .withNewDataDisk(10)
    .apply();
```

Weitere Informationen zum Arbeiten mit den einzelnen Verwaltungsbibliotheken finden Sie in der Datei *README.md*, die sich im Projektverzeichnis der Bibliothek im [GitHub-Repository des SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/resourcemanager#readme) befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/java/api) sowie in den [Azure-Beispielen](/samples/browse/?products=azure&languages=java) zur Verfügung.

## <a name="get-help-and-connect-with-the-sdk-team"></a>Hilfe erhalten und das SDK-Team kontaktieren

* Besuchen Sie die [Dokumentation zum Azure SDK für Java](https://azure.github.io/azure-sdk-for-java/).
* Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-for-java) Fragen an die Community.
* Öffnen Sie Issues mit dem SDK im [GitHub-Repository](https://github.com/Azure/azure-sdk-for-java/issues).
* Erwähnen Sie [@AzureSDK](https://twitter.com/AzureSdk/) auf Twitter.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, was das Azure SDK für Java ist, können Sie sich detaillierte Einblicke in viele der übergreifenden Konzepte verschaffen, die für Produktivität bei der Verwendung der Bibliotheken verfügbar sind. Die folgenden Artikel bieten gute Ausgangspunkte:

* [HTTP-Clients und -Pipelines](http-client-pipeline.md)
* [Asynchrone Programmierung](async-programming.md)
* [Paginierung und Iteration](pagination.md)
* [Zeitintensive Vorgänge](lro.md)
* [Konfigurieren von Proxys](proxying.md)
* [Konfigurieren von Ablaufverfolgung](tracing.md)
