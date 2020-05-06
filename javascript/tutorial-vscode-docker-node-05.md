---
title: Bereitstellen eines Containerimages für eine Node.js-App in Visual Studio Code
description: 'Teil 5 des Tutorials: Bereitstellen des Images in Azure App Service'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 487110258ed3302e781cfa24a5ae9f518ebb3bda
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80740667"
---
# <a name="deploy-the-image-to-azure-app-service"></a>Bereitstellen des Images in Azure App Service

[Vorheriger Schritt: Erstellen des App-Images](tutorial-vscode-docker-node-04.md)

In diesem Schritt stellen Sie das Image, das Sie in eine Registrierung in [Azure App Service](https://azure.microsoft.com/services/app-service/) gepusht haben, direkt über Visual Studio Code bereit.

## <a name="enable-admin-access-on-the-registry"></a>Aktivieren des Administratorzugriffs für die Registrierung

Damit Sie Ihr Image für eine Webanwendung bereitstellen können, müssen Sie für Ihre Registrierung im Azure-Portal den Administratorzugriff aktivieren.

1. Klicken Sie im **Docker-Explorer** mit der rechten Maustaste auf den Namen der Registrierung, und wählen Sie „Im Portal öffnen“ aus. 

    ![Befehl „Im Portal öffnen“ in VS Code](media/deploy-containers/open-in-portal.png)

    Dadurch wird Ihre Registrierung im Azure-Portal geöffnet.

1. Klicken Sie auf der Randleiste auf „Zugriffsschlüssel“, und legen Sie die Einstellung „Administratorbenutzer“ auf „Aktiviert“ fest.  
    
    ![Aktivieren der Einstellung „Administratorbenutzer“ im Azure-Portal](media/deploy-containers/access-keys.png)

## <a name="deploy-image"></a>Bereitstellen des Images

1. Erweitern Sie im **DOCKER**-Explorer unter **Registrierungen** die Knoten für Ihr Image, klicken Sie mit der rechten Maustaste auf `:latest`, und wählen Sie **Deploy Image to Azure App Service** (Image in Azure App Service bereitstellen) aus.

    ![Bereitstellen über den Explorer](media/deploy-containers/deploy-image-command.png)

1. Geben Sie Werte für die App Service-Instanz an, wenn Sie dazu aufgefordert werden:

    - Der Name muss innerhalb von Azure eindeutig sein.
    - Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. (Eine **Ressourcengruppe** ist im Wesentlichen eine benannte Sammlung der Ressourcen einer Anwendung in Azure.)
    - Wählen Sie einen vorhandenen App Service-Plan aus, oder erstellen Sie einen neuen Plan. (Ein **App Service-Plan** definiert die physischen Ressourcen, die die Website hosten. Sie können für dieses Tutorial einen Basic-Tarif oder einen kostenlosen Tarif verwenden.)

1. Nach Abschluss der Bereitstellung zeigt Visual Studio Code eine Benachrichtigung mit der Website-URL an:

    ![Meldung bei erfolgreicher Bereitstellung](media/deploy-containers/deploy-successful.png)

1. Sie können die Ergebnisse auch im Bereich **Ausgabe** von Visual Studio Code im Abschnitt **Docker** anzeigen:

    ![Ausgabe bei erfolgreicher Bereitstellung](media/deploy-containers/deploy-output.png)

1. Um die bereitgestellte Website aufzurufen, **klicken** Sie bei gedrückter+**STRG-TASTE** auf die URL im Bereich **Ausgabe**. Die neue App Service-Instanz wird auch in Visual Studio Code im **AZURE**-Explorer im Abschnitt **APP SERVICE** angezeigt. Dort können Sie mit der rechten Maustaste auf die Website klicken und **Website durchsuchen** auswählen.

> [!div class="nextstepaction"]
> [Ich habe meine Website in Azure bereitgestellt](tutorial-vscode-docker-node-06.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=deploy-app)
