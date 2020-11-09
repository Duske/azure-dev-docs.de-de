---
title: Include-Datei 2
description: Include-Datei 2
ms.topic: include
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 05f3e5fb847e7589e41d041736c986dce0bc5b29
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278678"
---
In diesem Schritt erstellen Sie eine einfache Deno-API mit dem integrierten Webserver von Deno. Sie führen die App anschließend lokal aus.

1. Navigieren Sie in einem Terminal oder an einer Eingabeaufforderung zu einem Speicherort, an dem Sie den App-Ordner erstellen möchten, und erstellen Sie einen neuen Ordner namens `deno-demo`.

1. Erstellen Sie eine neue Datei namens `demo.ts`.
1. Von Deno wird die direkte Ausführung von Code aus URLs akzeptiert. Schreiben Sie einen HTTP-Server, der alle Anforderungen mit „Hello World“ beantwortet. Verwenden Sie den folgenden Code:

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. Führen Sie die App aus, indem Sie das folgende Skript ausführen:

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. Testen Sie die App, indem Sie `http://localhost:80` in einem Browser öffnen. Die Website sollte wie folgt angezeigt werden:

    ![Ausführen des Demoservers](../../media/deploy-azure/deno-hello-world.png)

    Sie können diesen Code auch ausführen, indem Sie `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts` eingeben.

1. Drücken Sie im Terminal **STRG**+**C** , um den Server zu beenden.
