---
title: Authentifizieren mit den Azure-Verwaltungsmodulen für Node.js
description: Authentifizieren mit einem Dienstprinzipal bei den Azure-Verwaltungsmodulen für Node.js
ms.topic: how-to
ms.date: 06/17/2017
ms.custom: devx-track-js
ms.openlocfilehash: 150b00c4dbb21d0514d1d7c7d34813272bbf06e1
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586117"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Authentifizieren mit den Azure-Verwaltungsmodulen für JavaScript

Es gibt zwei Gruppen von Verwaltungspaketen für Azure-Dienste, die Sie bei der Ressourcenverwaltung unterstützen.
- Azure SDK für Node.js
- Azure SDK für JavaScript

Das Azure SDK für Node.js ist die ältere Gruppe von Verwaltungspaketen für Azure-Dienste, für die Folgendes gilt: 
- Nur in Node.js und nicht in Browsern verwendbar
- Geschrieben in JavaScript mit von Hand geschriebenen Typdeklarationsdateien
- Nicht in der aktiven Entwicklung und zugunsten der Pakete des Azure SDK für JavaScript als veraltet markiert
- Namen von Paketen beginnen mit `azure-arm-`
- Paket [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) zur Erstellung von Anmeldeinformationen erforderlich, die dann an die Clientklassen in den Paketen übergeben werden können, um die Authentifizierung mit Azure Active Directory durchzuführen
- Live im Repository https://github.com/Azure/azure-sdk-for-node

Das Azure SDK für JavaScript ist die neuere Gruppe von Verwaltungspaketen für Azure-Dienste, für die Folgendes gilt:
- Sowohl in Node.js als auch in Browsern verwendbar
- In TypeScript geschrieben und sowohl in JavaScript- als auch in TypeScript-Projekten verwendbar
- In der aktiven Entwicklung und mit Bereitstellung von Updates, wenn die Ressourcenverwaltungs-APIs von Azure-Diensten aktualisiert werden
- Namen von Paketen beginnen mit `@azure/arm-`
- Paket [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) zur Erstellung von Anmeldeinformationen erforderlich, die dann an die Clientklassen in den Paketen übergeben werden können, um die Authentifizierung mit Azure Active Directory durchzuführen. Wenn Ihre Anwendung im Browser ausgeführt wird, verwenden Sie stattdessen [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth).
- Live im Repository https://github.com/Azure/azure-sdk-for-js

Die beiden Gruppen lassen sich ganz einfach durch einen Blick auf die Paketnamen unterscheiden.

Für alle Dienst-APIs ist bei der Instanziierung die Authentifizierung mit einem `credentials`-Objekt erforderlich. Es gibt mehrere Möglichkeiten, die Authentifizierung durchzuführen und die erforderlichen Anmeldeinformationen für Pakete im Azure SDK für Node.js und im Azure SDK für JavaScript zu erstellen.

Zu den gängigen Methoden zählen unter anderem folgende:

- Standardauthentifizierung unter Verwendung von Benutzername und Kennwort
- Interaktive Anmeldung. Dies ist die einfachste Authentifizierungsmethode, erfordert jedoch die Anmeldung mit einem Benutzerkonto.
- Dienstprinzipalauthentifizierung. Im Thema [Erstellen eines Azure-Dienstprinzipals mit Node.js](./node-sdk-azure-authenticate-principal.md) werden verschiedene Techniken zum Erstellen eines Dienstprinzipals beschrieben. 

Die Infodatei der folgenden Pakete enthält jeweils ausführliche Informationen zu den verschiedenen Methoden, mit denen Sie ein Anmeldeinformationsobjekt erhalten.
- [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) bei Verwendung eines Verwaltungspakets im Azure SDK für JavaScript in Node.js
- [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth) bei Verwendung eines Verwaltungspakets im Azure SDK für JavaScript in einem Browser
- [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) bei Verwendung eines Verwaltungspakets im älteren Azure SDK für Node.js

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="next-steps"></a>Nächste Schritte   

* [Bereitstellen einer statischen Website in Azure in Visual Studio Code](tutorial-vscode-static-website-node-01.md)