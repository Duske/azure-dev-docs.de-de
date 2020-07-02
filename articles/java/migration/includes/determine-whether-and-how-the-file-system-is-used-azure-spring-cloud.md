---
author: yevster
ms.author: yebronsh
ms.date: 5/26/2020
ms.openlocfilehash: 30d39530700806dc910c085c36a7834efa9c1414
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507601"
---
#### <a name="determine-whether-and-how-the-file-system-is-used"></a>Ermitteln, ob und wie das Dateisystem verwendet wird

Suchen Sie nach Fällen, in denen Ihre Dienste Daten in das lokale Dateisystem schreiben bzw. Daten daraus lesen. Ermitteln Sie, wo kurzlebige/temporäre Dateien geschrieben und gelesen und wo langlebige Dateien geschrieben und gelesen werden.

> [!NOTE]
> Pro Azure Spring Cloud-Instanz bietet Azure Spring Cloud 5 GB temporären Speicher (eingebunden in `/tmp`). Wenn die geschriebenen temporären Dateien über diesen Grenzwert hinausgehen oder an einen anderen Speicherort geschrieben werden, sind Codeänderungen erforderlich.

<!-- The following two "static content" sections should be identical to the contents of includes\static-content.md except that here we use H5 headings. -->

##### <a name="read-only-static-content"></a>Schreibgeschützter statischer Inhalt

Falls mit Ihrer Anwendung derzeit statischer Inhalt bereitgestellt wird, benötigen Sie dafür einen anderen Speicherort. Sie können beispielsweise erwägen, statischen Inhalt in Azure Blob Storage zu verschieben und Azure CDN hinzuzufügen, um global eine sehr hohe Downloadgeschwindigkeit zu erzielen. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website)und[Schnellstart: Integrieren eines Azure-Speicherkontos in Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

##### <a name="dynamically-published-static-content"></a>Dynamisch veröffentlichter statischer Inhalt

Wenn Ihre Anwendung statischen Inhalt zulässt, der von Ihrer Anwendung hochgeladen bzw. produziert wird, nach der Erstellung aber unveränderlich ist, können Sie Azure Blob Storage und Azure CDN wie oben beschrieben nutzen. Hierbei können Sie auch eine Azure-Funktion zum Verarbeiten von Uploads und der CDN-Aktualisierung verwenden. Eine entsprechende Beispielimplementierung finden Sie unter [Hochladen und CDN-Vorabladen von statischem Inhalt mit Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
