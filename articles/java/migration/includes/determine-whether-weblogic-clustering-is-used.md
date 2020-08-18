---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: f50115be65be8e746527b3d4ee833a738109ddbc
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052255"
---
### <a name="determine-whether-weblogic-clustering-is-used"></a>Ermitteln, ob das WebLogic-Clustering genutzt wird

Höchstwahrscheinlich haben Sie Ihre Anwendung auf mehreren WebLogic-Servern bereitgestellt, um Hochverfügbarkeit zu erzielen. Sie können diese Cluster direkt aus Ihrer lokalen Installation zu der in Azure Virtual Machines ausgeführten WebLogic-Instanz migrieren. Weitere Informationen finden Sie in der Oracle-Dokumentation unter [Domänenkonfigurationsdateien](https://docs.oracle.com/middleware/12213/wls/DOMCF/config_files.htm#DOMCF127).

### <a name="account-for-load-balancing-requirements"></a>Konto für Lastenausgleichsanforderungen

Der Lastenausgleich ist ein wesentlicher Bestandteil der Migration Ihres Oracle WebLogic Server-Clusters zu Azure.  Die einfachste Lösung ist die Verwendung der integrierten Unterstützung für [Azure Application Gateway](/azure/application-gateway/overview), die im Azure Marketplace-Angebot für Oracle WebLogic Server-Cluster bereitgestellt wird.  Ein Tutorial zu diesem Thema finden Sie unter [Tutorial: Migrieren eines WebLogic Server-Clusters zu Azure mit Azure Application Gateway als Lastenausgleich](../migrate-weblogic-with-app-gateway.md).

Eine Zusammenfassung der Funktionen von Azure Application Gateway im Vergleich zu anderen Azure-Lastenausgleichslösungen finden Sie unter [Übersicht über Lastenausgleichsoptionen in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).
