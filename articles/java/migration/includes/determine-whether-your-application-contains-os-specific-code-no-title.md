---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 73f3893f22764280ed5d7c030e7c8eab7d2bf6b1
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82988792"
---
Wenn Ihre Anwendung Code mit Abhängigkeiten vom Hostbetriebssystem enthält, müssen Sie ihn umgestalten, um diese Abhängigkeiten zu beseitigen. Beispielsweise müssen Sie ggf. alle Vorkommen von `/` oder `\` in Dateisystempfaden durch [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) oder [`Paths.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-) ersetzen.
