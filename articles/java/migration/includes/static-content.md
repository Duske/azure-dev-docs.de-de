---
author: yevster
ms.author: yebronsh
ms.date: 4/10/2020
ms.openlocfilehash: c6f4f3b58ff7d5a8733a0f2df2ab0bf3c6eb6fe4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988728"
---
#### <a name="read-only-static-content"></a>Schreibgeschützter statischer Inhalt

Falls mit Ihrer Anwendung derzeit statischer Inhalt bereitgestellt wird, benötigen Sie dafür einen anderen Speicherort. Sie können beispielsweise erwägen, statischen Inhalt in Azure Blob Storage zu verschieben und Azure CDN hinzuzufügen, um global eine sehr hohe Downloadgeschwindigkeit zu erzielen. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website)und[Schnellstart: Integrieren eines Azure-Speicherkontos in Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Dynamisch veröffentlichter statischer Inhalt

Wenn Ihre Anwendung statischen Inhalt zulässt, der von Ihrer Anwendung hochgeladen bzw. produziert wird, nach der Erstellung aber unveränderlich ist, können Sie Azure Blob Storage und Azure CDN wie oben beschrieben nutzen. Hierbei können Sie auch eine Azure-Funktion zum Verarbeiten von Uploads und der CDN-Aktualisierung verwenden. Eine entsprechende Beispielimplementierung finden Sie unter [Hochladen und CDN-Vorabladen von statischem Inhalt mit Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).
