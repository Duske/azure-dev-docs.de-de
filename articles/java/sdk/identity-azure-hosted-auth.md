---
title: Authentifizieren von in Azure gehosteten Java-Anwendungen
description: Übersicht über Azure SDK für Java-Konzepte im Zusammenhang mit Authentifizierung in Entwicklungsumgebungen
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: e9097a432abf5957c9983ec2846bfb18ba581db4
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528534"
---
# <a name="authenticate-azure-hosted-java-applications"></a>Authentifizieren von in Azure gehosteten Java-Anwendungen

In diesem Artikel wird erläutert, wie die Azure-Identitätsbibliothek Azure Active Directory-Tokenauthentifizierung für in Azure gehostete Anwendungen unterstützt. Diese Unterstützung wird durch eine Reihe von `TokenCredential`-Implementierungen ermöglicht, die im Folgenden erläutert werden.

In diesem Artikel werden die folgenden Themen behandelt:

* [Azure-Standardanmeldeinformationen](#default-azure-credential)
* [Anmeldeinformationen für verwaltete Identität](#managed-identity-credential)

## <a name="default-azure-credential"></a>Azure-Standardanmeldeinformationen

`DefaultAzureCredential` ist für die meisten Szenarien geeignet, in denen die Anwendung letztendlich in der Azure-Cloud ausgeführt wird. `DefaultAzureCredential` kombiniert Anmeldeinformationen, die bei der Bereitstellung häufig zum Authentifizieren verwendet werden, mit Anmeldeinformationen, die für die Authentifizierung in einer Entwicklungsumgebung verwendet werden. `DefaultAzureCredential` versucht, die Authentifizierung über die folgenden Mechanismen in der angegebenen Reihenfolge auszuführen.

![Authentifizierungsflow mit DefaultAzureCredential](./media/defaultazurecredential.svg)

* Umgebung: `DefaultAzureCredential` liest Kontoinformationen, die über [Umgebungsvariablen](#environment-variables) angegeben werden und verwendet diese für die Authentifizierung.
* Verwaltete Identität: Wenn die Anwendung auf einem Azure-Host mit aktivierter verwalteter Identität bereitgestellt wird, führt `DefaultAzureCredential` die Authentifizierung mit diesem Konto aus.
* IntelliJ: Wenn Sie sich über das Azure-Toolkit für IntelliJ authentifiziert haben, führt `DefaultAzureCredential` die Authentifizierung mit diesem Konto aus.
* Visual Studio Code: Wenn Sie sich über das Plug-In für das Visual Studio Code Azure-Konto authentifiziert haben, führt `DefaultAzureCredential` die Authentifizierung mit diesem Konto aus.
* Azure CLI: Wenn Sie ein Konto über den Azure CLI-Befehl `az login` authentifiziert haben, führt `DefaultAzureCredential` die Authentifizierung mit diesem Konto aus.

### <a name="configure-defaultazurecredential"></a>Konfigurieren von DefaultAzureCredential

`DefaultAzureCredential` unterstützt eine Reihe von Konfigurationen durch Setter für `DefaultAzureCredentialBuilder` oder Umgebungsvariablen.

* Das Festlegen der Umgebungsvariablen `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` und `AZURE_TENANT_ID` (wie in [Umgebungsvariablen](#environment-variables) definiert) konfiguriert `DefaultAzureCredential` für die Authentifizierung als durch die Werte angegebenen Dienstprinzipal.
* Wenn Sie `.managedIdentityClientId(String)` für den Generator oder die Umgebungsvariable `AZURE_CLIENT_ID` festlegen, wird `DefaultAzureCredential` für die Authentifizierung als benutzerdefinierte verwaltete Identität konfiguriert. Wenn keine Werte angegeben werden, wird die Authentifizierung als vom System zugewiesene verwaltete Identität konfiguriert.
* Wenn Sie `.tenantId(String)` für den Generator oder die Umgebungsvariable `AZURE_TENANT_ID` festlegen, wird `DefaultAzureCredential` für die Authentifizierung bei einem bestimmten Mandanten für den freigegebenen Tokencache, Visual Studio Code und IntelliJ IDEA konfiguriert.
* Wenn die Umgebungsvariable `AZURE_USERNAME` festgelegt wird, wird `DefaultAzureCredential` für das Abrufen des entsprechenden zwischengespeicherten Token aus dem freigegebenen Tokencache konfiguriert.
* Wenn Sie `.intelliJKeePassDatabasePath(String)` für den Generator festlegen, wird `DefaultAzureCredential` für das Lesen einer bestimmten KeePass-Datei bei der Authentifizierung mit IntelliJ-Anmeldeinformationen konfiguriert.

### <a name="authenticate-with-defaultazurecredential"></a>Authentifizieren mit DefaultAzureCredential

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `DefaultAzureCredential` authentifiziert wird.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-a-user-assigned-managed-identity-with-defaultazurecredential"></a>Authentifizieren einer benutzerseitig zugewiesenen verwalteten Identität mit DefaultAzureCredential

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `DefaultAzureCredential` bei Bereitstellung für eine Azure-Ressource authentifiziert wird, für die eine benutzerseitig zugewiesene Identität konfiguriert ist.

```java
/**
 * The default credential will use the user-assigned managed identity with the specified client ID.
 */
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .managedIdentityClientId("<managed identity client ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

### <a name="authenticate-a-user-in-azure-toolkit-for-intellij-with-defaultazurecredential"></a>Authentifizieren eines Benutzers in Azure-Toolkit für IntelliJ mit DefaultAzureCredential

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `DefaultAzureCredential` auf einer Arbeitsstation authentifiziert wird, auf der IntelliJ IDEA installiert und die Anmeldung des Benutzers beim Azure-Toolkit für IntelliJ mit einem Azure-Konto erfolgt ist.

Weitere Informationen zum Konfigurieren von IntelliJ IDEA finden Sie unter [Anmelden beim Azure-Toolkit für IntelliJ für IntelliJCredential](identity-dev-env-auth.md#sign-in-azure-toolkit-for-intellij-for-intellijcredential).

```java
/**
 * The default credential will use the KeePass database path to find the user account in IntelliJ on Windows.
 */
// KeePass configuration is required only for Windows. No configuration needed for Linux / Mac.
DefaultAzureCredential defaultCredential = new DefaultAzureCredentialBuilder()
  .intelliJKeePassDatabasePath("C:\\Users\\user\\AppData\\Roaming\\JetBrains\\IdeaIC2020.1\\c.kdbx")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(defaultCredential)
  .buildClient();
```

## <a name="managed-identity-credential"></a>Anmeldeinformationen für verwaltete Identität

Die verwaltete Identität authentifiziert die verwaltete Identität (system- oder benutzerseitig zugewiesen) einer Azure-Ressource. Wenn die Anwendung also innerhalb einer Azure-Ressource ausgeführt wird, die verwaltete Identität über den `IDENTITY/MSI`- bzw. `IMDS`-Endpunkt oder beide Endpunkte unterstützt, authentifizieren diese Anmeldeinformationen Ihre Anwendung und bieter eine großartige Authentifizierungserfahrung ohne Geheimnisse.

Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

### <a name="authenticate-in-azure-with-managed-identity"></a>Authentifizierung in Azure mit verwalteter Identität

Das folgende Beispiel zeigt die Authentifizierung von `SecretClient` aus der [azure-security-keyvault-secrets][secrets_client_library]-Clieentbibliothek mit `ManagedIdentityCredential` mit einem virtuellen Computer, einem App-Dienst, einer Funktions-App, mit Cloud Shell oder einer Service Fabric, arc- oder AKS-Umgebung in Azure, wobei die system- oder benutzerseitig zugewiesene verwaltete Identität aktiviert ist.

```java
/**
 * Authenticate with a managed identity.
 */
ManagedIdentityCredential managedIdentityCredential = new ManagedIdentityCredentialBuilder()
  .clientId("<user-assigned managed identity client ID>") // required only for user-assigned
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(managedIdentityCredential)
  .buildClient();
```

## <a name="environment-variables"></a>Umgebungsvariablen

Sie können `DefaultAzureCredential` und `EnvironmentCredential` mit Umgebungsvariablen konfigurieren. Jeder Authentifizierungstyp erfordert Werte für bestimmte Variablen:

### <a name="service-principal-with-secret"></a>Dienstprinzipal mit Geheimnis

| Variablenname         | Wert                                                  |
| --------------------- | ------------------------------------------------------ |
| `AZURE_CLIENT_ID`     | ID einer Azure Active Directory-Anwendung.           |
| `AZURE_TENANT_ID`     | ID des Azure Active Directory-Mandanten der Anwendung. |
| `AZURE_CLIENT_SECRET` | Eines der Clientgeheimnisse der Anwendung.               |

### <a name="service-principal-with-certificate"></a>Dienstprinzipal mit Zertifikat

| Variablenname         | Wert                                                                                                |
| --------------------- | ---------------------------------------------------------------------------------------------------- |
| `AZURE_CLIENT_ID`     | ID einer Azure Active Directory-Anwendung.                                                         |
| `AZURE_TENANT_ID`     | ID des Azure Active Directory-Mandanten der Anwendung.                                               |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Pfad zu einer PEM-codierten Zertifikatdatei einschließlich privatem Schlüssel (ohne Kennwortschutz).|

### <a name="username-and-password"></a>Benutzername und Kennwort

| Variablenname         | Wert                                            |
| --------------------- | ------------------------------------------------ |
| `AZURE_CLIENT_ID`     | ID einer Azure Active Directory-Anwendung.     |
| `AZURE_USERNAME`      | Einen Benutzername (normalerweise eine E-Mail-Adresse).           |
| `AZURE_PASSWORD`      | Das zugehörige Kennwort für den angegebenen Benutzernamen.  |

Die Konfiguration wird in der oben genannten Reihenfolge versucht. Wenn zum Beispiel sowohl Werte für ein Clientgeheimnis als auch für ein Zertifikat vorhanden sind, wird das Clientgeheimnis verwendet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Authentifizierung für in Azure gehostete Anwendungen behandelt. Diese Form von Authentifizierung ist eine von mehreren Methoden, die Sie im Azure SDK für Java für die Authentifizierung verwenden können. In den folgenden Artikeln werden weitere Möglichkeiten beschrieben:

* [Azure-Authentifizierung in Entwicklungsumgebungen](identity-dev-env-auth.md)
* [Authentifizierung mit Dienstprinzipalen](identity-service-principal-auth.md)
* [Authentifizierung mit Benutzeranmeldeinformationen](identity-user-auth.md)

Wenn Sie die Authentifizierung gemeistert haben, finden Sie unter [Konfigurieren der Protokollierung im Azure SDK für Java](logging-overview.md) weitere Informationen zur Protokollierungsfunktionalität, die vom SDK bereitgestellt wird.

<!-- LINKS -->
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
