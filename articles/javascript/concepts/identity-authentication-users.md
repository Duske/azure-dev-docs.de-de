---
title: Authentifizierung und Autorisierung von JavaScript mit Azure
description: In diesem Artikel erfahren Sie, wie Sie JavaScript-Apps mit Identität, Authentifizierung und Benutzern mit Azure entwickeln.
ms.topic: reference
ms.date: 12/09/2020
ms.custom: devx-track-js
ms.openlocfilehash: b43ebd5a9c782b2abd83033a8ce0bee038655f80
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97529440"
---
# <a name="identity-authentication-and-users"></a>Identität, Authentifizierung und Benutzer

Authentifizierung und Autorisierung sind weitreichende Themen für eine Webanwendung, die auf spezifische programmgesteuerte Tasks und Benutzerinteraktionen mit einer Anwendung reduziert werden können. Dieser Artikel befasst sich mit den wichtigsten Konzepten, mit denen sich JavaScript-Entwickler häufig auseinandersetzen müssen. 

## <a name="authentication-with-azure"></a>Authentifizierung mit Azure

Die Authentifizierung ist die Fähigkeit einer Identität, Zugriff auf ein Objekt zu erhalten. 

In der Regel bezieht dies für einen JavaScript-Entwickler in Azure auf die folgenden Fähigkeiten:

* Das Gewähren des Zugriffs auf Azure-Ressourcen für ein Programm
* Das Gewähren des Zugriffs auf Ihre App für einen Benutzer, normalerweise nach einer Anmeldung

|Erforderlich|Perspektive|BESCHREIBUNG|
|--|--|--|
|Ja|Entwickler|Der Anwendungscode muss erforderliche Anmeldeinformationen an Azure übergeben, um auf Azure-Ressourcen zuzugreifen.|
|Nein|Benutzer|Für einen Benutzer einer Anwendung kann die Authentifizierung anonym erfolgen oder ein Benutzerkonto erfordern. Dieser eingeschränkte Zugriff kann einen beliebigen Authentifizierungsanbieter verwenden, einschließlich Microsoft. Alternativ können Sie eine eigene Authentifizierungsebene für Ihre Benutzer erstellen.|

## <a name="authentication-for-developers-to-azure-services"></a>Authentifizierung bei Azure-Diensten für Entwickler

Die programmgesteuerte Authentifizierung bei Azure erfordert gültige Anmeldeinformationen für den spezifischen Dienst, den der Code verwendet. Sie müssen die Schnellstartdokumentation für den Dienst lesen und wissen, welche Art von Anmeldeinformationen der Dienst erwartet. 

### <a name="local-developer-environment-for-authenticating-to-azure"></a>Lokale Entwicklerumgebung für die Authentifizierung bei Azure

Sobald Sie wissen, wie Sie eine Verbindung mit einem Dienst herstellen, sollten Sie einen Dienstprinzipal erstellen und den Dienstprinzipal auf eine Umgebungsvariable auf Ihrem Entwicklungscomputer festlegen. Durch diesen Schritt werden die direkte Interaktion zwischen Ihrem persönlichen Konto und Azure und das Risiko entfernt, dass Ihr persönliches Konto beim Einchecken der Anmeldeinformationen beim Quellcode kompromittiert wird. 

### <a name="cloud-apps-authenticating-to-azure"></a>Authentifizieren von Cloud-Apps bei Azure

Für cloudbasierte Apps bieten die Azure-Hostingdienste Zugriff auf die [Anwendungseinstellungen](../how-to/configure-web-app-settings.md), Umgebungsvariablen und Geheimnisse. Speichern Sie Geheimnisse in [Azure Key Vault](/azure/key-vault), und greifen Sie programmgesteuert über Ihre gehostete App auf diese Geheimnisse zu, um eine weitere Sicherheitsschicht zu Ihrer Web-App hinzuzufügen. 

## <a name="modern-programmatic-authentication-with-azureidentity"></a>Moderne programmgesteuerte Authentifizierung mit @azure/identity

Die aktuelle Azure SDK-Bibliothek verwendet einen Dienstprinzipal für die programmgesteuerte Authentifizierung bei Azure-Diensten mit dem npm-Paket [@azure/identity](https://www.npmjs.com/package/@azure/identity). Diese Authentifizierung vereinfacht den Prozess und ist in den [modernen Azure SDK-Paketen](https://www.npmjs.com/package/@azure/identity#client-libraries-supporting-authentication-with-azure-identity) verfügbar. 

```javascript
// The default credential first checks environment variables for configuration.
// If environment configuration is incomplete, it will try managed identity.

// Azure service to use
const { KeyClient } = require("@azure/keyvault-keys");

// Azure authentication library to access Azure service
const { DefaultAzureCredential } = require("@azure/identity");

// Azure SDK clients accept the credential as a parameter
const credential = new DefaultAzureCredential();

// Create authenticated client
const client = new KeyClient(vaultUrl, credential);

// Use service from authenticated client
const getResult = await client.getKey("MyKeyName");
```

## <a name="classic-programmatic-authentication"></a>Klassische programmgesteuerte Authentifizierung

Verwenden Sie eines der folgenden Pakete für die meisten anderen verwalteten Azure SDK-Bibliotheken: 

* [@azure/ms-rest-js](https://www.npmjs.com/package/@azure/ms-rest-js): für die Arbeit im Browser und in Node.js-Umgebungen
* [@azure/ms-rest-nodeauth](https://www.npmjs.com/package/@azure/ms-rest-nodeauth): bietet mehrere verschiedene Authentifizierungsmechanismen, einschließlich der interaktiven Authentifizierung, Dienstprinzipalauthentifizierung und Benutzer-/Kennwortauthentifizierung
* [@azure/ms-rest-browserauth](https://www.npmjs.com/package/@azure/ms-rest-browserauth): erfordert die Azure AD-App

Im folgenden Beispiel wird die Authentifizierung mit einem vom Dienst bereitgestellten Schlüssel und Endpunkt veranschaulicht.

```javascript
// Azure service to use
const { QnAMakerRuntimeClient } = require("@azure/cognitiveservices-qnamaker-runtime");

// Azure authentication library to access Azure service
const { CognitiveServicesCredentials } = require("@azure/ms-rest-azure-js");  
 
// QnA Maker runtime credentials
const QNAMAKER_KEY = process.env["QNAMAKER_KEY"];
const QNAMAKER_ENDPOINT = process.env["QNAMAKER_ENDPOINT"];
const KNOWLEDGEBASE_ID = process.env["QNAMAKER_KNOWLEDGE_BASE_ID"];

const cognitiveServicesCredentials = new CognitiveServicesCredentials(QNAMAKER_KEY);
const client = new QnAMakerRuntimeClient(cognitiveServicesCredentials, QNAMAKER_ENDPOINT);
const customHeaders = { Authorization: `EndpointKey ${QNAMAKER_KEY}` };

// A question you'd like to get a response for, from the knowledge base. For example
const question = "How are you?";

// Maximum number of answer to retreive
const top = 1;

// Find only answers that contain these metadata
const strictFilters = [{ name: "editorial", value: "chitchat" }];

client.runtime.generateAnswer( 
        KNOWLEDGEBASE_ID,
        { question, top, strictFilters },
        { customHeaders }
).then(result =>{
    console.log(JSON.stringify(result));

    // Sample Result
    // {
    //   answers: [
    //     {
    //       questions: [
    //         "How are you?",
    //         "How is your tuesday?"
    //       ],
    //       answer:
    //         ""I'm doing great, thanks for asking!",
    //       score: 100,
    //       id: 90,
    //       source:
    //         "qna_chitchat_Friendly.tsv",
    //       metadata: [{ name: "editorial", value: "chitchat" }],
    //       context: { isContextOnly: false, prompts: [] }
    //     }
    //   ],
    //   debugInfo: null,
    //   activeLearningEnabled: false
    // }

});

```

## <a name="user-authentication-with-an-app-registration"></a>Benutzerauthentifizierung mit einer App-Registrierung

MSAL (Microsoft Authentication Library, Microsoft-Authentifizierungsbibliothek) ist die empfohlene Bibliothek für die Webentwicklung für die Benutzerauthentifizierung. Die Bibliothek ist in mehreren [Sprachen und Frameworks](/azure/active-directory/develop/msal-overview#languages-and-frameworks) verfügbar.

Zur Verwendung dieser Bibliothek benötigt Ihre Web-App eine [App-Registrierung](/azure/active-directory/develop/quickstart-register-app) bei Microsoft. Die App-Registrierung enthält allgemeine Authentifizierungsinformationen wie Benutzerberechtigungen und die Umleitungs-URL. 

Weiteres Lernmaterial mit diesem Beispielprojekt finden Sie in diesem [MSAL-Schnellstart](/azure/active-directory/develop/quickstart-v2-javascript).

Einem Benutzer wird Zugriff auf Ihre App gewährt, wenn er sich bei Ihrer App anmeldet. Diese Berechtigung wird für den Benutzer gespeichert und kann verwaltet und widerrufen werden:

* Verwaltung der Benutzerberechtigungen für die App: [https://account.live.com/consent/manage](https://account.live.com/consent/manage)
* Verwaltung der Active Directory-Berechtigungen für die App: [https://myapplications.microsoft.com/](https://myapplications.microsoft.com/)

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren Ihrer Azure App Service-Instanz](../how-to/configure-web-app-settings.md)