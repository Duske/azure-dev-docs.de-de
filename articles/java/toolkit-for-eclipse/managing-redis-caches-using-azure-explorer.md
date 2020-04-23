---
title: Verwalten von Redis Caches mit dem Azure-Explorer für Eclipse
description: Erfahren Sie, wie Sie Ihre Azure Redis Caches mit dem Azure-Explorer für Eclipse verwalten.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 80464f717dd50591c8cf3bbbdb7cd4fa569ba4a7
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81674426"
---
# <a name="managing-redis-caches-using-the-azure-explorer-for-eclipse"></a>Verwalten von Redis Caches mit dem Azure-Explorer für Eclipse

Der Azure-Explorer, der zum Azure-Toolkit für Eclipse gehört, bietet Java-Entwicklern eine einfach zu bedienende Lösung zum Verwalten von Redis Caches in ihrem Azure-Konto innerhalb der IDE von Eclipse.

[!INCLUDE [prerequisites](includes/prerequisites.md)]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-eclipse"></a>Erstellen eines Redis Cache mithilfe von Eclipse

Mit den folgenden Schritten werden die Schritte zum Erstellen eines Redis Cache mit dem Azure-Explorer erläutert.

1. Melden Sie sich beim Azure-Konto gemäß den Anweisungen im Artikel [Anleitung zur Anmeldung für das Azure-Toolkit für Eclipse] an.

1. Erweitern Sie im Toolfenster des **Azure-Explorers** den Knoten **Azure**. Klicken Sie mit der rechten Maustaste auf **Redis Caches**, und klicken Sie dann auf **Redis Cache erstellen**.

   ![Menü „Redis Cache erstellen“][CR01]

1. Wenn das Dialogfeld **Neuer Redis Cache** angezeigt wird, geben Sie die folgenden Optionen an:

   ![Dialogfeld zum Erstellen eines neuen Redis Cache][CR02]

   a. **DNS-Name**: Gibt die DNS-Unterdomäne für den neuen Redis Cache an. Sie wird „.redis.cache.windows.net“ vorangestellt. Beispiel: *wingtiptoys.redis.cache.windows.net*

   b. **Abonnement**: Wählen Sie das Azure-Abonnement aus, das Sie für den neuen Redis Cache verwenden möchten.

   c. **Ressourcengruppe**: Gibt die Ressourcengruppe für den Redis Cache an. Sie müssen eine der folgenden Optionen auswählen:
      * **Neue erstellen**: Gibt an, dass Sie eine neue Ressourcengruppe erstellen möchten.
      * **Vorhandene verwenden**: Gibt an, dass Sie in einer Liste von Ressourcengruppen, die Ihrem Azure-Konto zugeordnet sind, eine Auswahl treffen.

   d. **Standort**: Gibt den Ort an, an dem Ihr Redis Cache erstellt wird, z. B. *USA, Westen*.

   e. **Tarif**: Gibt an, welcher Tarif für Ihren Redis Cache verwendet wird. Diese Einstellung bestimmt die Anzahl von Clientverbindungen. (Weitere Informationen finden Sie unter [Redis Cache – Preise].)

   f. **Nicht-SSL-Port**: Gibt an, ob Ihr Redis Cache andere Verbindungen als SSL-Verbindungen zulässt. Standardmäßig sind nur SSL-Verbindungen zulässig.

1. Wenn Sie alle Redis Cache-Einstellungen angegeben haben, klicken Sie auf **OK**.

Nachdem Ihr Redis Cache erstellt wurde, wird er im Azure-Explorer angezeigt.

   ![Redis Cache im Azure-Explorer][CR03]

> [!NOTE]
>
> Weitere Informationen zum Konfigurieren der Azure Redis Cache-Einstellungen finden Sie unter [Konfigurieren von Azure Redis Cache].
>

## <a name="display-the-properties-for-your-redis-cache-in-eclipse"></a>Anzeigen der Eigenschaften für den Redis Cache in Eclipse

1. Klicken Sie im Azure-Explorer mit der rechten Maustaste auf den Redis Cache, und klicken Sie auf **Eigenschaften anzeigen**.

   ![Azure Explorer-Kontextmenü zum Anzeigen der Eigenschaften für einen Redis Cache][SP01]

1. Der Azure-Explorer zeigt die Eigenschaften für den Redis Cache an.

   ![Redis Cache: Eigenschaften][SP02]

## <a name="delete-your-redis-cache-by-using-eclipse"></a>Löschen des Redis Cache mithilfe von Eclipse

1. Klicken Sie im Azure-Explorer mit der rechten Maustaste auf den Redis Cache, und klicken Sie auf **Löschen**.

   ![Azure Explorer-Kontextmenü zum Löschen eines Redis Cache][DE01]

1. Klicken Sie auf **OK**, wenn die Aufforderung zum Löschen des Redis Cache angezeigt wird.

   ![Aufforderung zum Löschen des Redis Cache][DE02]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Redis Caches, Konfigurationseinstellungen und Preisen finden Sie unter den folgenden Links:

* [Azure Redis Cache]
* [Dokumentation zu Redis Cache]
* [Redis Cache – Preise]
* [Konfigurieren von Azure Redis Cache]

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Redis Cache – Preise]: https://azure.microsoft.com/pricing/details/cache/
[Azure Redis Cache]: https://azure.microsoft.com/services/cache/
[Dokumentation zu Redis Cache]: /azure/redis-cache/
[Konfigurieren von Azure Redis Cache]: /azure/redis-cache/cache-configure

<!-- IMG List -->

[CR01]: media/managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: media/managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: media/managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: media/managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: media/managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: media/managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: media/managing-redis-caches-using-azure-explorer/DE02.png
