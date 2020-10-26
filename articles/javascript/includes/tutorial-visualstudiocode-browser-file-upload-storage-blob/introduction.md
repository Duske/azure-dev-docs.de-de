---
title: include file tutorial-azure-web-app-mongodb-00.md
description: include file tutorial-azure-web-app-mongodb-00.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: b9ab409c845b7690474eb47117df4401ecaf59cd
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344163"
---
Verwenden Sie in diesem Tutorial eine React-App, um eine Datei in ein Azure Storage-Blob hochzuladen. 

Die Programmieraufgaben wurden für Sie erledigt. Dieses Tutorial konzentriert sich auf die erfolgreiche Verwendung der lokalen und der Azure-Remoteumgebungen in Visual Studio Code mit Azure-Erweiterungen.

## <a name="top-tasks"></a>Hauptaufgaben

Dieses Tutorial enthält mehrere **wichtige Azure-Aufgaben** für JavaScript-Entwickler:

* Lokales Ausführen einer React-App mit Visual Studio Code
* Erstellen einer Storage-Ressource und Konfiguration für Dateiuploads
    * Konfigurieren von CORS
    * Erstellen eines SAS-Tokens (Shared Access Signatures)
* Konfigurieren von Code für die Azure SDK-Clientbibliothek zur Verwendung von SAS-Token für die Authentifizierung beim Dienst

## <a name="sample-application"></a>Beispielanwendung

Die [React-Beispiel-App](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob), die auf GitHub verfügbar ist, besteht aus den folgenden Elementen:

* **React-App**, gehostet an Port 3000
* Azure SDK-Clientbibliotheksskript zum Hochladen in Storage-Blobs

:::image type="content" source="../../media/tutorial-browser-file-upload/browser-react-app-azure-storage-resource-image-uploaded-displayed.png" alt-text="Einfache React-App, die mit Azure Storage-Blobs verbunden ist.":::
