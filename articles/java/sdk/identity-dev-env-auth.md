---
title: Azure-Authentifizierung in Java-Entwicklungsumgebungen
description: Übersicht über Azure SDK für Java-Konzepte im Zusammenhang mit Authentifizierung in Entwicklungsumgebungen
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 7913430a81a0fe223d6eb23a48541e0f752323b2
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528529"
---
# <a name="azure-authentication-in-java-development-environments"></a>Azure-Authentifizierung in Java-Entwicklungsumgebungen

Dieser Artikel bietet eine Übersicht über die Unterstützung der Azure Identity-Bibliothek für Azure Active Directory-Tokenauthentifizierung. Diese Unterstützung ermöglicht durch eine Reihe von `TokenCredential`-Implementierungen Authentifizierung für Anwendungen, die lokal auf Entwicklercomputern ausgeführt werden.

In diesem Artikel werden die folgenden Themen behandelt:

* [Gerätecodeanmeldeinformationen](#device-code-credential)
* [Interaktive Browseranmeldeinformationen](#interactive-browser-credential)
* [Azure CLI-Anmeldeinformationen](#azure-cli-credential)
* [IntelliJ-Anmeldeinformationen](#intellij-credential)
* [Visual Studio Code-Anmeldeinformationen](#visual-studio-code-credential)

## <a name="device-code-credential"></a>Gerätecodeanmeldeinformationen

Die Gerätecodeanmeldeinformationen authentifizieren einen Benutzer interaktiv auf Geräten mit eingeschränkter Benutzeroberfläche. Dies funktioniert, indem der Benutzer aufgefordert wird, eine Anmelde-URL auf einem für Browser aktivierten Computer zu besuchen, wenn die Anwendung versucht, sich zu authentifizieren. Der Benutzer gibt dann den in den Anweisungen genannten Gerätecode zusammen mit seinen Anmeldeinformationen ein. Bei erfolgreicher Authentifizierung wird die Anwendung, die Authentifizierung angefordert hat, auf dem Gerät, auf dem sie ausgeführt wird, erfolgreich authentifiziert.

Weitere Informationen finden Sie unter [Microsoft Identity Platform und der OAuth 2.0-Flow für Geräteautorisierungsgenehmigung](/azure/active-directory/develop/v2-oauth2-device-code).

### <a name="enable-applications-for-device-code-flow"></a>Aktivieren von Anwendungen für Gerätecodeflow

Führen Sie die folgenden Schritte aus, um einen Benutzer über Gerätecodeflow zu authentifizieren:

1. Navigieren Sie im Azure-Portal zu Azure Active Directory, und suchen Sie nach Ihrer App-Registrierung.
2. Navigieren Sie zum Abschnitt **Authentifizierung**.
3. Überprüfen Sie unter **Vorgeschlagene umgeleitete URIs** den URI, der mit `/common/oauth2/nativeclient` endet.
4. Wählen Sie unter **Standardclienttyp** die Option *Ja* aus, um die **Anwendung als öffentlichen Client zu behandeln**.

Mit diesen Schritten wird die Anwendung authentifiziert, sie verfügt aber immer noch nicht über die Berechtigung, Sie bei Active Directory anzumelden oder auf Ressourcen in Ihrem Namen zuzugreifen. Um dieses Problem zu beheben, navigieren Sie zu **API-Berechtigungen**, und aktivieren Sie Microsoft Graph sowie die Ressourcen, auf die Sie zugreifen möchten.

Sie müssen auch Administrator Ihres Mandanten sein, um Ihrer Anwendung Einwilligung zu erteilen, wenn Sie sich zum ersten Mal anmelden.

Wenn Sie die Option für Gerätecodeflow für Ihr Active Directory nicht konfigurieren können, muss Ihre App ggf. mehrinstanzenfähig sein. Wenn Sie Ihre App mehrinstanzenfähig machen möchten, navigieren Sie zum Bereich **Authentifizierung** und wählen dann **Konten in einem beliebigen Organisationsverzeichnis** aus. Wählen Sie dann für *Anwendung als öffentlichen Client behandeln* die Option **Ja** aus.

### <a name="authenticate-a-user-account-with-device-code-flow"></a>Authentifizieren eines Benutzerkontos mit dem Gerätecodeflow

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `DeviceCodeCredential` auf einem IoT-Gerät authentifiziert wird.

```java
DeviceCodeCredential deviceCodeCredential = new DeviceCodeCredentialBuilder()
  .challengeConsumer(challenge -> {
    // lets user know of the challenge
    System.out.println(challenge.getMessage());
  }).build();

// Azure SDK client builders accept the credential as a parameter
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
InteractiveBrowserCredential interactiveBrowserCredential = new InteractiveBrowserCredentialBuilder()
  .clientId("<your client ID>")
  .redirectUrl("http://localhost:8765")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(interactiveBrowserCredential)
  .buildClient();
```

## <a name="azure-cli-credential"></a>Azure CLI-Anmeldeinformationen

Die Visual Studio Code-Anmeldeinformationen führen die Authentifizierung in einer Entwicklungsumgebung mit aktiviertem Benutzer oder Dienstprinzipal in der Azure CLI aus. Die Azure CLI wird für einen bereits bei ihr angemeldeten Benutzer verwendet, um die Anwendung anhand von Azure Active Directory zu authentifizieren.

### <a name="sign-in-azure-cli-for-azureclicredential"></a>Anmelde bei der Azure CLI für AzureCliCredential

Melden Sie sich mit dem folgenden [Azure CLI][azure_cli]-Befehl als Benutzer an:

```bash
az login
```

Verwenden Sie den folgenden Befehl, um sich als Dienstprinzipal anzumelden:

```bash
az login --service-principal --username <client ID> --password <client secret> --tenant <tenant ID>
```

Wenn das Konto oder der Dienstprinzipal Zugriff auf mehrere Mandanten hat, stellen Sie in der Ausgabe des folgenden Befehls sicher, dass sich der gewünschte Mandant bzw. das gewünschte Abonnement im Zustand „Aktiviert“ befindet:

```bash
az account list
```

Bevor Sie `AzureCliCredential` im Code verwenden, führen Sie den folgenden Befehl aus, um zu überprüfen, ob das Konto erfolgreich konfiguriert wurde.

```bash
az account get-access-token
```

Abhängig von der Gültigkeit des Aktualisierungstokens in Ihrer Organisation müssen Sie diesen Vorgang möglicherweise nach einem bestimmten Zeitraum wiederholen. Im Allgemeinen beträgt die Gültigkeitsdauer des Aktualisierungstokens einige Wochen bis hin zu einigen Monaten. Sie werden von `AzureCliCredential` aufgefordert, sich erneut anzumelden.

### <a name="authenticate-a-user-account-with-azure-cli"></a>Authentifizieren eines Benutzerkontos mit der Azure CLI

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `AzureCliCredential` auf einer Arbeitsstation authentifiziert wird, auf der die Azure CLI installiert und die Anmeldung bei ihr erfolgt ist.

```java
AzureCliCredential cliCredential = new AzureCliCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(cliCredential)
  .buildClient();
```

## <a name="intellij-credential"></a>IntelliJ-Anmeldeinformationen

Die Visual Studio Code-Anmeldeinformationen führen die Authentifizierung in einer Entwicklungsumgebung mit dem Konto in Azure-Toolkit für IntelliJ aus. Es werden die Anmeldeinformationen des angemeldeten Benutzers in der IntelliJ-IDE verwendet, um die Anwendung anhand von Azure Active Directory zu authentifizieren.

## <a name="sign-in-azure-toolkit-for-intellij-for-intellijcredential"></a>Anmelden beim Azure-Toolkit für IntelliJ für IntelliJCredential

Führen Sie die unten beschriebenen Schritte aus:

1. Öffnen Sie im IntelliJ-Fenster **Datei > Einstellungen > Plug-Ins**.
1. Suchen Sie im Marketplace nach „Azure-Toolkit für IntelliJ“. Installieren Sie es, und starten Sie die IDE neu.
1. Suchen Sie nach dem neuen Menüelement **Extras > Azure > Azure-Anmeldung**.
1. Mithilfe der **Geräteanmeldung** können Sie sich als Benutzerkonto anmelden. Befolgen Sie die Anweisungen zum Anmelden auf der `login.microsoftonline.com`-Website mit dem Gerätecode. IntelliJ fordert Sie auf, Ihre Abonnements auszuwählen. Wählen Sie das Abonnement mit den Ressourcen aus, auf die Sie zugreifen möchten.

Unter Windows benötigen Sie auch den KeePass-Datenbankpfad zum Lesen von IntelliJ-Anmeldeinformationen. Sie finden den Pfad in den IntelliJ-Einstellungen unter **Datei > Einstellungen > Darstellung und Verhalten > Systemeinstellungen > Kennwörter**. Notieren Sie sich den Speicherort des KeePassDatabase-Pfads.

## <a name="authenticate-a-user-account-with-intellij-idea"></a>Authentifizieren eines Benutzerkontos mit IntelliJ IDEA

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `IntelliJCredential` auf einer Arbeitsstation authentifiziert wird, auf der IntelliJ IDEA installiert und die Anmeldung des Benutzers mit einem Azure-Konto erfolgt ist.

```java
IntelliJCredential intelliJCredential = new IntelliJCredentialBuilder()
  // KeePass configuration isrequired only for Windows. No configuration needed for Linux / Mac.
  .keePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(intelliJCredential)
  .buildClient();
```

## <a name="visual-studio-code-credential"></a>Visual Studio Code-Anmeldeinformationen

Die Visual Studio Code-Anmeldeinformationen aktivieren Authentifizierung in Entwicklungsumgebungen, in denen VS Code mit der [Azure-Kontoerweiterung für VS Code](https://github.com/Microsoft/vscode-azure-account) installiert ist. Es werden die Anmeldeinformationen des angemeldeten Benutzers in der VS Code-IDE verwendet, um die Anwendung anhand von Azure Active Directory zu authentifizieren.

### <a name="sign-in-visual-studio-code-azure-account-extension-for-visualstudiocodecredential"></a>Anmelden bei der Azure-Kontoerweiterung für Visual Studio Code für VisualStudioCodeCredential

Die Visual Studio Code-Authentifizierung erfolgt durch eine Integration in die [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Um diese Form von Authentifizierung zu verwenden, installieren Sie die Azure-Kontoerweiterung und verwenden dann **Ansicht > Befehlspalette**, um den Befehl **Azure: Anmelden** auszuführen. Mit diesem Befehl wird ein Browserfenster geöffnet und eine Seite angezeigt, auf der Sie sich bei Azure anmelden können. Nachdem Sie den Anmeldevorgang abgeschlossen haben, können Sie den Browser wie angegeben schließen. Wenn Sie Ihre Anwendung ausführen (entweder im Debugger oder an einem beliebigen anderen Ort auf dem Entwicklungscomputer), werden die Anmeldeinformationen aus Ihrer Anmeldung verwendet.

### <a name="authenticate-a-user-account-with-visual-studio-code"></a>Authentifizieren eines Benutzerkontos mit Visual Studio Code

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `VisualStudioCodeCredential` auf einer Arbeitsstation authentifiziert wird, auf der Visual Studio Code installiert und die Anmeldung des Benutzers mit einem Azure-Konto erfolgt ist.

```java
VisualStudioCodeCredential visualStudioCodeCredential = new VisualStudioCodeCredentialBuilder().build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(visualStudioCodeCredential)
  .buildClient();
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Authentifizierung während der Entwicklung mithilfe von Anmeldeinformationen behandelt, die auf Ihrem Computer verfügbar sind. Diese Form von Authentifizierung ist eine von mehreren Methoden, die Sie im Azure SDK für Java für die Authentifizierung verwenden können. In den folgenden Artikeln werden weitere Möglichkeiten beschrieben:

* [Authentifizieren von in Azure gehosteten Anwendungen](identity-azure-hosted-auth.md)
* [Authentifizierung mit Dienstprinzipalen](identity-service-principal-auth.md)
* [Authentifizierung mit Benutzeranmeldeinformationen](identity-user-auth.md)

Wenn Sie die Authentifizierung gemeistert haben, finden Sie unter [Konfigurieren der Protokollierung im Azure SDK für Java](logging-overview.md) weitere Informationen zur Protokollierungsfunktionalität, die vom SDK bereitgestellt wird.

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
