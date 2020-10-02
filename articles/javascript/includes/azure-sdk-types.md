---
ms.custom: devx-track-js
ms.topic: include
ms.date: 09/30/2020
ms.openlocfilehash: 36265a01dc58f4c21d23feea900ea53fba8c8846
ms.sourcegitcommit: 0b1c751c5a4a837977fec1c777bca5ad15cf2fc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91621680"
---
## <a name="types-of-sdk-client-libraries"></a>Arten von SDK-Clientbibliotheken

Es gibt zwei Sätze von Verwaltungspaketen für Azure-Dienste, die Sie bei der Ressourcenverwaltung unterstützen.

|Feature|Azure SDK für JavaScript<br>(empfohlen, modern)|Azure SDK für Node.js<br>(älter)|
|--|--|--|
|In der aktiven Entwicklung|✔️|❌|
|Verwendung in Node.js|✔️|✔️|
|Verwendung im Browser|✔️|❌|
|Verwendung in JavaScript|✔️|✔️|
|Verwendung in TypeScript<br>(.d.ts enthalten)|✔️|❌|
|Authentifizierungsbibliothek<br>[Beispiele](../node-sdk-azure-authenticate.md)|Nicht-Browser: [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth)<br>Browser: [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)|Nicht-Browser: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|
|GitHub-Repository|[Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js)|[Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node)|
