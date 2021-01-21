---
title: Architektur einer Maschinelles Sehen-App
description: In diesem Abschnitt des Tutorials werden die Client-App und der Bereitstellungsprozess erläutert.
ms.topic: tutorial
ms.date: 12/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: b8949b355ec30775dfacf5ea77db53edb595578c
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561646"
---
# <a name="2-application-architecture-for-static-web-app-with-computer-vision"></a>2. Anwendungsarchitektur für eine statische Web-App mit maschinellem Sehen

Hier erfahren Sie mehr über die Clientanwendung und den Bereitstellungsprozess.

## <a name="what-is-an-azure-static-web-app"></a>Was ist eine statische Azure-Web-App?

Bei der Erstellung statischer Web-Apps stehen in Azure mehrere Optionen zur Verfügung, um den gewünschten Grad an Funktionalität und Kontrolle zu erhalten. In diesem Tutorial wird der einfachste Dienst erstellt. Dabei werden Ihnen viele der Entscheidungen abgenommen, sodass Sie sich ganz auf Ihren Front-End-Code konzentrieren können und sich nicht mit der Hostingumgebung beschäftigen müssen.

## <a name="client-application-architecture"></a>Architektur der Clientanwendung

Die React-App (create-react-app) umfasst folgende Funktionen: 
* Anzeige einer Meldung, wenn Azure-Schlüssel und -Endpunkt für [**maschinelles Sehen**](/azure/cognitive-services/computer-vision/) von Cognitive Services nicht gefunden werden
* Möglichkeit zum Analysieren eines Bilds mit maschinellem Sehen von Cognitive Services
    * Eingabe einer öffentlichen Bild-URL oder Analyse eines Bilds aus einer Sammlung
    * Nach Abschluss der Analyse:
        * Anzeigebild
        * Anzeigen der JSON-Ergebnisse des maschinellen Sehens 

:::image type="content" source="../../media/static-web-app/browser-screenshot-react-computervision-app-image-analysis-result.png" alt-text="Screenshot: Browserausschnitt mit den Ergebnissen des React-Beispiels für maschinelles Sehen von Cognitive Services":::

## <a name="deploy-to-azure-with-github-action"></a>Bereitstellen in Azure mit GitHub-Aktion

Die GitHub-Aktion wird gestartet, wenn ein Pushvorgang an einen bestimmten Branch durchgeführt wird, und umfasst Folgendes:
* Einfügen von GitHub-Geheimnissen für den Schlüssel und den Endpunkt für maschinelles Sehen in den Build
* Erstellen des React-Clients (create-react-app)
* Verschieben der resultierenden Dateien in Ihre [**statische Azure-Web-App-Ressource**](/azure/static-web-apps)

> [!div class="nextstepaction"]
> [Herunterladen und Ausführen der React-App für die Bildanalyse mit Cognitive Services](run-the-react-cognitive-services-image-analyzer-app-locally.md)