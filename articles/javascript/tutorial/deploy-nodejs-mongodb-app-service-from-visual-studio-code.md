---
title: 'Bereitstellen einer Express.js/MongoDB-App mit VSCode: App Service/CosmosDB'
description: Verwenden Sie in diesem Tutorial eine Node.js-App mit einer MongoDB-Datenbank, indem Sie die native MongoDB-API verwenden. Stellen Sie die Node.js-Anwendung in Azure App Service (unter Linux) bereit, und überprüfen Sie dann, ob die gehostete App funktioniert.
ms.topic: tutorial
ms.date: 12/03/2020
ms.custom: scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 8c40801607e11a4b929f0bb76926122d26217805
ms.sourcegitcommit: 550b165d0b910f4ea9652d8401dd4fc93f057f05
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96610968"
---
# <a name="deploy-expressjs-mongodb-app-to-app-service-from-visual-studio-code"></a>Bereitstellen einer Express.js-MongoDB-App in App Service über Visual Studio Code

Stellen Sie die Express.js-Anwendung bereit. Diese stellt eine Verbindung mit MongoDB und Azure App Service (unter Linux) und einer CosmosDB-Instanz her. 

Die Programmieraufgaben wurden für Sie erledigt. Dieses Tutorial konzentriert sich auf die erfolgreiche Verwendung der lokalen und der Azure-Remoteumgebungen in Visual Studio Code mit Azure-Erweiterungen.

## <a name="top-tasks"></a>Hauptaufgaben

Dieses Tutorial enthält mehrere **wichtige Azure-Aufgaben** für JavaScript-Entwickler:

* Erstellen einer CosmosDB-Ressource zum Hosten der MongoDB-Datenbank
* Erstellen einer App Service-Ressource zum Hosten einer Express.js-App
* Bereitstellen einer Express-App in App Service

## <a name="sample-application"></a>Beispielanwendung

Die [Express.js-Beispiel-App](https://github.com/Azure-Samples/js-e2e-express-mongo) besteht aus den folgenden Elementen:

* **Express.js-Server** gehostet an Port 8080
* Einfache Engine für die **serverseitige React.js-Ansicht**
* **Native MongoDB-API**-Funktionen zum Einfügen, Löschen und Suchen von Daten

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::

## <a name="create-or-use-existing-azure-subscription"></a>Erstellen oder Verwenden eines vorhandenen Azure-Abonnements 

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)

## <a name="install-software"></a>Installieren von Software

- [Node.js 12 (LTS) und npm:](https://nodejs.org/en/download) Der Node.js-Paket-Manager, der auf dem lokalen Computer installiert ist
- [Visual Studio Code](https://code.visualstudio.com/) auf dem lokalen Computer installiert. 
- Visual Studio Code-Erweiterungen:
    - [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code (installiert aus Visual Studio Code).
    - [Azure-Datenbanken](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)

## <a name="create-a-cosmosdb-database-resource-for-mongodb"></a>Erstellen einer CosmosDB-Datenbankressource für MongoDB

Erstellen Sie zuerst eine Cosmos-Ressource, da dieser Vorgang einige Minuten in Anspruch nimmt. 

1. Wählen Sie in Visual Studio Code im Menü auf der linken Seite das Symbol **Azure** aus, und wählen Sie dann den Abschnitt **Datenbanken** aus. 

    Wenn der Abschnitt **Datenbanken** nicht angezeigt wird, vergewissern Sie sich, dass Sie den Abschnitt im oberen Azure-Menü **...** aktiviert haben. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-azure-extension-select-database-section.png" alt-text="Partieller Screenshot des Remotecontainersymbols von Visual Studio Code"::: 

1. Wählen Sie im Abschnitt **Datenbanken** des Azure-Explorers Ihr Abonnement mit einem Rechtsklick aus, und wählen Sie dann **Server erstellen** aus.
1. Wählen Sie in der Befehlspalette **Neuen Azure-Datenbankserver erstellen** die Option **Azure Cosmos DB für MongoDB-API** aus. 
1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie Ihre Werte verwendet werden. Die Erstellung der Datenbank kann bis zu 15 Minuten dauern.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen **Kontonamen** für die neue Ressource ein.| Geben Sie einen Wert wie `cosmos-mongodb-YOUR-NAME` für Ihre Ressource ein. Ersetzen Sie `YOUR-NAME` durch Ihren Namen oder die eindeutige ID. Dieser eindeutige Name wird auch als Teil der URL verwendet, um in einem Browser auf die Ressource zuzugreifen.|
    |Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.|Wenn Sie eine Ressourcengruppe erstellen müssen, verwenden Sie eine Benennungskonvention, die den Besitzer, den Zweck und die Region identifiziert, z. B. `westus-cosmostutorial-joesmith`.|
    |Speicherort|Der Speicherort der Ressource. Wählen Sie für dieses Tutorial einen regionalen Standort in Ihrer Nähe aus.|

    Das Erstellen der Ressource kann bis zu 15 Minuten dauern. Sie können den nächsten Abschnitt überspringen, wenn Sie wenig Zeit haben. Denken Sie jedoch daran, diesen Abschnitt in einigen Minuten durchzuarbeiten.

## <a name="get-cosmosdb-connection-string"></a>Abrufen der CosmosDB-Verbindungszeichenfolge

Klicken Sie im Azure-Datenbank-Explorer mit der rechten Maustaste auf den Ressourcennamen, und wählen Sie dann **Verbindungszeichenfolge kopieren** aus, um die Verbindungszeichenfolge zu kopieren. Sie benötigen diese später im Tutorial für die Umgebungsvariablendatei.

:::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-databases-extenion-copy-connection-string.png" alt-text="Express.js-Web-App-Formular und Datenergebnisse aus der lokalen MongoDB-Datenbank.":::

## <a name="download-and-run-the-sample-expressjs-app"></a>Herunterladen und Ausführen der Express.js-Beispiel-App

Die Express.js-Web-App wird für Sie bereitgestellt. Laden Sie die App herunter, installieren Sie die Abhängigkeiten, und führen Sie die App aus. 

1. [Laden Sie das gezippte GitHub-Repository](https://github.com/Azure-Samples/js-e2e-express-mongo.git) auf Ihren lokalen Computer herunter, und erweitern Sie es dann zu einem Ordner. 
1. Öffnen Sie den Ordner mit Visual Studio Code. Sie können entweder mit der rechten Maustaste auf den Ordner klicken und **Mit Code öffnen** auswählen oder die CLI-Entsprechung verwenden, wenn Sie sich im Ordner befinden:

    ```bash
    code .
    ```

1. Bearbeiten Sie die Umgebungsdatei `.env`, und fügen Sie die Verbindungszeichenfolgen-Eigenschaft für Ihre CosmosDB-Instanz als Wert der Eigenschaft `DATABASE_URL` hinzu. 

    ```bash
    ENVIRONMENT=development
    DATABASE_NAME=
    DATABASE_COLLECTION_NAME=
    DATABASE_URL=
    ```

1. Öffnen Sie in Visual Studio Code ein Terminalfenster, führen Sie die folgenden Befehle aus, um die Abhängigkeiten des Beispiels zu installieren, und starten Sie die Web-App.

    ```bash
    npm install && npm start
    ```

1. Zeigen Sie die Web-App auf dem lokalen Computer in einem Browser an.

    ```url
    http://localhost:8080/
    ```

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::

## <a name="create-web-app-resource-and-deploy-expressjs-app"></a>Erstellen einer Web-App-Ressource und Bereitstellen der Express.js-App

Verwenden Sie die Visual Studio Code-Erweiterung für App Service, um eine App-Dienstressource zu erstellen und die Express.js-Web-App für die Ressource bereitzustellen.

1. Navigieren Sie zum Azure-Explorer. Klicken Sie mit der rechten Maustaste auf das Abonnement, und wählen Sie dann `Create new web app...` aus.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/create-web-app-with-extension.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Erstellen einer Web-App.":::

1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie Ihre Werte verwendet werden.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen Namen für die neue Web-App ein.| Geben Sie einen Wert wie `web-app-with-mongodb-YOUR-NAME` als App-Dienstressource ein. Ersetzen Sie `<YOUR-NAME>` durch Ihren Namen oder die eindeutige ID. Dieser eindeutige Name wird auch als Teil der URL verwendet, um in einem Browser auf die Ressource zuzugreifen.|
    |Wählen Sie eine Runtime für Ihre Linux-App aus.|Wählen Sie `Node 12 LTS`aus.|

1. Nachdem der App-Erstellungsprozess beendet ist, wird in der unteren rechten Ecke von Visual Studio Code eine Statusmeldung mit Auswahl `Deploy` oder `View output` angezeigt. Wählen Sie `Deploy` aus.

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-extension-create-web-app-deploy-web-app.png" alt-text="Partieller Screenshot von Visual Studio Code und der Azure-App-Diensterweiterung zum Bereitstellen der Web-App sofort nach dem Erstellen der Web-App.":::

    Wenn die Statusmeldung nicht mehr angezeigt wird, können Sie die Bereitstellung ausführen, indem Sie den Azure-Explorer auswählen, mit der rechten Maustaste auf den Ressourcennamen klicken und dann **In Web-App bereitstellen** auswählen.

1. Während des Bereitstellungsprozesses können Sie über eine Benachrichtigung auswählen, ob Sie das **Ausgabefenster** anzeigen möchten.  Dadurch wird der aktuelle Status der Bereitstellung angezeigt. 

1. Nachdem die Bereitstellung abgeschlossen wurde, wird eine Benachrichtigung angezeigt. Wählen Sie **Protokolle streamen** aus, um die aktuellen Protokolle anzuzeigen. 

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-deployed.png" alt-text="Der Dienst wird bereitgestellt. „Protokolle streamen“.":::

    :::image type="content" source="../media/tutorial-end-to-end-app-cosmos/vscode-app-service-stream-logs.png" alt-text="Nachdem die Bereitstellung abgeschlossen wurde, wird eine Benachrichtigung angezeigt, die eine Option „Protokolle streamen“ enthält.":::    

1. Öffnen Sie die Website in einem Browser, und ersetzen Sie den Text `YOUR-RESOURCE_NAME` durch ihren eigenen Ressourcennamen: `https://YOUR-RESOURCE_NAME.azurewebsites.net`.
1. Verwenden Sie die Web-App, und fügen Sie ihr Elemente hinzu, oder entfernen Sie Elemente. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Nachdem Sie dieses Tutorial abgeschlossen haben, müssen Sie die beiden erstellten Ressourcen entfernen, um sicherzustellen, dass keine Gebühren anfallen. 

1. Verwenden Sie in Visual Studio Code den Azure-Explorer für Datenbanken, klicken Sie mit der rechten Maustaste auf die Ressource, und wählen Sie dann **Konto löschen...** aus.
1. Verwenden Sie in Visual Studio Code den Azure-Explorer für App Service, klicken Sie mit der rechten Maustaste auf die Ressource, und wählen Sie dann **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über App Service und CosmosDB:
* [Konfigurieren einer Node.js-App für Azure App Service](/azure/app-service/configure-language-nodejs?pivots=platform-linux)
* [Verbinden mit SSH](/azure/app-service/configure-linux-open-ssh-session)
* [Migrieren von Daten zu CosmosDB](/azure/dms/tutorial-mongodb-cosmos-db?toc=/azure/cosmos-db/toc.json?toc=/azure/cosmos-db/toc.json)
