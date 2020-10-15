---
title: Verwalten von Speicherkonten mit dem Azure-Explorer für IntelliJ
description: Erfahren Sie, wie Sie Ihre Azure-Speicherkonten mit Azure-Explorer für IntelliJ verwalten.
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 2c0de20cd28725d8cf9b482841978640c8a37ee8
ms.sourcegitcommit: 723441eda0eb4ff893123201a9e029b7becf5ecc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91846781"
---
# <a name="manage-storage-accounts-by-using-the-azure-explorer-for-intellij"></a>Verwalten von Speicherkonten mithilfe von Azure-Explorer für IntelliJ

> [!NOTE]
> Das Feature für Speicherkonten im Azure-Explorer ist veraltet. Sie können das Azure-Portal verwenden, um Speicherkonten und Container zu erstellen und zu verwalten. Schnellstartanleitungen zur Verwaltung von Speicherkonten finden Sie in der Dokumentation zu [Azure Storage](/azure/storage/blobs/storage-quickstart-blobs-portal).

Der Azure-Explorer gehört zum Azure-Toolkit für IntelliJ und bietet Java-Entwicklern eine einfach zu bedienende Lösung zum Verwalten von Speicherkonten in ihrem Azure-Konto innerhalb der integrierten Entwicklungsumgebung (IDE) von IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Gehen Sie folgendermaßen vor, um ein Speicherkonto mit dem Azure-Explorer zu erstellen:

1. Melden Sie sich beim Azure-Konto gemäß den Anweisungen in [Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ] an. 

2. Erweitern Sie in der Ansicht des **Azure-Explorers** den Knoten **Azure**. Klicken Sie mit der rechten Maustaste auf **Speicherkonten**, und klicken Sie dann auf **Speicherkonto erstellen**.

3. Geben Sie im Dialogfeld **Speicherkonto erstellen** folgende Details an:

   * **Name**: Der Name für das neue Speicherkonto.

   * **Kontoart**: Der Typ des zu erstellenden Speicherkontos (z. B. „Blob Storage“). Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten]. 

   * **Leistung**: Das zu verwendende Speicherkontoangebot des ausgewählten Herausgebers (z. B. „Premium“). Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage]. 

   * **Replikation**: Die Replikation für das Speicherkonto (z. B. „Zonenredundant“). Weitere Informationen finden Sie unter [Azure-Speicherreplikation]. 

   * **Abonnement**: Das Azure-Abonnement, das Sie für das neue Speicherkonto verwenden möchten.

   * **Standort**: Der Standort, an dem Ihr Speicherkonto erstellt wird (z. B. „USA, Westen“).

   * **Ressourcengruppe**: Die Ressourcengruppe für Ihre VM. Wählen Sie eine der folgenden Optionen aus:
      * **Neue erstellen**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden**: Gibt an, dass Sie in einer Liste von Ressourcengruppen, die Ihrem Azure-Konto zugeordnet sind, eine Auswahl treffen möchten.

4. Nachdem Sie alle vorstehenden Optionen angegeben haben, klicken Sie auf **OK**.

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

Gehen Sie folgendermaßen vor, um ein Speicherkonto mit dem Azure-Explorer zu löschen:

1. Klicken Sie in der Ansicht des **Azure-Explorers** mit der rechten Maustaste auf das Speicherkonto, und wählen Sie dann **Löschen** aus.

2. Klicken Sie im Bestätigungsfenster auf **Ja**.


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

[Anleitung zur Anmeldung für das Azure-Toolkit für IntelliJ]: ./sign-in-instructions.md
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