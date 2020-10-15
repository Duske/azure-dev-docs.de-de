---
title: Verwalten von Speicherkonten mit dem Azure-Explorer für Eclipse
description: Erfahren Sie, wie Sie Ihre Azure Storage-Konten mit Azure-Explorer für Eclipse verwalten.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 7a2a3406e43e1bc65ac61b7197399cdce08ef29b
ms.sourcegitcommit: 723441eda0eb4ff893123201a9e029b7becf5ecc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91846411"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-eclipse"></a>Verwalten von Speicherkonten mithilfe von Azure-Explorer für Eclipse

> [!NOTE]
> Das Feature für Speicherkonten im Azure-Explorer ist veraltet. Sie können das Azure-Portal verwenden, um Speicherkonten und Container zu erstellen und zu verwalten. Schnellstartanleitungen zur Verwaltung von Speicherkonten finden Sie in der Dokumentation zu [Azure Storage](/azure/storage/blobs/storage-quickstart-blobs-portal).

Der Azure-Explorer gehört zum Azure-Toolkit für Eclipse und bietet Java-Entwicklern eine einfach zu bedienende Lösung zum Verwalten von Speicherkonten in ihrem Azure-Konto innerhalb der integrierten Entwicklungsumgebung (IDE) von Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

1. Melden Sie sich beim Azure-Konto gemäß den Anweisungen in [Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse](./sign-in-instructions.md) an.

1. Erweitern Sie in der Ansicht des **Azure-Explorers** den Knoten **Azure**. Klicken Sie mit der rechten Maustaste auf **Speicherkonten**, und klicken Sie dann auf **Speicherkonto erstellen**.

1. Geben Sie im Dialogfeld **Speicherkonto erstellen** folgende Details an:

   * **Name**: Der Name für das neue Speicherkonto.

   * **Abonnement**: Das Azure-Abonnement, das Sie für das neue Speicherkonto verwenden möchten.

   * **Ressourcengruppe**: Die Ressourcengruppe für Ihre VM. Wählen Sie eine der folgenden Optionen aus:
      * **Neue erstellen**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden**: Gibt an, dass Sie in einer Liste von Ressourcengruppen, die Ihrem Azure-Konto zugeordnet sind, eine Auswahl treffen möchten.

   * **Region**: Der Standort, an dem Ihr Speicherkonto erstellt wird (z. B. „USA, Westen“).

   * **Kontoart**: Der Typ des zu erstellenden Speicherkontos (z. B. „Universell v1“). Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten].

   * **Leistung**: Das zu verwendende Speicherkontoangebot des ausgewählten Herausgebers (z. B. „Standard“). Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage].

   * **Replikation**: Die Replikation für das Speicherkonto (z. B. „Lokal redundant“). Weitere Informationen finden Sie unter [Azure-Speicherreplikation].

1. Nachdem Sie alle vorhergehenden Optionen angegeben haben, klicken Sie auf **Erstellen**.

## <a name="create-and-manage-storage-containers"></a>Erstellen und Verwalten von Speichercontainern

Verwenden Sie zum Erstellen und Verwalten von Speichercontainern das Azure-Portal, oder stellen Sie Ihre Ressourcen programmgesteuert bereit.

Ein Schritt-für-Schritt-Tutorial zur Verwendung des Azure-Portals zum Erstellen eines Containers in Azure Storage und zum Hoch- und Herunterladen von Blockblobs für diesen Container finden Sie unter [Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal](/azure/storage/blobs/storage-quickstart-blobs-portal).

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

1. Klicken Sie in der **Azure-Explorers**-Ansicht mit der rechten Maustaste auf das Speicherkonto, und klicken Sie dann auf **Löschen**.

1. Klicken Sie im Bestätigungsfenster auf **OK**.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Storage-Konten sowie Größen und Preisen finden Sie in den folgenden Ressourcen:

* [Einführung in Microsoft Azure Storage]
* [Informationen zu Azure-Speicherkonten]
* Größen für Azure Storage-Konten
  * [Größen für Windows-Speicherkonten in Azure]
  * [Größen für Linux-Speicherkonten in Azure]
* Preise von Azure Storage-Konten
  * [Preise von Windows-Speicherkonten]
  * [Preise von Linux-Speicherkonten]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Einführung in Microsoft Azure Storage]: /azure/storage/common/storage-introduction
[Informationen zu Azure-Speicherkonten]: /azure/storage/storage-create-storage-account
[Azure-Speicherreplikation]: /azure/storage/storage-redundancy
[Skalierbarkeits- und Leistungsziele für Azure Storage]: /azure/storage/storage-scalability-targets
[Naming and referencing containers, blobs, and metadata]: /rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata

[Größen für Windows-Speicherkonten in Azure]: /azure/virtual-machines/sizes
[Größen für Linux-Speicherkonten in Azure]: /azure/virtual-machines/sizes
[Preise von Windows-Speicherkonten]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Preise von Linux-Speicherkonten]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[CS01]: media/managing-storage-accounts-using-azure-explorer/CS01.png
[CS02]: media/managing-storage-accounts-using-azure-explorer/CS02.png
[CC01]: media/managing-storage-accounts-using-azure-explorer/CC01.png
[CC02]: media/managing-storage-accounts-using-azure-explorer/CC02.png

[DS01]: media/managing-storage-accounts-using-azure-explorer/DS01.png
[DS02]: media/managing-storage-accounts-using-azure-explorer/DS02.png
[DC01]: media/managing-storage-accounts-using-azure-explorer/DC01.png
[DC02]: media/managing-storage-accounts-using-azure-explorer/DC02.png