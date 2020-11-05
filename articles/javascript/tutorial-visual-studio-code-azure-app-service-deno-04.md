---
title: Bereinigen von Ressourcen nach dem Bereitstellen in Azure App Service
description: 'Teil 4 des Deno-Tutorials: Bereinigen von Ressourcen'
ms.topic: tutorial
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 307f0d344324312f9b93f4f60e3c312b9d415941
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233400"
---
# <a name="clean-up"></a>Bereinigung

[Vorheriger Schritt: Bereitstellen der App](tutorial-visual-studio-code-azure-app-service-deno-03.md)

In diesem Abschnitt entfernen und bereinigen Sie alle erstellten Ressourcen.

## <a name="remove-your-resources"></a>Entfernen Ihrer Ressourcen

Die von Ihnen erstellte App Service-Instanz enthält einen zugrunde liegenden App Service-Plan, der im Free-Tarif ausgeführt wird. Daher fallen hierfür keine laufenden Kosten an.

Gehen Sie wie folgt vor, wenn Sie die Ressourcen bereinigen möchten: Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** und dann die Ressourcengruppe aus, die in diesem Tutorial erstellt wurde (z. B. `deno-quickstart`), und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Ich bin fertig.](node-howto-deploy-web-app.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
