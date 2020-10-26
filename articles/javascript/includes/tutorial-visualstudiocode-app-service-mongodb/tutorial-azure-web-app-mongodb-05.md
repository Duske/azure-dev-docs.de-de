---
title: include file tutorial-azure-web-app-mongodb-05.md
description: include file tutorial-azure-web-app-mongodb-05.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 9b8a718ec921050237f911ee4b5c3a47b6bbb45e
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183866"
---
Erstellen Sie in diesem Abschnitt des Tutorials eine cloudbasierte Datenbank, und verbinden Sie die Remote-App, um diese Clouddatenbank zu verwenden. 

## <a name="create-a-cosmos-database"></a>Erstellen einer Cosmos-Datenbank

Erstellen Sie eine Cosmos-Ressource, um eine cloudbasierte MongoDB-Datenbank zu hosten. 

1. Wählen Sie in Visual Studio Code im Menü auf der linken Seite das Symbol **Azure** aus, und wählen Sie dann den Abschnitt **Datenbanken** aus. 

    Wenn der Abschnitt **Datenbanken** nicht angezeigt wird, vergewissern Sie sich, dass Sie den Abschnitt im oberen Azure-Menü **...** aktiviert haben. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-azure-extension-select-database-section.png" alt-text="Partieller Screenshot des Remotecontainersymbols von Visual Studio Code"::: 

1. Wählen Sie im Abschnitt **Datenbanken** des Azure-Explorers Ihr Abonnement mit einem Rechtsklick aus, und wählen Sie dann **Server erstellen** aus.
1. Wählen Sie in der Befehlspalette **Neuen Azure-Datenbankserver erstellen** die Option **Azure Cosmos DB für MongoDB-API** aus. 
1. Befolgen Sie die Anweisungen anhand der folgenden Tabelle, um zu verstehen, wie Ihre Werte verwendet werden. Die Erstellung der Datenbank kann bis zu 15 Minuten dauern.

    |Eigenschaft|Wert|
    |--|--|
    |Geben Sie einen global eindeutigen **Kontonamen** für die neue Ressource ein.| Geben Sie einen Wert wie `cosmos-mongodb-YOUR-NAME` für Ihre Ressource ein. Ersetzen Sie `YOUR-NAME` durch Ihren Namen oder die eindeutige ID. Dieser eindeutige Name wird auch als Teil der URL verwendet, um in einem Browser auf die Ressource zuzugreifen.|
    |Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.|Wenn Sie eine Ressourcengruppe erstellen müssen, verwenden Sie eine Benennungskonvention, die den Besitzer, den Zweck und die Region identifiziert, z. B. `westus-cosmostutorial-joesmith`.|
    |Speicherort|Der Speicherort der Ressource. Wählen Sie für dieses Tutorial einen regionalen Standort in Ihrer Nähe aus.|

    > [!CAUTION]
    > Das Erstellen der Ressource kann bis zu 15 Minuten dauern.     

1. Zeigen Sie die neu erstellte Cosmos-Ressource im Explorer an. Es sind noch keine Datenbanken vorhanden. 
1. Klicken Sie im Azure-Datenbank-Explorer mit der rechten Maustaste auf den Ressourcennamen, und wählen Sie dann **Verbindungszeichenfolge kopieren** aus, um die Verbindungszeichenfolge zu kopieren. Sie benötigen diese später im Lernprogramm.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-databases-extenion-copy-connection-string.png" alt-text="Partieller Screenshot des Remotecontainersymbols von Visual Studio Code":::

## <a name="optional-use-new-cloud-database-in-local-environment"></a>Optional: Verwenden der neuen Clouddatenbank in der lokalen Umgebung

Um die neue Clouddatenbank verwenden zu können, muss die lokale Anwendung so geändert werden, dass die neue Verbindungszeichenfolge verwendet wird. 

1. Öffnen Sie in Visual Studio Code die Datei `.env`, und fügen Sie der neuen Verbindungszeichenfolge den Wert **DATABASE_URL** hinzu. 
1. Fügen Sie `&retrywrites=false` am Ende der Verbindungszeichenfolge hinzu, damit die Datenbank erstellt werden kann, wenn die Web-App zum ersten Mal ausgeführt wird. 
1. Führen Sie die Web-App lokal aus, ohne den Entwicklungscontainer zu verwenden, um sicherzustellen, dass die App eine Verbindung mit der Clouddatenbank herstellt. 

## <a name="use-new-cloud-database-in-remote-web-app"></a>Verwenden der neuen Clouddatenbank in einer Remote-Web-App

Die Verbindung mit der Datenbank wird mit einer Umgebungsvariablen namens `DATABASE_URL` festgelegt. Um die Remote-Web-App für die Verwendung dieser Umgebungsvariablen zu konfigurieren, müssen Sie diese Variable für die Remote-Web-App erstellen. 

1. Wählen Sie in Visual Studio Code im Azure App Service-Explorer den Web-App-Dienstknoten aus, und erweitern Sie ihn.
1.  Klicken Sie mit der rechten Maustaste auf den Knoten **Anwendungseinstellungen**, um die `DATABASE_URL`-Eigenschaft mit der Verbindungszeichenfolge für Ihre Azure Cosmos DB für MongoDB hinzuzufügen. Fügen Sie `&retrywrites=false` am Ende der Verbindungszeichenfolge hinzu, damit die Datenbank erstellt werden kann, wenn die Web-App zum ersten Mal ausgeführt wird. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-remote-web-app-application-settings.png" alt-text="Partieller Screenshot des Remotecontainersymbols von Visual Studio Code"::: 

1. Öffnen Sie die Remotewebsite in einem Browser, und verwenden Sie das Formular, um Daten hinzuzufügen und zu löschen. 

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren? 

### <a name="mongodb-connection-strings"></a>MongoDB-Verbindungszeichenfolgen
Das Erstellen der Datenbank bei der erstmaligen Ausführung des Codes erfordert möglicherweise Wiederholungen. Daher muss die Verbindungszeichenfolge die `&retrywrites=false`-Einstellung aufweisen. Wenn Sie mehr über dieses Problem erfahren möchten, beginnen Sie mit diesem [öffentlichen Problem (Nr. 1296)](https://github.com/microsoft/vscode-cosmosdb/issues/1296) auf GitHub. 