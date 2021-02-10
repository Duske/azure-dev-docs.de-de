---
title: Azure-Authentifizierung mit Benutzeranmeldeinformationen
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit Authentifizierung von Anwendungen mit Benutzeranmeldeinformationen
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: d2b5b24b0f39d56ca2235ec56a4ea56c4be6b185
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528523"
---
# <a name="azure-authentication-with-user-credentials"></a>Azure-Authentifizierung mit Benutzeranmeldeinformationen

In diesem Artikel wird erläutert, wie die Azure-Identitätsbibliothek Azure Active Directory-Tokenauthentifizierung mit vom Benutzer bereitgestellten Anmeldeinformationen unterstützt. Diese Unterstützung wird durch eine Reihe von TokenCredential-Implementierungen ermöglicht, die im Folgenden erläutert werden.

In diesem Artikel werden die folgenden Themen behandelt:

* [Gerätecodeanmeldeinformationen](#device-code-credential)
* [Interaktive Browseranmeldeinformationen](#interactive-browser-credential)
* [Anmeldeinformationen mit Benutzername/Kennwort](#username-password-credential)

## <a name="device-code-credential"></a>Gerätecodeanmeldeinformationen

Die Gerätecodeanmeldeinformationen authentifizieren einen Benutzer interaktiv auf Geräten mit eingeschränkter Benutzeroberfläche. Dies funktioniert, indem der Benutzer aufgefordert wird, eine Anmelde-URL auf einem für Browser aktivierten Computer zu besuchen, wenn die Anwendung versucht, sich zu authentifizieren. Der Benutzer gibt dann den in den Anweisungen genannten Gerätecode zusammen mit seinen Anmeldeinformationen ein. Bei erfolgreicher Authentifizierung wird die Anwendung, die Authentifizierung angefordert hat, auf dem Gerät, auf dem sie ausgeführt wird, erfolgreich authentifiziert.

Weitere Informationen finden Sie unter [Microsoft Identity Platform und der OAuth 2.0-Flow für Geräteautorisierungsgenehmigung](/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="enable-applications-for-device-code-flow"></a>Aktivieren von Anwendungen für Gerätecodeflow

Führen Sie die folgenden Schritte aus, um einen Benutzer über Gerätecodeflow zu authentifizieren:

1. Navigieren Sie im Azure-Portal zu Azure Active Directory, und suchen Sie nach Ihrer App-Registrierung.
2. Navigieren Sie zum Abschnitt **Authentifizierung**.
3. Überprüfen Sie unter **Vorgeschlagene umgeleitete URIs** den URI, der mit `/common/oauth2/nativeclient` endet.
4. Wählen Sie unter **Standardclienttyp** für `Treat application as a public client` die Option `yes` aus.

Mit diesen Schritten wird die Anwendung authentifiziert, sie verfügt aber immer noch nicht über die Berechtigung, Sie bei Active Directory anzumelden oder auf Ressourcen in Ihrem Namen zuzugreifen. Um dieses Problem zu beheben, navigieren Sie zu **API-Berechtigungen**, und aktivieren Sie Microsoft Graph sowie die Ressourcen, auf die Sie zugreifen möchten, z. B. Azure Service Management, Key Vault usw.

Sie müssen auch Administrator Ihres Mandanten sein, um Ihrer Anwendung Einwilligung zu erteilen, wenn Sie sich zum ersten Mal anmelden.

Wenn Sie die Option für Gerätecodeflow für Ihr Active Directory nicht konfigurieren können, muss Ihre App ggf. mehrinstanzenfähig sein. Wenn Sie Ihre App mehrinstanzenfähig machen möchten, navigieren Sie zum Bereich **Authentifizierung** und wählen dann **Konten in einem beliebigen Organisationsverzeichnis** aus. Wählen Sie dann für *Anwendung als öffentlichen Client behandeln* die Option **Ja** aus.

### <a name="authenticate-a-user-account-with-device-code-flow"></a>Authentifizieren eines Benutzerkontos mit dem Gerätecodeflow

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [Azure Key Vault Secret-Clientbibliothek für Java][secrets_client_library] mithilfe von `DeviceCodeCredential` auf einem IoT-Gerät authentifiziert wird.

```java
/**
 * Authenticate with device code credential.
 */
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
    .challengeConsumer(challenge -> {
    // Lets the user know about the challenge.
    System.out.println(challenge.getMessage());
    }).build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(deviceCodeCredential)
    .buildClient();
```

## <a name="interactive-browser-credential"></a>Interaktive Browseranmeldeinformationen

Diese Anmeldeinformationen authentifizieren einen Benutzer interaktiv mit dem Standardsystembrowser und bieten eine reibungslose Authentifizierungserfahrung, da Sie Ihre eigenen Anmeldeinformationen zum Authentifizieren Ihrer Anwendung verwenden können.

### <a name="enable-applications-for-interactive-browser-oauth-2-flow"></a>Aktivieren von Anwendungen für interaktiven OAuth 2-Browserflow

Wenn Sie `InteractiveBrowserCredential` verwenden möchten, müssen Sie eine Anwendung in Azure Active Directory mit Berechtigungen zum Anmelden im Namen eines Benutzers registrieren. Führen Sie die oben genannten Schritte für den Gerätecodeflow aus, um Ihre Anwendung zu registrieren. Wie bereits erwähnt, muss ein Administrator Ihres Mandanten Ihrer Anwendung Einwilligung erteilen, bevor sich ein Benutzerkonto anmelden kann.

Sie werden feststellen, dass in `InteractiveBrowserCredentialBuilder` eine Umleitungs-URL erforderlich ist. Fügen Sie die Umleitungs-URL dem Unterabschnitt **Umleitungs-URIs** unter dem Abschnitt **Authentifizierung** Ihrer registrierten AAD-Anwendung hinzu.

### <a name="authenticate-a-user-account-interactively-in-the-browser"></a>Interaktives Authentifizieren eines Benutzerkontos im Browser

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `InteractiveBrowserCredential` authentifiziert wird.

```java
/**
 * Authenticate interactively in the browser.
 */
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
    .clientId("<your app client ID>")
    .redirectUrl("YOUR_APP_REGISTERED_REDIRECT_URL")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(interactiveBrowserCredential)
    .buildClient();
```

## <a name="username-password-credential"></a>Anmeldeinformationen mit Benutzername/Kennwort

`UsernamePasswordCredential` unterstützt das Authentifizieren einer öffentlichen Clientanwendung mithilfe der Benutzeranmeldeinformationen, für die keine mehrstufige Authentifizierung erforderlich ist. Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `UsernamePasswordCredential` authentifiziert wird. Der Benutzer darf keine mehrstufige Authentifizierung aktiviert haben.

```java
/**
 * Authenticate with username, password.
 */
UsernamePasswordCredential usernamePasswordCredential = new UsernamePasswordCredentialBuilder()
    .clientId("<your app client ID>")
    .username("<your username>")
    .password("<your password>")
    .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
    .vaultUrl("https://<your Key Vault name>.vault.azure.net")
    .credential(usernamePasswordCredential)
    .buildClient();
```

Weitere Informationen finden Sie unter [Microsoft Identity Platform und OAuth 2.0-Kennwortanmeldeinformationen des Ressourcenbesitzers](/azure/active-directory/develop/v2-oauth-ropc).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Authentifizierung mit Benutzeranmeldeinformationen behandelt. Diese Form von Authentifizierung ist eine von mehreren Methoden, die Sie im Azure SDK für Java für die Authentifizierung verwenden können. In den folgenden Artikeln werden weitere Möglichkeiten beschrieben:

* [Azure-Authentifizierung in Entwicklungsumgebungen](identity-dev-env-auth.md)
* [Authentifizieren von in Azure gehosteten Anwendungen](identity-azure-hosted-auth.md)
* [Authentifizierung mit Dienstprinzipalen](identity-service-principal-auth.md)

Wenn Sie die Authentifizierung gemeistert haben, finden Sie unter [Konfigurieren der Protokollierung im Azure SDK für Java](logging-overview.md) weitere Informationen zur Protokollierungsfunktionalität, die vom SDK bereitgestellt wird.

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
