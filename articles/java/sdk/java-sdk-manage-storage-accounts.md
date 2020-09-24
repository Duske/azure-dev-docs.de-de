---
title: Verwalten von Azure-Speicherkonten mit Java | Microsoft-Dokumentation
description: Beispielcode zum Verwalten von Azure-Speicherkonten mit dem Azure SDK für Java
author: rloutlaw
ms.assetid: 49be8b66-3b56-4c10-8f14-9d326d815cb4
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.custom: devx-track-java
ms.openlocfilehash: 7a16f5c9f9c6e27a56bace08aa43aef3c4a9bdb6
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831876"
---
# <a name="manage-azure-storage-accounts-from-your-java-applications"></a>Verwalten von Azure-Speicherkonten über Ihre Java-Anwendungen

[Dieses Beispiel](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) erstellt mit den [Java-Verwaltungsbibliotheken](https://github.com/Azure/azure-sdk-for-java) ein [Azure Storage](/azure/storage/common/storage-introduction)-Konto und verwendet die Kontozugriffsschlüssel. 

## <a name="run-the-sample"></a>Ausführen des Beispiels

Erstellen Sie eine [Authentifizierungsdatei](/azure/java/java-sdk-azure-authenticate#mgmt-file), und legen Sie die Umgebungsvariable `AZURE_AUTH_LOCATION` mit dem vollständigen Pfad zur Datei auf Ihrem Computer fest. Führen Sie dann Folgendes aus:

```
git clone https://github.com/Azure-Samples/storage-java-manage-storage-accounts.git
cd storage-java-manage-storage-accounts
mvn clean compile exec:java
```

Sehen Sie sich das [vollständige Codebeispiel auf GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) an.

## <a name="authenticate-with-azure"></a>Authentifizieren über Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)] 

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

```java
// create a new storage account
StorageAccount storageAccount = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .create();
```

Der bereitgestellte Speichername muss in Azure eindeutig sein und darf nur Kleinbuchstaben und Ziffern enthalten. Das für dieses Konto verwendete Leistungs- und Replikationsstandardprofil lautet [Standard_GRS](/azure/storage/common/storage-redundancy-grs).

## <a name="list-keys-in-a-storage-account"></a>Auflisten von Schlüsseln in einem Speicherkonto
```java
// list the name and value for each access key in the storage account
List<StorageAccountKey> storageAccountKeys = storageAccount.getKeys();
for(StorageAccountKey key : storageAccountKeys)    {
    System.out.println("Key name: " + key.keyName() + " with value "+ key.value());
}
```

In jedem Azure-Speicherkonto werden zwei Schlüssel bereitgestellt, sodass Sie einen Schlüssel erneut generieren können, während Sie mithilfe des anderen Schlüssels weiterhin Zugriff auf den Speicher gewähren.

## <a name="regenerate-a-key-in-a-storage-account"></a>Erneutes Generieren eines Schlüssels in einem Speicherkonto

```java
// regenerate the first key in a storage account and return an updated list of keys 
List<StorageAccountKey> updatedStorageAccountKeys =
    storageAccount.regenerateKey(storageAccountKeys.get(0).keyName());
```

Nach dem Generieren eines neuen Schlüssels müssen Sie alle Azure-Ressourcen und -Anwendungen mit dem neuen Schlüssel aktualisieren.

## <a name="list-all-storage-accounts-in-a-resource-group"></a>Auflisten aller Speicherkonten in einer Ressourcengruppe
```java
// get a list of accounts in a resource group , log info about each one
List<StorageAccount> accounts = azure.storageAccounts().listByResourceGroup(rgName);
for (StorageAccount sa : accounts) {
    System.out.println("Storage Account " + sa.name() + " created @ " + sa.creationTime());
}
```

Unter [com.microsoft.azure.management.storage.StorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) finden Sie eine Reihe nützlicher Methoden zum Überprüfen der Konfiguration eines Speicherkontos.

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten
```java
// delete by ID when you already have a storage account object
azure.storageAccounts().deleteById(storageAccount.id());

// delete by resource group and account name if you don't have an account object
azure.storageAccounts().deleteByResourceGroup(rgName,accountName);
```

> [!NOTE]
> Speicherkonten mit verwendeten Datenträgerimages, die mit virtuellen Computern verbunden sind, oder von anderen Artefakten verwendete Datenträger dürfen nicht mit diesen Methoden entfernt werden. Trennen Sie vor dem Entfernen des Kontos den Speicher von diesen Ressourcen.

## <a name="sample-explanation"></a>Erläuterung des Beispiels

Der [Beispielcode auf GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts):

- erstellt ein Speicherkonto.
- liest und regeneriert erneut Zugriffsschlüssel.
- listet alle Speicherkonten in einer Ressourcengruppe auf.
- löscht das Speicherkonto. 

| Im Beispiel verwendete Klasse | Notizen
|-------|-------|
| [StorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount)  | Darstellung eines Azure-Speicherkontos. Verwenden Sie die Methoden in der Klasse, um Informationen zum Speicherkonto abzurufen.
| [StorageAccountKey](/java/api/com.microsoft.azure.management.storage.storageaccountkey) | `StorageAccount.getKeys()` gibt die Speicherkontoschlüssel zurück. Verwenden Sie die `regenerateKey`-Methoden in `StorageAccount`, um die Schlüssel zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [next-steps](includes/java-next-steps.md)]