---
title: Authentifizieren mit den Azure-Verwaltungsmodulen für Node.js
description: Authentifizieren mit einem Dienstprinzipal bei den Azure-Verwaltungsmodulen für Node.js
ms.topic: how-to
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: b0e39b14e8f96c58e1e5fb4f2bbf12f07180db8b
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699908"
---
# <a name="authenticate-with-the-azure-management-modules-for-javascript"></a>Authentifizieren mit den Azure-Verwaltungsmodulen für JavaScript

Bei allen [SDK-Clientbibliotheken](../azure-sdk-library-package-index.md) ist eine Authentifizierung per Objekt vom Typ `credentials` erforderlich. Es gibt mehrere Möglichkeiten, die erforderlichen Anmeldeinformationen zu authentifizieren und zu erstellen.

Wie andere Software und Dienste auch, wurde die Authentifizierung im Laufe der Jahre immer weiter verbessert. Es ist wichtig, dass Sie wissen, welche Authentifizierungsbibliothek von Ihren Diensten verwendet wird. 

Die Authentifizierungsbibliotheken umfassen Folgendes:

* @azure/identity: Neuestes Authentifizierungspaket
* @azure/ms-rest-nodeauth
* @azure/ms-rest-browserauth

Es werden auch noch ältere Authentifizierungspakete genutzt. Bei Verwendung dieser Pakete sollten Sie von den älteren Authentifizierungsmethoden zu neueren Vorgehensweisen migrieren, um die Sicherheit und Stabilität zu erhöhen. 

## <a name="best-practices-with-azure-sdk-client-library-authentication"></a>Bewährte Methoden für die Authentifizierung über die Azure SDK-Clientbibliothek

Bei jedem npm-Paket ist die Clientbibliothek für die Authentifizierung genau angegeben. Es ist nicht ratsam, Authentifizierungspakete und Code zu mischen, sofern auf der Seite des npm-Pakets hierfür nicht generell dasselbe Authentifizierungspaket verwendet wird. 

## <a name="azure-identity-library"></a>Azure Identity-Bibliothek

Die Azure Identity-Bibliothek ist das neueste Authentifizierungspaket für Azure. Sehen Sie sich die [Liste mit den unterstützten Bibliotheken](https://www.npmjs.com/package/@azure/identity#client-libraries-supporting-authentication-with-azure-identity) für Azure Identity an.

Mit der [@azure/identity](https://www.npmjs.com/package/@azure/identity)-Bibliothek wird die Authentifizierung bei Azure Active Directory für Azure SDK-Bibliotheken vereinfacht. Sie enthält TokenCredential-Implementierungen, die für die Authentifizierung von API-Anforderungen an SDK-Bibliotheken übergeben werden können. Die Bibliothek unterstützt die Tokenauthentifizierung über einen Azure Active Directory-Dienstprinzipal oder eine verwaltete Identität.

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { BlobServiceClient } = require("@azure/storage-blob");
 
// Enter your storage account name
const account = "<account>";
const defaultAzureCredential = new DefaultAzureCredential();
 
const blobServiceClient = new BlobServiceClient(
  `https://${account}.blob.core.windows.net`,
  defaultAzureCredential
);
```

Der obige JavaScript-Beispielcode zeigt, wie Sie unter Verwendung der Azure-Identitätsbibliothek Azure-Standardanmeldeinformationen erstellen und diese anschließend für den Zugriff auf eine Azure Storage-Ressource verwenden.

## <a name="azure-ms-rest--libraries"></a>ms-rest-*-Azure-Bibliotheken
Bei den modernen, auf `@azure` ausgerichteten [Clientbibliotheken](../azure-sdk-library-package-index.md#modern-javascripttypescript-libraries) benötigen Sie ein Token, um einen Dienst verwenden zu können. Sie erhalten das Token, indem Sie eine Azure SDK-Clientauthentifizierungsmethode verwenden, bei der Anmeldeinformationen zurückgegeben werden. 

```javascript
const msRestNodeAuth = require("@azure/ms-rest-nodeauth");
msRestNodeAuth.interactiveLogin().then((credential) => {
    // service code goes here
}).catch((err) => {
    // error code goes here
    console.error(err);
});
```

Der obige JavaScript-Beispielcode zeigt, wie Sie die moderne Azure-Authentifizierungsbibliothek mit einer interaktiven Anmeldung verwenden, um Anmeldeinformationen zu erhalten.

```javascript
// service code - this is an example only and not best practices for code flow
const { BlobServiceClient } = require('@azure/storage-blob');
const billingManagementClient = new billing.BillingManagementClient(credential, subscriptionId);
billingManagementClient.enrollmentAccounts.list().then((enrollmentList) => {
    console.log("The result is:");
    console.log(result);
})
```

Der obige JavaScript-Beispielcode zeigt, wie Sie diese Anmeldeinformationen an eine bestimmte Clientbibliothek eines Azure-Diensts übergeben (beispielsweise an den Storage-Dienst aus dem nächsten Codebeispiel). Anhand der Anmeldeinformationen generiert die Clientbibliothek ein Token für Sie. Das Token wird vom Dienst verwendet, um die Authentifizierung auf Dienstebene für Ihre Anforderungen zu validieren. 

Die Clientbibliothek verwaltet das Token und führt die Aktualisierung dafür durch, wenn dies erforderlich ist. Sie als Entwickler mit Ihrer Codebasis müssen dies nicht verwalten.

## <a name="older-azure-sdk-client-authentication"></a>Authentifizierung für ältere Azure SDK-Clients 

Ältere Azure SDK-Clients werden nach und nach auf die oben beschriebene moderne Authentifizierung umgestellt. Bis zu dieser Umstellung werden für die älteren Clientbibliotheken verschiedene Authentifizierungsclients eingesetzt, oder es wird ein völlig anderer Authentifizierungsmechanismus verwendet (z. B. Ressourcenschlüssel). 

Optimale Ergebnisse mit älteren Clientbibliotheken erzielen Sie wie folgt: 
* Bei jedem npm-Paket ist die Clientbibliothek für die Authentifizierung genau angegeben.  
* Falls Ihr aktueller Code die modernen Bibliotheken vom Typ `@azure/ms-*` sowie die älteren Authentifizierungsbibliotheken in der gleichen Codebasis enthält, gilt Folgendes:
    * Stellen Sie sicher, dass es sich bei der älteren, nicht auf Azure ausgerichteten Bibliothek um die neueste Bibliothek für Ihren Dienst handelt. Dies ist in der Dokumentation des Diensts angegeben. 
    * Wenn Sie weiterhin eine Mischung aus modernen und älteren Authentifizierungsbibliotheken verwenden möchten, müssen für die ältere Bibliothek ggf. Ablauf- und Aktualisierungsfunktionen für Anmeldeinformationen bereitgestellt werden, um sie auf die Anwendungslogik in Ihrer Codebasis abzustimmen. 

## <a name="authentication-with-azure-services-while-developing"></a>Authentifizierung für Azure-Dienste bei der Entwicklung

Häufige Methoden zum Erstellen der erforderlichen Anmeldeinformationen während der Entwicklung sind:

| Azure-Authentifizierungstyp|Zweck|
|--|--|
|**Dienstprinzipal**|Diese Art der Authentifizierung ist die _empfohlene Methode_. Informieren Sie sich, wie Sie [einen Azure-Dienstprinzipal erstellen](node-sdk-azure-authenticate-principal.md). Mit einem Dienstprinzipal können Sie eine Verbindung mit Azure herstellen, die von Ihrem persönlichen Azure-Konto getrennt ist. Hierbei kann es sich um ein temporäres Konto oder auch ein Langzeitkonto handeln, das anstelle Ihres persönlichen Kontos genutzt wird.|
| **Interactive**| Dies ist die einfachste Authentifizierungsmöglichkeit beim Ausprobieren von Azure-Diensten. Hierfür müssen Sie sich über einen Browser bei Ihrem persönlichen Konto anmelden. |
|**Grundlegend**|Bei dieser Authentifizierung müssen Sie Ihren persönlichen Benutzernamen und das Kennwort eingeben. Da dies die am wenigsten sichere Methode ist, ist ihre Nutzung nicht zu empfehlen.| 

## <a name="authentication-with-azure-services-and-production-code"></a>Authentifizierung mit Azure-Diensten und Produktionscode

Häufige Methoden zum Erstellen der erforderlichen Anmeldeinformationen in Ihrem Produktionscode sind:

|Azure-Authentifizierungstyp|Zweck|
|--|--|
|**Verwaltete Dienstidentität (Managed Service Identity, MSI)**|Die [MSI-Authentifizierung](/azure/active-directory/managed-identities-azure-resources/overview) ist am besten für Produktionsszenarien geeignet. Sie nutzen diese Vorgehensweise nicht in Ihrer lokalen Entwicklungsumgebung. Informationen zu den Diensten, die MSI unterstützen, finden Sie [hier](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).|
|**Zertifikate**|[Zertifikate](/azure/cloud-services/cloud-services-certs-create) müssen über das [Portal](/azure/cloud-services/cloud-services-configure-ssl-certificate-portal) in Azure hochgeladen werden.|

## <a name="javascript-authentication-samples-for-azure"></a>Beispiele für die JavaScript-Authentifizierung für Azure

|Authentifizierungspaket|Beispielauthentifizierungsskripts|
|--|--|
|[@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth) <br>(empfohlen)|[Dienstprinzipal mit Zertifikat](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpCert.ts)<br>[Dienstprinzipal aus Datei](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/authFileWithSpSecret.ts)<br>[Interactive](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/interactivePersonalAccount.ts)<br>[Grundlegend](https://github.com/Azure/ms-rest-nodeauth/blob/master/samples/usernamePassword.ts)|
|[@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth)<br>(empfohlen)|[Authentifizierung mit Popupelement (create-react-app)](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/authentication-with-popup)<br>[React ohne Popupelement](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/react-app)<br>[HTML mit Anmeldeschaltfläche](https://github.com/Azure/ms-rest-browserauth/tree/master/samples/vanilla)|
|[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure)|[Dienstprinzipal](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#service-principal-authentication)<br>[Interactive](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#interactive-login)<br>[Grundlegend](https://github.com/Azure/azure-sdk-for-node/blob/master/Documentation/Authentication.md#basic-authentication)|

## <a name="next-steps"></a>Nächste Schritte   

* [Azure-npm-Pakete](../azure-sdk-library-package-index.md)
* [Dokumentation zu Azure-npm-Paketen](/javascript/api/overview/azure/)
