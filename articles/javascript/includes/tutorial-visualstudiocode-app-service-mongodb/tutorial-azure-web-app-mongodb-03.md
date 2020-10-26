---
title: include file tutorial-azure-web-app-mongodb-03.md
description: include file tutorial-azure-web-app-mongodb-03.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: adea87271b1332f77ab254530410787d1a9baa3c
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183863"
---
In diesem Abschnitt des Tutorials wird die Beispielanwendung in Azure bereitgestellt. Anschließend können Sie die remote ausgeführte App in Ihrem Browser anzeigen. 

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

[!INCLUDE [azure-sign-in](../azure-sign-in.md)]

## <a name="create-web-app-resource"></a>Erstellen einer Web-App-Ressource

Verwenden Sie die Visual Studio Code-Erweiterung, um eine App-Dienstressource zu erstellen und die Web-App für die Ressource bereitzustellen.

1. Navigieren Sie zum Azure-Explorer. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann `Create new web app...` aus.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Erstellen einer Web-App.":::

1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie Ihre Werte verwendet werden.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen Namen für die neue Web-App ein.| Geben Sie einen Wert wie `web-app-with-mongodb-YOUR-NAME` als App-Dienstressource ein. Ersetzen Sie `<YOUR-NAME>` durch Ihren Namen oder die eindeutige ID. Dieser eindeutige Name wird auch als Teil der URL verwendet, um in einem Browser auf die Ressource zuzugreifen.|
    |Wählen Sie eine Runtime für Ihre Linux-App aus.|Wählen Sie `Node 12 LTS`aus.|

1. Nachdem der App-Erstellungsprozess beendet ist, wird in der unteren rechten Ecke von Visual Studio Code eine Statusmeldung mit Auswahl `Deploy` oder `View output` angezeigt. Wählen Sie `Deploy` aus.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Erstellen einer Web-App.":::

    Wenn die Statusmeldung nicht mehr angezeigt wird, können Sie die Bereitstellung ausführen, indem Sie den Azure-Explorer auswählen, mit der rechten Maustaste auf den Ressourcennamen klicken und dann **In Web-App bereitstellen** auswählen.

1. Während des Bereitstellungsprozesses können Sie über eine Benachrichtigung auswählen, ob Sie das **Ausgabefenster** anzeigen möchten.  Dadurch wird der aktuelle Status der Bereitstellung angezeigt. 

1. Nachdem die Bereitstellung abgeschlossen wurde, wird eine Benachrichtigung angezeigt. Wählen Sie **Protokolle streamen** aus, um die aktuellen Protokolle anzuzeigen. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Erstellen einer Web-App.":::

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Erstellen einer Web-App.":::    

1. Öffnen Sie die Website in einem Browser, und ersetzen Sie den Text `YOUR-RESOURCE_NAME` durch ihren eigenen Ressourcennamen: `https://YOUR-RESOURCE_NAME.azurewebsites.net`.
    
    Die Website kann nun lokal und remote ausgeführt werden, stellt aber immer noch keine Verbindung mit der Datenbank her. 

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren?

Der anfängliche Webdienst ist so konfiguriert, dass er an Port 8080 ausgeführt wird und öffentlich verfügbar ist. Diese Arten von Websiteeinstellungen können konfiguriert werden.
* [App-Einstellungen](/azure/app-service/configure-common)
* [Authentifizierung](/azure/app-service/configure-authentication-provider-microsoft)
* [Einschränken des Zugriffs nach Netzwerk](/azure/azure/app-service/app-service-ip-restrictions)

Wenn Sie diese App Service-Erweiterung zum Bereitstellen Ihrer Website in der Azure-Cloud verwenden, möchten Sie vielleicht mehr darüber erfahren, wie Sie diese [Bereitstellung konfigurieren](https://github.com/microsoft/vscode-azureappservice/wiki/Configuring-Zip-Deployment#additional-zip-deploy-configuration-settings).