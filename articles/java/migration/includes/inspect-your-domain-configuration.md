---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 750a8cdd34ead8390a0c4c2cb028f4624bd256a9
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988917"
---
### <a name="inspect-your-domain-configuration"></a>Untersuchen der Domänenkonfiguration

Die Hauptkonfigurationseinheit in WebLogic Server ist die Domäne. Daher enthält die Datei *config.xml* viele Konfigurationsinformationen, die Sie bei der Migration sorgfältig berücksichtigen müssen. Die Datei enthält Verweise auf zusätzliche XML-Dateien, die in Unterverzeichnissen gespeichert sind. Oracle rät dazu, wie gewohnt die **Verwaltungskonsole** zu verwenden, um die verwaltbaren Objekte und Dienste von WebLogic Server zu konfigurieren, und WebLogic Server die Verwaltung der Datei *config.xml* zu überlassen. Weitere Informationen finden Sie unter [Domänenkonfigurationsdateien](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>Innerhalb Ihrer Anwendung

Untersuchen Sie die Datei *WEB-INF/weblogic.xml* bzw. *WEB-INF/web.xml*.
