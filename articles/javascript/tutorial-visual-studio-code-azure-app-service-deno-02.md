---
title: Erstellen der Deno-Azure App Service-Instanz in Visual Studio Code
description: 'Teil 2 des Tutorials: Erstellen und lokales Ausführen der Deno-App'
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6440f75d11bd4eb68dfbcf4e1dcf6b2b27c0496f
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "90772923"
---
# <a name="test-local-deno-apps"></a>Testen lokaler Deno-Apps

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-visual-studio-code-azure-app-service-deno-01.md)

In diesem Schritt erstellen Sie eine einfache Deno-API mit dem integrierten Webserver von Deno. Sie führen die App anschließend lokal aus.

## <a name="create-and-run-a-local-deno-app"></a>Erstellen und Ausführen einer lokalen Deno-App

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

    ![Ausführen des Demoservers](media/deploy-azure/deno-hello-world.png)

    Sie können diesen Code auch ausführen, indem Sie `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts` eingeben.

1. Drücken Sie im Terminal **STRG**+**C**, um den Server zu beenden.

> [!div class="nextstepaction"]
> [Ich habe die Deno-App erstellt.](tutorial-visual-studio-code-azure-app-service-deno-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=create-app)

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
