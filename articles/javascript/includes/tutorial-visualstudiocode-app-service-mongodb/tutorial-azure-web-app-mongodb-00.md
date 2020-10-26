---
title: include file tutorial-azure-web-app-mongodb-00.md
description: include file tutorial-azure-web-app-mongodb-00.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: c0d904935992b54b63f45f76a022947375414c55
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183848"
---
Verwenden Sie in diesem Tutorial eine Node.js-App mit einer MongoDB-Datenbank, indem Sie die native MongoDB-API verwenden. Stellen Sie die Node.js-Anwendung in Azure App Service (unter Linux) bereit, und überprüfen Sie dann, ob die cloudbasierte App funktioniert. 

Die Programmieraufgaben wurden für Sie erledigt. Dieses Tutorial konzentriert sich auf die erfolgreiche Verwendung der lokalen und der Azure-Remoteumgebungen in Visual Studio Code mit Azure-Erweiterungen.

## <a name="top-tasks"></a>Hauptaufgaben

Dieses Tutorial enthält mehrere **wichtige Azure-Aufgaben** für JavaScript-Entwickler:

* Verwenden einer lokalen MongoDB-Datenbank
* Verwenden der App mit einem Container
* Bereitstellen der App in der Cloud
* Konfigurieren von in der Cloud gehosteten App-Einstellungen 
* Verbinden der lokalen App mit einer Remotedatenbank

## <a name="sample-application"></a>Beispielanwendung

Die [Node.js-Beispiel-App](https://github.com/Azure-Samples/js-e2e-express-mongo), die auf GitHub verfügbar ist, besteht aus den folgenden Elementen:

* **Express.js-Server** gehostet an Port 8080
* Einfache Engine für die **serverseitige React.js-Ansicht**
* **Native MongoDB-API**-Funktionen zum Einfügen, Löschen und Suchen von Daten

:::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Einfache Node.js-App, die mit der MongoDB-Datenbank verbunden ist.":::

## <a name="the-mongodb-connection"></a>Die MongoDB-Verbindung

Wenn die Datenbankverbindung nicht hergestellt werden kann, zeigt die App die folgende Meldung an: `No database found.`. Dies ist der Anfangszustands der App.

Nachdem die Datenbankverbindung hergestellt wurde, besteht die App aus zwei Textfeldern in einem Formular mit einer Schaltfläche „Submit“ (Senden) mit dem Inhalt der Mongo-Sammlung, die unter dem Formular angezeigt wird.

## <a name="limited-time-to-work-on-the-tutorial"></a>Nur begrenzte Zeit zum Durcharbeiten des Tutorials verfügbar?

In diesem Tutorial erstellen Sie eine Azure-Ressource für Cosmos DB. Auf diese Weise hostet Azure MongoDB-Datenbanken. Dieser Ressourcenerstellungsvorgang kann bis zu 20 Minuten dauern. Sie können [diesen Prozess jetzt starten](../../tutorial-visualstudiocode-app-service-mongodb/tutorial-azure-web-app-mongodb-feedback.yml?tutorial-step=5), wenn Ihre Zeit begrenzt ist. Die Ressource sollte dann verfügbar sein, wenn Sie sie benötigen. 

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren? 

Jeder Schritt des Tutorials enthält einen Abschnitt **Möchten sie mehr erfahren?** Dabei handelt es sich um _optionale Informationen_, mit denen Sie Ihr Wissen vertiefen können. Sie können diese Informationen lesen, während Sie das Tutorial durcharbeiten, oder später zum Tutorial zurückkehren. 