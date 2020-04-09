---
title: Bereitstellen eines Containerimages für eine Node.js-App in Visual Studio Code
description: 'Teil 4 des Tutorials: Bereitstellen des Images in Azure App Service'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 8fe8024adca9edda2142dc6582b6456b77ea4b8f
ms.sourcegitcommit: a65fa8dbb168bd39e225a293d9ee73d18ece1864
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362776"
---
# <a name="deploy-the-image-to-azure-app-service"></a>Bereitstellen des Images in Azure App Service

[Vorheriger Schritt: Erstellen des App-Images](tutorial-vscode-docker-node-03.md)

In diesem Schritt stellen Sie das Image, das Sie in eine Registrierung in [Azure App Service](https://azure.microsoft.com/services/app-service/) gepusht haben, direkt über Visual Studio Code bereit.

1. Erweitern Sie im **DOCKER**-Explorer unter **Registrierungen** die Knoten für Ihr Image, klicken Sie mit der rechten Maustaste auf `:latest`, und wählen Sie **Deploy Image to Azure App Service** (Image in Azure App Service bereitstellen) aus.

    ![Bereitstellen über den Explorer](media/deploy-containers/deploy-image-command.png)

1. Geben Sie Werte für die App Service-Instanz an, wenn Sie dazu aufgefordert werden:

    - Der Name muss innerhalb von Azure eindeutig sein.
    - Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. (Eine **Ressourcengruppe** ist im Wesentlichen eine benannte Sammlung der Ressourcen einer Anwendung in Azure.)
    - Wählen Sie einen vorhandenen App Service-Plan aus, oder erstellen Sie einen neuen Plan. (Ein **App Service-Plan** definiert die physischen Ressourcen, die die Website hosten. Sie können für dieses Tutorial einen Basic-Tarif oder einen kostenlosen Tarif verwenden.)
    - Wählen Sie einen Tarif für den neuen App Service-Plan aus.
    - Wählen Sie einen Standort (in Ihrer Nähe) für neue Ressourcen aus.

1. Nach Abschluss der Bereitstellung zeigt Visual Studio Code eine Benachrichtigung mit der Website-URL an:

    ![Meldung bei erfolgreicher Bereitstellung](media/deploy-containers/deploy-successful.png)

1. Sie können die Ergebnisse auch im Bereich **Ausgabe** von Visual Studio Code im Abschnitt **Docker** anzeigen:

    ![Ausgabe bei erfolgreicher Bereitstellung](media/deploy-containers/deploy-output.png)

1. Um die bereitgestellte Website aufzurufen, **klicken** Sie bei gedrückter+**STRG-TASTE** auf die URL im Bereich **Ausgabe**. Die neue App Service-Instanz wird auch in Visual Studio Code im **AZURE**-Explorer im Abschnitt **APP SERVICE** angezeigt. Dort können Sie mit der rechten Maustaste auf die Website klicken und **Website durchsuchen** auswählen.

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt](tutorial-vscode-docker-node-05.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=deploy-app)
