---
title: Verwenden von Azure-Dienstprinzipalen mit der Azure CLI
description: Hier erfahren Sie, wie Sie mit der Azure-Befehlszeilenschnittstelle Dienstprinzipale erstellen und verwenden.
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 02/15/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: c18adbee84fd3e5c73367b07bbd0b03ac61008cd
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82030732"
---
# <a name="create-an-azure-service-principal-with-the-azure-cli"></a>Erstellen eines Azure-Dienstprinzipals mit der Azure-Befehlszeilenschnittstelle

Für automatisierte Tools, die Azure-Dienste verwenden, sollten stets eingeschränkte Berechtigungen festgelegt sein. Azure bietet Dienstprinzipale, damit Anwendungen nicht als Benutzer mit uneingeschränkten Berechtigungen angemeldet werden müssen.

Ein Azure-Dienstprinzipal ist eine Identität, die zur Verwendung mit Anwendungen, gehosteten Diensten und automatisierten Tools für den Zugriff auf Azure-Ressourcen erstellt wird. Dieser Zugriff wird durch die dem Dienstprinzipal zugewiesenen Rollen eingeschränkt. Dadurch können Sie steuern, auf welcher Ebene auf welche Ressourcen zugegriffen werden kann. Aus Sicherheitsgründen wird stets empfohlen, Dienstprinzipale mit automatisierten Tools zu verwenden, statt ihnen die Anmeldung mit einer Benutzeridentität zu erlauben.

In diesem Artikel wird Schritt für Schritt erläutert, wie Sie mit der Azure CLI einen Dienstprinzipal erstellen, Informationen zu ihm abrufen und ihn zurücksetzen.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Erstellen Sie mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) einen Dienstprinzipal. Beim Erstellen eines Dienstprinzipals wählen Sie den Typ der von ihm verwendeten Anmeldeauthentifizierung aus.

> [!NOTE]
>
> Wenn Ihr Konto nicht über Berechtigungen zum Erstellen eines Dienstprinzipals verfügt, gibt `az ad sp create-for-rbac` eine Fehlermeldung mit dem Hinweis „Nicht genügend Berechtigungen zum Abschließen des Vorgangs“ zurück. Bitten Sie den Azure Active Directory-Administrator, einen Dienstprinzipal zu erstellen.

Für Dienstprinzipale stehen zwei Authentifizierungstypen zur Verfügung: Kennwortbasierte Authentifizierung und zertifikatbasierte Authentifizierung.

### <a name="password-based-authentication"></a>Kennwortbasierte Authentifizierung

Ohne jegliche Authentifizierungsparameter wird die kennwortbasierte Authentifizierung mit einem für Sie erstellten zufälligen Kennwort verwendet.

  ```azurecli-interactive
  az ad sp create-for-rbac --name ServicePrincipalName
  ```

> [!IMPORTANT]
> Ab Azure CLI 2.0.68 wird der Parameter `--password` zum Erstellen eines Dienstprinzipals mit einem benutzerdefinierten Kennwort __nicht mehr unterstützt__, um die versehentliche Verwendung von unsicheren Kennwörtern zu verhindern.

Die Ausgabe für einen Dienstprinzipal mit Kennwortauthentifizierung enthält den Schlüssel `password`. Kopieren Sie diesen Wert __unbedingt__, da er nicht abgerufen werden kann. Wenn Sie das Kennwort vergessen haben, [setzen Sie die Anmeldeinformationen des Dienstprinzipals zurück](#reset-credentials).

Die Schlüssel `appId` und `tenant` werden in der Ausgabe von `az ad sp create-for-rbac` angezeigt und bei der Dienstprinzipalauthentifizierung verwendet.
Notieren Sie die Werte. Sie können jedoch auch jederzeit mit [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) abgerufen werden.

### <a name="certificate-based-authentication"></a>Zertifikatbasierte Authentifizierung

Verwenden Sie für die zertifikatbasierte Authentifizierung das Argument `--cert`. Bei Verwendung dieses Arguments muss bereits ein Zertifikat vorhanden sein. Vergewissern Sie sich, dass alle Tools, die diesen Dienstprinzipal verwenden, Zugriff auf den privaten Schlüssel des Zertifikats haben. Zertifikate müssen in einem ASCII-Format vorliegen, etwa PEM, CER oder DER. Übergeben Sie das Zertifikat als Zeichenfolge, oder verwenden Sie das Format `@path`, um das Zertifikat aus einer Datei zu laden.

> [!NOTE]
> Bei Verwendung einer PEM-Datei muss das Zertifikat (**CERTIFICATE**) an den privaten Schlüssel (**PRIVATE KEY**) in der Datei angefügt werden.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert "-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----"
```

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert @/path/to/cert.pem
```

Das Argument `--keyvault` kann hinzugefügt werden, um ein Zertifikat in Azure Key Vault zu verwenden. In diesem Fall ist der Wert `--cert` der Name des Zertifikats.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --cert CertName --keyvault VaultName
```

Verwenden Sie das Argument `--create-cert`, um ein _selbstsigniertes_ Zertifikat für die Authentifizierung zu erstellen:

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --create-cert
```

Konsolenausgabe:

```
Creating a role assignment under the scope of "/subscriptions/myId"
Please copy C:\myPath\myNewFile.pem to a safe place.
When you run 'az login', provide the file path in the --password argument
{
  "appId": "myAppId",
  "displayName": "myDisplayName",
  "fileWithCertAndPrivateKey": "C:\\myPath\\myNewFile.pem",
  "name": "http://myName",
  "password": null,
  "tenant": "myTenantId"
}
```

Inhalt der neuen PEM-Datei:
```
-----BEGIN PRIVATE KEY-----
myPrivateKeyValue
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
myCertificateValue
-----END CERTIFICATE-----
```

> [!NOTE]
> Mit dem Befehl `az ad sp create-for-rbac --create-cert` werden der Dienstprinzipal und eine PEM-Datei erstellt. Die PEM-Datei enthält einen ordnungsgemäß formatierten privaten Schlüssel (**PRIVATE KEY**) und ein ordnungsgemäß formatiertes Zertifikat (**CERTIFICATE**).

Das Argument `--keyvault` kann hinzugefügt werden, um das Zertifikat in Azure Key Vault zu speichern. Bei Verwendung von `--keyvault` ist das Argument `--cert`__erforderlich__.

```azurecli-interactive
az ad sp create-for-rbac --name ServicePrincipalName --create-cert --cert CertName --keyvault VaultName
```

Die Ausgabe enthält den Schlüssel `fileWithCertAndPrivateKey`, es sei denn, Sie speichern das Zertifikat in Key Vault. Am Wert dieses Schlüssels können Sie erkennen, wo das generierte Zertifikat gespeichert ist.
Kopieren Sie das Zertifikat __unbedingt__ an einen sicheren Speicherort. Andernfalls können Sie sich nicht mit diesem Dienstprinzipal anmelden.

Rufen Sie für in Key Vault gespeicherte Zertifikate den privaten Schlüssel des Zertifikats mit [az keyvault secret show](/cli/azure/keyvault/secret#az-keyvault-secret-show) ab. In Key Vault ist der Name des Zertifikatgeheimnisses mit dem Zertifikatnamen identisch. Wenn Sie keinen Zugriff mehr auf den privaten Schlüssel des Zertifikats haben, [setzen Sie die Anmeldeinformationen des Dienstprinzipals zurück](#reset-credentials).

Die Schlüssel `appId` und `tenant` werden in der Ausgabe von `az ad sp create-for-rbac` angezeigt und bei der Dienstprinzipalauthentifizierung verwendet.
Notieren Sie die Werte. Sie können jedoch auch jederzeit mit [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) abgerufen werden.

## <a name="get-an-existing-service-principal"></a>Abrufen eines vorhandenen Dienstprinzipals

Mit [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) kann eine Liste der Dienstprinzipale in einem Mandanten abgerufen werden. Dieser Befehl gibt standardmäßig die ersten 100 Dienstprinzipale für Ihren Mandanten zurück. Verwenden Sie zum Abrufen aller Dienstprinzipale eines Mandanten das Argument `--all`. Da das Abrufen dieser Liste sehr lange dauern kann, wird empfohlen, die Liste mit einem der folgenden Argumente zu filtern:

* `--display-name` fordert Dienstprinzipale mit einem _Präfix_ an, das dem angegebenen Namen entspricht. Der Anzeigename eines Dienstprinzipals ist der Wert, der während der Erstellung mit dem Parameter `--name` festgelegt wird. Haben Sie `--name` während der Erstellung des Dienstprinzipals nicht festgelegt, lautet das Namenspräfix `azure-cli-`.
* `--spn` filtert nach genauen Übereinstimmungen des Dienstprinzipalnamens. Der Dienstprinzipalname beginnt immer mit `https://`.
  Handelte es sich bei dem für `--name` verwendeten Wert nicht um einen URI, wird für diesen Wert `https://` gefolgt vom Anzeigenamen verwendet.
* `--show-mine` fordert nur Dienstprinzipale an, die vom angemeldeten Benutzer erstellt wurden.
* `--filter` akzeptiert einen OData-Filter und führt die _serverseitige_ Filterung aus. Diese Methode wird anstelle der clientseitigen Filterung mit dem Argument `--query` der CLI empfohlen. Weitere Informationen zu OData-Filtern finden Sie unter [OData-Ausdruckssyntax für Filter und Sortierklauseln in Azure Search](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

Für Dienstprinzipalobjekte werden ausführliche Informationen zurückgegeben. Verwenden Sie die Abfragezeichenfolge `[].{id:appId, tenant:appOwnerTenantId}`, um nur die für die Anmeldung erforderlichen Informationen abzurufen. Die Anmeldeinformationen für alle Dienstprinzipale, die vom derzeit angemeldeten Benutzer erstellt wurden, rufen Sie beispielsweise wie folgt ab:

```azurecli-interactive
az ad sp list --show-mine --query "[].{id:appId, tenant:appOwnerTenantId}"
```

> [!IMPORTANT]
>
> Mit `az ad sp list` und [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show) rufen Sie den Benutzer und den Mandanten ab, allerdings nicht die Authentifizierungsgeheimnisse _oder_ die Authentifizierungsmethode.
> Geheimnisse für Zertifikate in Key Vault können mit [az keyvault secret show](/cli/azure/keyvault/secret#az-keyvault-secret-show) abgerufen werden. Andere Geheimnisse werden jedoch nicht standardmäßig gespeichert.
> Wenn Sie eine Authentifizierungsmethode oder ein Geheimnis vergessen haben, [setzen Sie die Anmeldeinformationen des Dienstprinzipals zurück](#reset-credentials).

## <a name="manage-service-principal-roles"></a>Verwalten von Dienstprinzipalrollen

Die Azure CLI enthält die folgenden Befehle zum Verwalten von Rollenzuweisungen:

* [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)
* [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)
* [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete)

Standardmäßig hat ein Dienstprinzipal die Rolle **Mitwirkender**. Diese Rolle besitzt uneingeschränkte Berechtigungen für Lese- und Schreibvorgänge in einem Azure-Konto. Die Rolle **Leser** ist stärker eingeschränkt und bietet schreibgeschützten Zugriff.  Weitere Informationen zur rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) finden Sie unter [Rollenbasierte Zugriffssteuerung: Integrierte Rollen](/azure/active-directory/role-based-access-built-in-roles).

Das folgende Beispiel fügt die Rolle **Leser** hinzu und entfernt die Rolle **Mitwirkender**:

```azurecli-interactive
az role assignment create --assignee APP_ID --role Reader
az role assignment delete --assignee APP_ID --role Contributor
```

> [!NOTE]
> Wenn Ihr Konto nicht über Berechtigungen zum Zuweisen einer Rolle verfügt, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass Ihr Konto keine Berechtigung zum Ausführen der Aktion „Microsoft.Authorization/roleAssignments/write“ hat. Bitten Sie den Azure Active Directory-Administrator um die Verwaltung von Rollen.

Durch das Hinzufügen einer Rolle werden zuvor zugewiesene Berechtigungen _nicht_ eingeschränkt. Beim Einschränken der Berechtigungen eines Dienstprinzipals sollte die Rolle __Mitwirkender__ entfernt werden.

Die Änderungen können durch Auflisten der zugewiesenen Rollen überprüft werden:

```azurecli-interactive
az role assignment list --assignee APP_ID
```

## <a name="sign-in-using-a-service-principal"></a>Anmelden mithilfe eines Dienstprinzipals

Testen Sie die Anmeldeinformationen und Berechtigungen des neuen Dienstprinzipals, indem Sie sich anmelden. Für die Anmeldung mit einem Dienstprinzipal benötigen Sie `appId`, `tenant` und Anmeldeinformationen.

So melden Sie sich mit einem Dienstprinzipal und einem Kennwort an:

```azurecli-interactive
az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
```

Um sich mit einem Zertifikat anmelden zu können, muss es lokal als PEM- oder DER-Datei im ASCII-Format verfügbar sein. Bei Verwendung einer PEM-Datei müssen der private Schlüssel (**PRIVATE KEY**) und das Zertifikat (**CERTIFICATE**) gemeinsam innerhalb der Datei angefügt werden.

```azurecli-interactive
az login --service-principal --username APP_ID --tenant TENANT_ID --password /path/to/cert
```

Weitere Informationen zum Anmelden mit einem Dienstprinzipal finden Sie unter [Anmelden mit der Azure CLI](authenticate-azure-cli.md).

## <a name="reset-credentials"></a>Zurücksetzen von Anmeldeinformation

Wenn Sie die Anmeldeinformationen für einen Dienstprinzipal vergessen haben, verwenden Sie [az ad sp credential reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset). Der Befehl zum Zurücksetzen akzeptiert die gleichen Argumente wie `az ad sp create-for-rbac`.

```azurecli-interactive
az ad sp credential reset --name APP_ID
```
