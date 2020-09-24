---
title: Verwalten virtueller Computer mit dem Azure-Explorer für Eclipse
description: Erfahren Sie, wie Sie Ihre virtuellen Azure-Computer mit dem Azure-Explorer für Eclipse verwalten.
documentationcenter: java
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: e457d4fe152f9fa5fa64bafaa4f49311e8ff4475
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831886"
---
# <a name="manage-virtual-machines-by-using-the-azure-explorer-for-eclipse"></a>Verwalten virtueller Computer mit dem Azure-Explorer für Eclipse

Der Azure-Explorer gehört zum Azure-Toolkit für Eclipse und bietet Java-Entwicklern eine einfach zu bedienende Lösung zum Verwalten von virtuellen Computern in ihrem Azure-Konto innerhalb der integrierten Entwicklungsumgebung (IDE) von Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Melden Sie sich beim Azure-Konto gemäß den Anweisungen in [Anleitung zur Azure-Anmeldung für das Azure-Toolkit für Eclipse](./sign-in-instructions.md) an.

1. Erweitern Sie in der Ansicht des **Azure-Explorers** den Knoten **Azure**. Klicken Sie mit der rechten Maustaste auf **Virtuelle Computer**, und klicken Sie dann auf **VM erstellen**.

   :::image type="content" source="media/managing-virtual-machines-using-azure-explorer/CR01.png" alt-text="Option „VM erstellen“ im Azure-Explorer":::

1. Wählen Sie im Dialogfeld **Abonnement auswählen** Ihr Abonnement aus, und klicken Sie dann auf **Weiter**.

1. Wählen Sie im Fenster **Virtuelles Maschinenimage auswählen** Ihren **Standort** (z. B. *USA, Westen*) aus. Sie haben die Möglichkeit, mit einem empfohlenen Image fortzufahren oder ein benutzerdefiniertes Image auszuwählen. In dieser Schnellstartanleitung verwenden wir das empfohlene Image. 

   Geben Sie die folgenden Informationen ein, wenn Sie ein benutzerdefiniertes Image auswählen möchten:
   * **Herausgeber**: Der Herausgeber, der das zum Erstellen der VM verwendete Image erstellt hat (z. B. *Microsoft*).

   * **Angebot**: Das zu verwendende VM-Angebot des ausgewählten Herausgebers (z. B. *JDK*).

   * **SKU**: Die zu verwendende SKU (Stock Keeping Unit) des ausgewählten Angebots (z. B. *JDK_8*).

   * **Versionsnummer**: Die zu verwendende Version der ausgewählten SKU.

1. Klicken Sie auf **Weiter**.

1. Geben Sie im Fenster **Grundlegende Einstellungen des virtuellen Computers** die folgenden Informationen ein:

   * **Name des virtuellen Computers**: Der Name Ihrer neuen VM. Der Name muss mit einem Buchstaben beginnen und darf nur Buchstaben, Ziffern und Bindestriche enthalten.

   * **Size**: Die Anzahl von Kernen und der Arbeitsspeicher, die der VM zugeordnet werden sollen.

   * **Benutzername**: Das Administratorkonto, das für die Verwaltung Ihrer VM erstellt werden soll.

   * **Kennwort**: Das Kennwort für Ihr Administratorkonto. Geben Sie Ihr Kennwort im Feld **Bestätigen** erneut ein, damit die Richtigkeit überprüft werden kann.

1. Klicken Sie auf **Weiter**.

1. Geben Sie im Fenster **Zugeordnete Ressourcen** die folgenden Informationen ein:
   * **Ressourcengruppe**: Die Ressourcengruppe für Ihre VM. Wählen Sie eine der folgenden Optionen aus:
      * **Neue erstellen**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden**: Gibt an, dass Sie eine Ressourcengruppe auswählen möchten, die Ihrem Azure-Konto bereits zugeordnet ist.

   * **Speicherkonto**: Das Speicherkonto, das zum Speichern der VM verwendet werden soll. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen.

   * **Virtuelles Netzwerk** und **Subnetz**: Das virtuelle Netzwerk und das Subnetz, mit denen Ihre VM eine Verbindung herstellt. Sie können ein vorhandenes Netzwerk und Subnetz verwenden oder ein neues Netzwerk und Subnetz erstellen. Wenn Sie **Neu erstellen** auswählen, wird das folgende Dialogfeld angezeigt:

   * **Öffentliche IP-Adresse:** Eine externe IP-Adresse für Ihre VM. Sie können eine neue IP-Adresse erstellen oder **(Keine)** auswählen, wenn Ihr virtueller Computer nicht über eine öffentliche IP-Adresse verfügt.

   * **Netzwerksicherheitsgruppe**: Eine optionale Netzwerkfirewall für Ihre VM. Sie können eine vorhandene Firewall auswählen oder **(Keine)** auswählen, wenn Ihr virtueller Computer keine Netzwerkfirewall verwendet.

   * **Verfügbarkeitsgruppe**: Eine optionale Verfügbarkeitsgruppe, der Ihre VM angehören kann. Sie können eine vorhandene Verfügbarkeitsgruppe auswählen, eine neue erstellen oder **(Keine)** auswählen, wenn Ihr virtueller Computer keiner Verfügbarkeitsgruppe angehören soll.

10. Klicken Sie auf **Fertig stellen**.  

      > [!NOTE]
      > Sie können den Status der Erstellung unten rechts in Ihrem Eclipse-Arbeitsbereich überprüfen.

## <a name="restart-a-virtual-machine"></a>Neustarten einer VM

Um einen virtuellen Computer mithilfe des Azure-Explorers in Eclipse neu zu starten, gehen Sie folgendermaßen vor:

1. Klicken Sie in der Ansicht **Azure-Explorer** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Neu starten** aus.

1. Klicken Sie im Bestätigungsfenster auf **OK**.

   ![Fenster für die Bestätigung des VM-Neustarts](media/managing-virtual-machines-using-azure-explorer/RE02.png)

## <a name="shut-down-a-virtual-machine"></a>Herunterfahren eines virtuellen Computers

Um einen ausgeführten virtuellen Computer mithilfe des Azure-Explorers in Eclipse herunterzufahren, gehen Sie folgendermaßen vor:

1. Klicken Sie in der Ansicht **Azure-Explorer** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Herunterfahren** aus.

1. Klicken Sie im Bestätigungsfenster auf **OK**.

   ![Bestätigungsfenster für das Herunterfahren eines virtuellen Computers](media/managing-virtual-machines-using-azure-explorer/SH02.png)

## <a name="delete-a-virtual-machine"></a>Löschen einer VM

Um einen virtuellen Computer mithilfe des Azure-Explorers in Eclipse zu löschen, gehen Sie folgendermaßen vor:

1. Klicken Sie in der Ansicht **Azure-Explorer** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Löschen** aus.

1. Klicken Sie im Bestätigungsfenster auf **Ja**.

   ![Bestätigungsfenster für das Löschen eines virtuellen Computers](media/managing-virtual-machines-using-azure-explorer/DE02.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Größen und Preisen für virtuelle Azure-Computer finden Sie in den folgenden Ressourcen:

* Größen für virtuelle Azure-Computer
  * [Größen für virtuelle Windows-Computer in Azure]
  * [Größen für virtuelle Linux-Computer in Azure]
* Preise für virtuelle Azure-Computer
  * [Preise von virtuellen Windows-Computern]
  * [Preise von virtuellen Linux-Computern]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Größen für virtuelle Windows-Computer in Azure]: /azure/virtual-machines/sizes
[Größen für virtuelle Linux-Computer in Azure]: /azure/virtual-machines/sizes
[Preise von virtuellen Windows-Computern]: https://azure.microsoft.com/pricing/details/virtual-machines/windows/
[Preise von virtuellen Linux-Computern]: https://azure.microsoft.com/pricing/details/virtual-machines/linux/

<!-- IMG List -->

[RE01]: media/managing-virtual-machines-using-azure-explorer/RE01.png
[RE02]: media/managing-virtual-machines-using-azure-explorer/RE02.png

[SH01]: media/managing-virtual-machines-using-azure-explorer/SH01.png
[SH02]: media/managing-virtual-machines-using-azure-explorer/SH02.png

[DE01]: media/managing-virtual-machines-using-azure-explorer/DE01.png
[DE02]: media/managing-virtual-machines-using-azure-explorer/DE02.png

[CR01]: media/managing-virtual-machines-using-azure-explorer/CR01.png
[CR02]: media/managing-virtual-machines-using-azure-explorer/CR02.png
[CR03]: media/managing-virtual-machines-using-azure-explorer/CR03.png
[CR04]: media/managing-virtual-machines-using-azure-explorer/CR04.png
[CR05]: media/managing-virtual-machines-using-azure-explorer/CR05.png
[CR06]: media/managing-virtual-machines-using-azure-explorer/CR06.png
[CR07]: media/managing-virtual-machines-using-azure-explorer/CR07.png
[CR08]: media/managing-virtual-machines-using-azure-explorer/CR08.png