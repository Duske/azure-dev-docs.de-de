---
title: Azure-Authentifizierung mit Dienstprinzipal
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit Authentifizierung von Anwendungen über einen Dienstprinzipal
author: g2vinay
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: vigera
ms.openlocfilehash: fd8c107d4fb5d37e3f2c2e99ba45ad46a6de34ae
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528552"
---
# <a name="azure-authentication-with-service-principal"></a>Azure-Authentifizierung mit Dienstprinzipal

In diesem Artikel wird erläutert, wie die Azure-Identitätsbibliothek Azure Active Directory-Tokenauthentifizierung über einen Dienstprinzipal unterstützt. In diesem Artikel werden die folgenden Themen behandelt:

* [Erstellen eines Dienstprinzipals über die Azure CLI](#create-a-service-principal-with-the-azure-cli)
* [Anmeldeinformationen mit Clientgeheimnis](#client-secret-credential)
* [Anmeldeinformationen mit Clientzertifikat](#client-certificate-credential)

Weitere Informationen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](/azure/active-directory/develop/app-objects-and-service-principals).

## <a name="create-a-service-principal-with-the-azure-cli"></a>Erstellen eines Dienstprinzipals über die Azure CLI

Verwenden Sie die [Azure CLI][azure_cli]-Beispiele unten, um Anmeldeinformationen mit Clientgeheimnis zu erstellen oder abzurufen.

Verwenden Sie den folgenden Befehl, um einen Dienstprinzipal zu erstellen und seinen Zugriff auf Azure-Ressourcen zu konfigurieren:

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment
```

Dieser Befehl gibt einen Wert zurück, der der folgenden Ausgabe ähnelt:

```output
{
"appId": "generated-app-ID",
"displayName": "dummy-app-name",
"name": "http://dummy-app-name",
"password": "random-password",
"tenant": "tenant-ID"
}
```

Verwenden Sie den folgenden Befehl, um einen Dienstprinzipal zusammen mit einem Zertifikat zu erstellen. Notieren Sie sich den Pfad/Speicherort dieses Zertifikats.

```azurecli
az ad sp create-for-rbac -n <your application name> --skip-assignment --cert <certificate name> --create-cert
```

Überprüfen Sie die zurückgegebenen Anmeldeinformationen, und notieren Sie sich die folgenden Informationen:

* `AZURE\_CLIENT\_ID` als appId.
* `AZURE\_CLIENT\_SECRET` als Kennwort.
* `AZURE\_TENANT\_ID` als Mandant.

## <a name="client-secret-credential"></a>Anmeldeinformationen mit Clientgeheimnis

Diese Anmeldeinformationen authentifizieren den erstellten Dienstprinzipal über sein Clientgeheimnis (Kennwort). Dieses Beispiel zeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `ClientSecretCredential` authentifiziert wird.

```java
/**
 *  Authenticate with client secret.
 */
ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
  .clientId("<your client ID>")
  .clientSecret("<your client secret>")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientSecretCredential)
  .buildClient();
```

## <a name="client-certificate-credential"></a>Anmeldeinformationen mit Clientzertifikat

Diese Anmeldeinformationen authentifizieren den erstellten Dienstprinzipal über sein Clientzertifikat. Dieses Beispiel zeigt, wie `SecretClient` von der [azure-security-keyvault-secrets][secrets_client_library]-Clientbibliothek mithilfe von `ClientCertificateCredential` authentifiziert wird.

```java
/**
 *  Authenticate with a client certificate.
 */
ClientCertificateCredential clientCertificateCredential = new ClientCertificateCredentialBuilder()
  .clientId("<your client ID>")
  .pemCertificate("<path to PEM certificate>")
  // Choose between either a PEM certificate or a PFX certificate.
  //.pfxCertificate("<path to PFX certificate>", "PFX CERTIFICATE PASSWORD")
  .tenantId("<your tenant ID>")
  .build();

// Azure SDK client builders accept the credential as a parameter.
SecretClient client = new SecretClientBuilder()
  .vaultUrl("https://<your Key Vault name>.vault.azure.net")
  .credential(clientCertificateCredential)
  .buildClient();
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde Authentifizierung über einen Dienstprinzipal behandelt. Diese Form von Authentifizierung ist eine von mehreren Methoden, die Sie im Azure SDK für Java für die Authentifizierung verwenden können. In den folgenden Artikeln werden weitere Möglichkeiten beschrieben:

* [Azure-Authentifizierung in Entwicklungsumgebungen](identity-dev-env-auth.md)
* [Authentifizieren von in Azure gehosteten Anwendungen](identity-azure-hosted-auth.md)
* [Authentifizierung mit Benutzeranmeldeinformationen](identity-user-auth.md)

Wenn Sie die Authentifizierung gemeistert haben, finden Sie unter [Konfigurieren der Protokollierung im Azure SDK für Java](logging-overview.md) weitere Informationen zur Protokollierungsfunktionalität, die vom SDK bereitgestellt wird.

<!-- LINKS -->
[azure_cli]: /cli/azure
[secrets_client_library]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets
