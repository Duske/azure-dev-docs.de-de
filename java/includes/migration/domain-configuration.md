---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830978"
---
### <a name="domain-configuration"></a>Domänenkonfiguration

Die Hauptkonfigurationseinheit in WebLogic Server ist die Domäne. Daher enthält die Datei *config.xml* viele Konfigurationsinformationen, die Sie bei der Migration sorgfältig berücksichtigen müssen. Die Datei enthält Verweise auf zusätzliche XML-Dateien, die in Unterverzeichnissen gespeichert sind. Oracle rät dazu, wie gewohnt die **Verwaltungskonsole** zu verwenden, um die verwaltbaren Objekte und Dienste von WebLogic Server zu konfigurieren, und WebLogic Server die Verwaltung der Datei *config.xml* zu überlassen. Weitere Informationen finden Sie unter [Domänenkonfigurationsdateien](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>Innerhalb Ihrer Anwendung

Untersuchen Sie die Datei *WEB-INF/weblogic.xml* bzw. *WEB-INF/web.xml*.
