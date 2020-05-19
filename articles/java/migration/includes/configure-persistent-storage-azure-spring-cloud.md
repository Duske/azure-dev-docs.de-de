---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 6fdf908458eec428f4e01d1b5f20fcbf4e039dbe
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990222"
---
### <a name="configure-persistent-storage"></a>Konfigurieren von persistentem Speicher

Wenn ein Teil Ihrer Anwendung aus dem lokalen Dateisystem liest oder in das lokale Dateisystem schreibt, müssen Sie beständigen Speicher konfigurieren, um das lokale Dateisystem zu ersetzen. Weitere Informationen finden Sie unter [Verwenden von beständigem Speicher in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-persistent-storage).

Temporäre Dateien sollten in das Verzeichnis `/tmp` geschrieben werden. Dieses Verzeichnis kann unabhängig vom Betriebssystem mithilfe von `System.getProperty("java.io.tmpdir")` abgerufen werden. Sie können temporäre Dateien auch mithilfe von [`java.nio.Files::createTempFile`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/nio/file/Files.html#createTempFile(java.lang.String,java.lang.String,java.nio.file.attribute.FileAttribute...)) erstellen.
