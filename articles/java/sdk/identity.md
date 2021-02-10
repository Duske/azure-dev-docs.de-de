---
title: Azure-Authentifizierung mit Java und Azure Identity
description: Übersicht über die Authentifizierungs- und Identitätsfunktionen des Azure SDK
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: 2a09b4ccaf39564c1cd2547417d722472ed9017c
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528522"
---
# <a name="azure-authentication-with-java-and-azure-identity"></a>Azure-Authentifizierung mit Java und Azure Identity

Dieser Artikel bietet eine Übersicht über die Java Azure Identity-Bibliothek, die Unterstützung für Azure Active Directory-Tokenauthentifizierung über das Azure SDK für Java bereitstellt. Diese Bibliothek bietet eine Reihe von `TokenCredential`-Implementierungen, die zum Erstellen von Azure SDK-Clients zur Unterstützung von AAD-Tokenauthentifizierung verwendet werden können.

Die Azure-Identitätsbibliothek unterstützt derzeit Folgendes:

* [Azure-Authentifizierung in Java-Entwicklungsumgebungen](identity-dev-env-auth.md), die Folgendes ermöglicht:
  * IDEA IntelliJ-Authentifizierung mit den Anmeldeinformationen, die aus dem [Azure-Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij/) abgerufen werden.
  * Visual Studio Code-Authentifizierung mit den Anmeldeinformationen, die im [Azure-Plug-In für Visual Studio Code](https://code.visualstudio.com/docs/azure/extensions) gespeichert werden.
  * Azure CLI-Authentifizierung, bei der die Anmeldeinformationen in der [Azure CLI](/cli/azure/what-is-azure-cli) gespeichert werden.
* [Authentifizierung von in Azure gehosteten Anwendungen](identity-azure-hosted-auth.md), die Folgendes ermöglicht:
  * Azure-Standardauthentifizierung mit Anmeldeinformationen
  * Authentifizierung mit verwalteten Identitäten
* [Authentifizierung mit Dienstprinzipalen](identity-service-principal-auth.md), die Folgendes ermöglicht:
  * Authentifizierung mit Clientgeheimnis
  * Authentifizierung mit Clientzertifikaten
* [Authentifizierung mit Benutzeranmeldeinformationen](identity-user-auth.md), die Folgendes ermöglicht:
  * Interaktive Browserauthentifizierung
  * Gerätecodeauthentifizierung
  * Authentifizierung mit Benutzername/Kennwort

Folgen Sie den oben aufgeführten Links, um mehr über die Besonderheiten der einzelnen Authentifizierungsansätze zu erfahren. Im weiteren Verlauf dieses Artikels werden die häufig verwendeten `DefaultAzureCredential`-Methoden und verwandte Themen vorgestellt.

## <a name="add-the-maven-dependencies"></a>Hinzufügen von Maven-Abhängigkeiten

Um die Maven-Abhängigkeit hinzuzufügen, fügen Sie den folgenden XML-Code in die Datei *pom.xml* des Projekts ein. Ersetzen Sie die Versionsnummer *1.2.1* durch die neueste veröffentlichte Versionsnummer, die auf der Seite [Microsoft Azure-Clientbibliothek für Identität](https://mvnrepository.com/artifact/com.azure/azure-identity) angezeigt wird.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.1</version>
</dependency>
```

## <a name="key-concepts"></a>Wichtige Begriffe

Es gibt zwei wichtige Konzepte zum Verständnis der Azure-Identitätsbibliothek: das Konzept von Anmeldeinformationen und die häufigste Implementierung dieser Anmeldeinformationen, `DefaultAzureCredential`.

Bei den Anmeldeinformationen handelt es sich um eine Klasse, die die Daten enthält oder abrufen kann, die für einen Dienstclient zum Authentifizieren von Anforderungen erforderlich sind. Dienstclients akzeptieren über das Azure SDK Anmeldeinformationen, wenn Sie erstellt werden, und Dienstclients verwenden diese Anmeldeinformationen zum Authentifizieren von Anforderungen an den Dienst.

Die Azure-Identitätsbibliothek konzentriert sich auf die OAuth-Authentifizierung mit Azure Active Directory und bietet verschiedene Anmeldeinformationsklassen, die ein AAD-Token zum Authentifizieren von Dienstanforderungen abrufen können. Alle Anmeldeinformationsklassen in dieser Bibliothek sind Implementierungen der abstrakten Klasse `TokenCredential` in [azure-core][azure_core_library], und Sie können sie zum Erstellen von Dienstclients verwenden, die sich mit einem `TokenCredential` authentifizieren können.

`DefaultAzureCredential` ist für die meisten Szenarien geeignet, in denen die Anwendung letztendlich in der Azure-Cloud ausgeführt werden soll. `DefaultAzureCredential` kombiniert Anmeldeinformationen, die bei der Bereitstellung häufig zum Authentifizieren verwendet werden, mit Anmeldeinformationen, die für die Authentifizierung in einer Entwicklungsumgebung verwendet werden. Weitere Informationen (einschließlich Beispielen für die Verwendung von `DefaultAzureCredential`) finden Sie im Abschnitt [Azure-Standardanmeldeinformationen](identity-azure-hosted-auth.md#default-azure-credential) vib [Authentifizieren von in Azure gehosteten Java-Anwendungen](identity-azure-hosted-auth.md).

## <a name="examples"></a>Beispiele

Wie bereits in [Verwenden des Azure SDK für Java](overview.md#provision-and-manage-azure-resources-with-management-libraries) erwähnt, unterscheiden sich die Verwaltungsbibliotheken geringfügig. Einer der Unterschiede besteht darin, dass Bibliotheken für die *Nutzung* von Azure-Diensten (so genannte Clientbibliotheken) und Bibliotheken für die *Verwaltung* von Azure-Dienste (als Verwaltungsbibliotheken bezeichnet) vorhanden sind. In den folgenden Abschnitten finden Sie einen kurzen Überblick über Authentifizierung in Client- und Verwaltungsbibliotheken.

### <a name="authenticate-azure-client-libraries"></a>Authentifizieren von Azure-Clientbibliotheken

Im folgenden Beispiel wird gezeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `DefaultAzureCredential` authentifiziert wird.

```java
// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(new DefaultAzureCredentialBuilder().build())
  .buildClient();
```

### <a name="authenticate-azure-management-libraries"></a>Authentifizieren von Azure-Verwaltungsbibliotheken

Die Azure-Verwaltungsbibliotheken verwenden dieselben Anmeldeinformationen-APIs wie die Azure-Clientbibliotheken, erfordern jedoch auch eine [Azure-Abonnement-ID](/learn/modules/create-an-azure-account/4-multiple-subscriptions), um die Azure-Ressourcen für dieses Abonnement zu verwalten.

Sie finden die Abonnement-IDs auf der Seite [Abonnements im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Verwenden Sie alternativ den folgenden [Azure-CLI][azure_cli]-Befehl, um Abonnement-IDs abzurufen:

```azurecli
az account list --output table
```

Sie können die Abonnement-ID in der Umgebungsvariablen `AZURE_SUBSCRIPTION_ID` festlegen. Diese ID wird von `AzureProfile` als Standardabonnement-ID während der Erstellung einer `Manager`-Instanz abgerufen, wie im folgenden Beispiel gezeigt:

```java
AzureResourceManager azureResourceManager = AzureResourceManager.authenticate(
        new DefaultAzureCredentialBuilder().build(),
        new AzureProfile(AzureEnvironment.AZURE))
    .withDefaultSubscription();
```

`DefaultAzureCredential` authentifiziert in diesem Beispiel mithilfe von `DefaultAzureCredential` eine `AzureResourceManager`-Instanz. Anstelle von `DefaultAzureCredential` können Sie auch andere Implementierungen der Tokenanmeldeinformationen verwenden, die in der Azure-Identitätsbibliothek zur Verfügung gestellt werden.

## <a name="troubleshooting"></a>Problembehandlung

Anmeldeinformationen lösen Ausnahmen aus, wenn ein Authentifizierungsfehler auftritt oder die Authentifizierung nicht ausgeführt werden kann. Wenn Anmeldeinformationen keine Authentifizierung ausführen können, wird `ClientAuthenticationException` ausgelöst und verfügt über ein `message`-Attribut, das beschreibt, warum die Authentifizierung fehlgeschlagen ist. Wenn `ChainedTokenCredential` diese Ausnahme auslöst, wird die verkettete Ausführung der zugrunde liegenden Liste der Anmeldeinformationen beendet.

Wenn Anmeldeinformationen die Authentifizierung nicht ausführen können, weil eine der zugrunde liegenden Ressourcen, die für die Anmeldeinformationen erforderlich ist, auf dem Computer nicht verfügbar ist, wird `CredentialUnavailableException` ausgelöst und verfügt über ein `message`-Attribut, das beschreibt, warum die Anmeldeinformationen für die Authentifizierungsausführung nicht verfügbar sind. Wenn `ChainedTokenCredential` diese Ausnahme auslöst, erfasst die Nachricht Fehlermeldungen aus den einzelnen Anmeldeinformationen in der Kette.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die Azure-Identitätsfunktion vorgestellt, die im Azure SDK für Java verfügbar ist. `DefaultAzureCredential` wurde für viele Fälle als gängig und geeignet eingestuft. In den folgenden Artikeln werden andere Möglichkeiten der Authentifizieren mithilfe der Azure-Identitätsbibliothek beschrieben und weitere Informationen zu `DefaultAzureCredential` bereitgestellt:

* [Azure-Authentifizierung in Entwicklungsumgebungen](identity-dev-env-auth.md)
* [Authentifizieren von in Azure gehosteten Anwendungen](identity-azure-hosted-auth.md)
* [Authentifizierung mit Dienstprinzipalen](identity-service-principal-auth.md)
* [Authentifizierung mit Benutzeranmeldeinformationen](identity-user-auth.md)

<!-- LINKS -->
[azure_cli]: /cli/azure
[azure_sub]: https://azure.microsoft.com/free/
[source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity
[aad_doc]: /azure/active-directory/
[code_of_conduct]: https://opensource.microsoft.com/codeofconduct/
[keys_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys
[logging]: https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
[eventhubs_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs
[azure_core_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/core
[javadoc]: https://azure.github.io/azure-sdk-for-java
[jdk_link]: /java/azure/jdk
