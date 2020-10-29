---
title: Authentifizieren mit den Azure-Verwaltungsmodulen für Node.js
description: Authentifizieren mit einem Dienstprinzipal bei den Azure-Verwaltungsmodulen für Node.js
ms.topic: how-to
ms.date: 09/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: 084a7154bf52314886c0dfb57db3e9c879c8e2c0
ms.sourcegitcommit: c3a1c9051b89870f6bfdb3176463564963b97ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437186"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Authentifizieren mit den Azure-Verwaltungsmodulen für JavaScript

Für alle [SDK-Clientbibliotheken](../azure-sdk-library-package-index.md) ist bei der Instanziierung die Authentifizierung mit einem `credentials`-Objekt erforderlich. Es gibt mehrere Möglichkeiten, die erforderlichen Anmeldeinformationen zu authentifizieren und zu erstellen.

Allgemeine Methoden zum Erstellen der erforderlichen Anmeldeinformationen:

- Die **Dienstprinzipalauthentifizierung** ist die _empfohlene Methode_ . Informieren Sie sich, wie Sie [einen Azure-Dienstprinzipal erstellen](node-sdk-azure-authenticate-principal.md). 
- **Interaktive Anmeldung:** Dies ist die einfachste Authentifizierungsmethode, erfordert jedoch die Anmeldung mit einem Benutzerkonto und einem Browser.
- **Standardauthentifizierung** mit Benutzername und Kennwort. Diese Option bietet die geringste Sicherheit. 

## <a name="samples"></a>Beispiele

|Authentifizierungspaket|Beispielauthentifizierungsskripts|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(empfohlen)|[Dienstprinzipal mit Zertifikat](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[Dienstprinzipal aus Datei](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[Interactive](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[Grundlegend](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(empfohlen)|[Authentifizierung mit Popupelement (create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[React ohne Popupelement](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[HTML mit Anmeldeschaltfläche](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[Dienstprinzipal](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[Interactive](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[Grundlegend](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

[!INCLUDE [chrome-note](../includes/chrome-note.md)]

## <a name="next-steps"></a>Nächste Schritte   

* [Bereitstellen einer statischen Website in Azure in Visual Studio Code](../tutorial-vscode-static-website-node-01.md)