---
title: Konfigurieren Ihrer lokalen JavaScript-Umgebung für die Azure-Entwicklung
description: Hier erfahren Sie, wie Sie eine lokale JavaScript-Entwicklungsumgebung für die Arbeit mit Azure einrichten – einschließlich einem Editor, den Azure SDK-Bibliotheken, optionalen Tools und den erforderlichen Anmeldeinformationen für die Bibliothekauthentifizierung.
ms.date: 07/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2285e79ea62d2de961fd7d4dc7647fec2312b83c
ms.sourcegitcommit: 7be67fb768fb5e19f7de573068cc1376b3d90d1f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85911184"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Konfigurieren Ihrer lokalen JavaScript-Entwicklungsumgebung für Azure

Beim Erstellen von Cloudanwendungen ziehen es Entwickler in der Regel vor, Code auf Ihren lokalen Arbeitsstationen zu testen, bevor sie diesen Code in einer Cloudumgebung wie Azure bereitstellen. Die lokale Entwicklung bietet Ihnen den Vorteil einer größeren Vielfalt an Tools und einer vertrauten Entwicklungsumgebung.

Dieser Artikel enthält die Einrichtungsanweisungen zum Erstellen und Überprüfen einer lokalen Entwicklungsumgebung, die für JavaScript mit Azure geeignet ist.

## <a name="one-time-subscription-creation"></a>Einmalige Abonnementerstellung

Azure-Ressourcen werden innerhalb eines Abonnements erstellt, bei dem es sich um die Abrechnungseinheit für die Verwendung von Azure handelt. Sie können zwar kostenlose Ressourcen erstellen (jedes Abonnement bietet eine kostenlose Ressource für die meisten Dienste), die Erstellung von kostenpflichtigen Ressourcen wird jedoch empfohlen, wenn Sie Ihre Ressource in der Produktionsumgebung bereitstellen möchten.

* Wenn Sie bereits ein Abonnement haben, müssen Sie kein neues erstellen. Greifen Sie über das [Azure-Portal](https://portal.azure.com) auf Ihr vorhandenes Abonnement zu.
* [Abonnement mit kostenloser Testversion starten]()

## <a name="one-time-installation"></a>Einmalige Installation

Um mit einer Azure-Ressource und JavaScript auf Ihrer lokalen Arbeitsstation entwickeln zu können, müssen Sie Folgendes einmalig installieren:

|Name/Installationsprogramm|BESCHREIBUNG|
|--|--|
|[Node.js]()|Installieren Sie die aktuelle Laufzeitumgebung mit langfristigem Support (Long-Term Support, LTS) für die Entwicklung auf lokalen Arbeitsstationen. |
| npm (mit modernen Versionen von Node.js installiert) oder [Yarn]()|Paket-Manager zum Installieren von Azure SDK-Bibliotheken.|
|[VSCode](https://aka.ms/vscode-deploy)| VSCode bietet eine großartige JavaScript-Integrations- und Codierungsumgebung, ist aber nicht erforderlich. Sie können einen beliebigen Code-Editor verwenden. Wenn Sie einen anderen Editor verwenden, beachten Sie die Informationen in diesem Dokument zur Integration in Azure, oder verwenden Sie die Azure CLI.|
|[Azure-Befehlszeilenschnittstelle]()|Sie können die Azure CLI verwenden, um Azure-Ressourcen über eine Befehlszeile, ein Terminal oder eine Bash-Shell zu erstellen und zu verwalten.|

> [!CAUTION]
> Wenn Sie eine Azure-Ressource als Laufzeitumgebung für Ihren Code verwenden möchten, z. B. eine Azure-Web-App oder eine Azure-Containerinstanz, sollten Sie überprüfen, ob Ihre lokale Node.js-Entwicklungsumgebung der Azure-Ressourcenlaufzeit entspricht, die Sie verwenden möchten.

### <a name="optional-local-installations"></a>Optionale lokale Installationen

Die folgenden allgemeinen Installationen lokaler Arbeitsstationen sind optional, um Sie bei den lokalen Entwicklungsaufgaben zu unterstützen.

|Name/Installationsprogramm|BESCHREIBUNG|
|--|--|
| [git](https://git-scm.com/downloads) | Befehlszeilentools für die Quellcodeverwaltung. Wenn Sie es vorziehen, können Sie ein anderes Quellcodeverwaltungstool verwenden. |

## <a name="one-time-configuration-of-service-principal"></a>Einmalkonfiguration des Dienstprinzipals

Jeder Azure-Dienst verfügt über einen Authentifizierungsmechanismus. Dazu können Schlüssel und Endpunkte, Verbindungszeichenfolgen oder andere Mechanismen zählen. Um bewährten Methoden zu entsprechen, erstellen Sie Ressourcen und authentifizieren Sie sich bei Ressourcen mithilfe eines Dienstprinzipals. Mit einem Dienstprinzipal können Sie den Zugriffsbereich für den unmittelbaren Entwicklungsbedarf konkret definieren.

Konzeptionell umfassen die Erstellung und Verwendung eines Dienstprinzipals folgende Schritte:

* Melden Sie sich mit Ihrem einzelnen Benutzerkonto, z. B. joe@microsoft.com, bei Azure an.
* Erstellen Sie einen benannten Dienstprinzipal mit einem bestimmten Bereich. Da Sie in den meisten Schnellstarts aufgefordert werden, eine Azure-Ressource zu erstellen, muss der Dienstprinzipal Ressourcen erstellen können.
* Melden Sie sich mit Ihrem Benutzerkonto bei Azure ab.
* Authentifizieren Sie sich mit dem Dienstprinzipal programmgesteuert bei Azure.
* Der Dienstprinzipal erstellt eine Azure-Ressource und verwendet den Dienst, der dem Dienst zugeordnet ist.

### <a name="create-service-principal"></a>Erstellen eines Dienstprinzipals

Um die Erstellung des Dienstprinzipals zu vereinfachen, verwenden Sie die folgenden Schritte und das bereitgestellte Skript, um Ihren Dienstprinzipal zur Verwendung mit Azure-Schnellstarts zu erstellen. In den folgenden Schritten wird der Name `JOE` als Beispielbenutzername verwendet. Ersetzen Sie ihn durch ihren eigenen Namen oder E-Mail-Alias.

1. Öffnen Sie VSCode, und installieren Sie die Erweiterung [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Diese Erweiterung ermöglicht es Ihnen, Azure CLI-Befehle in der Skriptdatei zeilenweise auszuführen. Während Sie die einzelnen Befehle ausführen, wird ein benachbartes Dokument in VSCcode geöffnet, um die Ergebnisse anzuzeigen.

1. Erstellen Sie eine neue Datei mit dem Namen `create-service-principal.sh`, und kopieren Sie die folgenden Azure-Befehle in die Datei:

    ```azurecli
    # Replace ALL-CAPS variables with your own values

    ####################################
    # Login as you
    ####################################

    # Login - command opens browser, select your account to finish authentication, then close browser
    az login

    ####################################
    # Optional, set default subscription
    ####################################

    # If you have more than 1 subscription, use the `list` command to find the subscription, then use the `set` command to set the default by name or id
    az account list
    az account set --subscription MYCOMPANYSUBSCRIPTION

    ####################################
    # Create service principal
    ####################################

    # Create a service principal with a name that indicates its purpose and owner - the response includes the `appId` which is necessary in some of the remaining commands
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment

    ####################################
    # Add role of contributor
    ####################################

    # Add contributor role to service principal so it can create Azure resources
    az role assignment create --assignee APP-ID --role CONTRIBUTOR

    ####################################
    # Optional, verify role assignment
    ####################################

    # Verify role assignment for service principal
    az role assignment list --assignee APP-ID

    ####################################
    # Logout
    ####################################

    # Logout off Azure CLI
    az logout
    ```

    In den restlichen Schritten in diesem Verfahren platzieren Sie für jede Zeile in der Datei, die **nicht** mit `#` beginnt, den VSCode-Cursor auf die Zeile, und **klicken Sie dann mit der rechten Maustaste**, um **Zeile im Editor auszuführen** auszuwählen.

    :::image type="content" source="media/development-setup/vscode-rightclick-run-line-in-editor.png" alt-text="In den restlichen Schritten in diesem Verfahren platzieren Sie für jede Zeile in der Datei, die nicht mit „#“ beginnt, den VSCode-Cursor auf die Zeile, und klicken Sie dann mit der rechten Maustaste, um „Zeile im Editor auszuführen“ auszuwählen.":::

1. Verwenden Sie Rechtsklick/„Zeile im Editor ausführen“ in der folgenden Zeile, um sich mithilfe der Azure CLI mit Ihrem eigenen Benutzerkonto bei Azure zu authentifizieren. Mit diesem Befehl wird ein Internetbrowser geöffnet. Wählen Sie Ihr Azure-Konto aus. Nachdem Sie Ihr Konto authentifiziert haben, schließen Sie das Browserfenster. Es wird für die restlichen Aufgaben nicht mehr benötigt.

    ```azurecli
    az login
    ```

    Die Antwort enthält alle Abonnements, auf die Sie Zugriff haben. Sie werden in einem anderen VSCode-Dokument als JSON-Array angezeigt. Suchen Sie die Eigenschaft `name` oder `id`. Sie benötigen einen dieser Werte für die restlichen Befehle.

    ```json
    [  {
    "cloudName": "AzureCloud",
    "id": "320d9379-aaaa-bbbb-cccc-52f2b0fc40ac",
    "isDefault": false,
    "name": "contoso-development-team",
    "state": "Enabled",
    "tenantId": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47",
    "user": {
      "name": "joe@contoso.com",
      "type": "user"
    }
    }]
    ```

    Das mit `isDefault: true` markierte Abonnement empfängt die restlichen Befehle. Wenn Sie das Standardabonnement ändern müssen, verwenden Sie den Befehl `az account set --subscription <name or id>`.


<a name='create-service-principal-command'></a>

1. Verwenden Sie in der folgenden Zeile Rechtsklick/„Zeile im Editor ausführen“, um den Dienstprinzipal zu erstellen, der mit Ihrem Benutzerkonto verknüpft ist. Dieser Dienstprinzipal hat aufgrund des Parameters `--skip-assignment` noch keine bereichsbezogenen Berechtigungen.


    ```azurecli
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment
    ```

    Der Dienstprinzipalname ist `JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS`. Eine Liste aller Dienstprinzipale, die Ihrem Azure-Benutzerkonto zugeordnet sind, finden Sie im Azure-Portal unter der Anwendungsliste des Active Directory-Diensts.

    Das Ergebnis enthält Informationen, die Sie benötigen: `appId` und `password`. Speichern Sie die Datei mit dem Namen `create-service-principal.json`.

    ```json
    {
      "appId": "93453d56-aaaa-bbbb-cccc-db600ecc4f6a",
      "displayName": "JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "name": "http://JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "password": "d88b21e0-aaaa-bbbb-cccc-e1e9b06d50f6",
      "tenant": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47"
    }
    ```

1. Verwenden Sie Rechtsklick/„Zeile im Editor ausführen“ in der folgenden Zeile, um die bereichsbezogene Berechtigung zum Erstellen von Azure-Ressourcen zuzuweisen. Der Bereich `CONTRIBUTOR` ermöglicht dem Dienstprinzipal das Erstellen von Azure-Ressourcen.

    ```azurecli
    az role assignment create --assignee APP-ID --role CONTRIBUTOR
    ```

    Das Ergebnis sieht wie folgt aus:

    ```json
    {
      "canDelegate": null,
      "id": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleAssignments/3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "name": "3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "principalId": "c05d56c9-aaaa-bbbb-cccc-0535d6167ed4",
      "principalType": "ServicePrincipal",
      "roleDefinitionId": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleDefinitions/b24988ac-aaaa-bbbb-cccc-20f7382dd24c",
      "scope": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

    Ab jetzt ist Ihr Dienstprinzipal einsatzbereit.

1. Verwenden Sie Rechtsklick/„Zeile im Editor ausführen“ in der folgenden Zeile, um sich mit dem folgenden Befehl von der Azure CLI abzumelden:

    ```azurecli
    az logout
    ```

## <a name="steps-for-each-new-development-project-setup"></a>Schritte für die Einrichtung aller neuen Entwicklungsprojekte

Da die Azure SDK-Bibliotheken für jeden Dienst einzeln bereitgestellt werden, gibt es nicht ein einziges herunterladbares Paket für den Zugriff auf alle Azure-Ressourcen. Sie installieren jede Bibliothek basierend auf dem Azure-Dienst, den Sie verwenden möchten.

Für jedes neue Projekt mit Azure müssen folgende Aktionen ausgeführt werden:
- Erstellen von Azure-Ressourcen oder Suchen nach Authentifizierungsinformationen für vorhandene Azure-Ressourcen
- Installieren von Azure SDK-Bibliotheken aus npm oder Yarn. Informationen zu [Bibliotheksversionen](#library-versions).
- Sicheres Verwalten von Authentifizierungsinformationen innerhalb des Projekts. Eine gängige Methode ist die Verwendung von **[Dotenv](https://www.npmjs.com/package/dotenv)** , um Umgebungsvariablen aus einer `.env`-Datei zu lesen. Hinzufügen der `.env`-Datei zur `.gitignore`-Datei, damit die `.env`-Datei nicht in die Quellcodeverwaltung eingecheckt wird.

### <a name="library-versions"></a>Bibliotheksversionen

Alle Azure-Bibliotheken wechseln in den Bereich `@azure`.

| Bibliothekstyp | BESCHREIBUNG|
|--|--|
|Modern|Ist auf `@azure` festgelegt, z. B. [@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob) und [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos), und schließt TypeScript-Typen ein.|
|Ältere Pakete|Beginnen in der Regel mit `azure-`. Viele Pakete, die nicht von Microsoft erstellt werden, beginnen mit diesem Namen. Stellen Sie sicher, dass der Besitzer des Pakets entweder Microsoft oder Azure ist.|

### <a name="create-resource-using-service-principal"></a>Erstellen einer Ressource mithilfe des Dienstprinzipals

Der folgende Abschnitt enthält ein Beispiel für die Erstellung einer Azure-Dienstressource mit einem Dienstprinzipal. Zur Anmeldung mit einem Dienstprinzipal benötigen Sie `appId`, `tenant`und `password`, die Sie im Verfahren [Erstellen eines Dienstprinzipals](#create-service-principal) in `create-service-principal.json` gespeichert haben.

1. Öffnen Sie VSCode, und verwenden Sie die zuvor installierte Erweiterung [Azure CLI-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Diese Erweiterung ermöglicht es Ihnen, Azure CLI-Befehle in der Skriptdatei zeilenweise auszuführen. Während Sie die einzelnen Befehle ausführen, wird ein benachbartes Dokument in VSCcode geöffnet, um die Ergebnisse anzuzeigen.

1. Erstellen Sie eine neue Datei mit dem Namen `create-service-resource.sh`, und kopieren Sie die folgenden Azure-Befehle in die Datei:

    ```azurecli
    ####################################
    # Login as service principal
    ####################################
    # User name for command is the app id
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID

    ####################################
    # Create resource group
    ####################################

    # Create resource group in westus region - check your quickstart if it requires a specific region, then change this value to the appropriate region
    # Common naming convention for resource group is `USERNAME-REGION-PURPOSE`
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP

    ####################################
    # Create specific service resource
    ####################################

    # Create resource in westus
    # This is an example of creating a Cognitive Services LUIS resource
    # Review your quickstart to find the exact command
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes

    ####################################
    # Get resource keys
    ####################################

    # Get resource keys
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

1. Verwenden Sie in der folgenden Zeile Rechtsklick/„Zeile im Editor ausführen“, um sich mit dem Dienstprinzipal anzumelden. Die Variablen in Großbuchstaben wurden in der Antwort vom [vorherigen Befehl zum Erstellen des Dienstprinzipals](#create-service-principal-command) zurückgegeben.

    ```azurecli
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
    ```

1. Verwenden Sie Rechtsklick/„Zeile im Editor ausführen“ in der folgenden Zeile, um eine Ressourcengruppe für alle Ressourcen zu erstellen, die Sie für den Schnellstart erstellen müssen. Die Ressourcengruppenregion kann nur Ressourcen aus dieser Region enthalten.

    ```azurecli
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

    Wenn Sie mit den Schnellstartressourcen fertig sind, können Sie die Ressourcengruppe löschen. Dabei werden die Ressourcen in einer Aktion gelöscht.

1. Verwenden Sie in der folgenden Zeile Rechtsklick/„Zeile im Editor ausführen“, um eine Cognitive Services-LUIS-Ressource zu erstellen. Dies ist ein Beispiel, Ihre eigene Ressource hat einen anderen Befehl.

    ```azurecli
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes
    ```

    Die LUIS-Ressource verwendet einen Schlüssel und einen Endpunkt, die Sie in den Schnellstarts für LUIS verwenden müssen.

1. Verwenden Sie in der folgenden Zeile Rechtsklick/„Zeile im Editor ausführen“, um den LUIS-Schlüssel und -Endpunkt abzurufen. Der Schlüssel und der Endpunkt werden bei der Authentifizierung beim LUIS-Dienst verwendet.

    ```azurecli
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

### <a name="create-environment-variables-for-the-azure-libraries"></a>Erstellen von Umgebungsvariablen für die Azure-Bibliotheken

Zum Verwenden der Azure-Einstellungen, die von den Azure SDK-Bibliotheken für den Zugriff auf die Azure-Cloud benötigt werden, legen Sie die gängigsten Werte auf Umgebungsvariablen fest. Mit den folgenden Befehlen werden die Umgebungsvariablen auf die lokale Arbeitsstation festgelegt. Ein weiterer gebräuchlicher Mechanismus ist die Verwendung des NPM-Pakets `DOTENV`, um eine `.env`-Datei für diese Einstellungen zu erstellen. Wenn Sie eine `.env`-Datei verwenden möchten, achten Sie darauf, die Datei nicht in die Quellcodeverwaltung einzuchecken. Das Hinzufügen der `.env`-Datei zur `.ignore`-Datei von Git ist die Standardmethode, um sicherzustellen, dass diese Einstellungen in die Quellcodeverwaltung eingecheckt werden.

In den folgenden Beispielen ist die Client-ID die Dienstprinzipal-ID und das Dienstprinzipalgeheimnis.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
```

---

Ersetzen Sie die in diesen Befehlen angezeigten Werte durch die Werte Ihres spezifischen Dienstprinzipals.

### <a name="install-npm-packages"></a>Installieren von npm-Paketen

Wir empfehlen, für jedes Projekt immer einen separaten Order und eine eigene `package.json`-Datei zu erstellen. Dazu gehen Sie folgendermaßen vor:

1. Öffnen Sie ein Terminal, eine Eingabeaufforderung oder eine Bash-Shell, und erstellen Sie einen neuen Ordner für das Projekt. Wechseln Sie dann in den neuen Ordner.

    ```console
    mkdir MY-NEW-PROJECT && cd MY-NEW-PROJECT
    ```

1. Initialisieren Sie die Paketdatei:

    ```console
    npm init -y
    ```

    Mit diesem Befehl wird der npm-Befehl ausgeführt, um die Datei „package.json“ zu erstellen und die Mindesteigenschaften zu initialisieren. Wenn Sie Azure SDK-Bibliothekspakete mit npm oder Yarn installieren, erfasst die Datei „package.json“ diese Installationsinformationen.

1. Installieren Sie die Azure SDK-Bibliotheken, die Sie für den Schnellstart benötigen. Der folgende Befehl ist ein Beispiel.

    ```console
    npm install @azure/cognitiveservices-luis-runtime
    ```

## <a name="use-source-control"></a>Verwenden der Quellcodeverwaltung

Wir empfehlen, sich anzugewöhnen, bei jedem Start eines Projekts ein Quellcodeverwaltungs-Repository zu erstellen. Wenn Sie Git installiert haben, führen Sie den folgenden Befehl aus:

```bash
git init
```

Von dort aus können Sie Befehle wie `git add` und `git commit` ausführen, um Änderungen zu committen. Durch regelmäßige Commits von Änderungen erstellen Sie einen Commitverlauf, über den Sie jeden vorherigen Zustand wiederherstellen können.

Um eine Onlinesicherung Ihres Projekts zu erstellen, empfehlen wir außerdem, dass Sie Ihr Repository auf [GitHub](https://github.com) oder [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops) hochladen. Wenn Sie zuerst ein lokales Repository initialisiert haben, verwenden Sie `git remote add`, um das lokale Repository an GitHub oder Azure DevOps anzufügen.

Dokumentation zu git finden Sie auf [git-scm.com/docs](https://git-scm.com/docs) und im Internet.

Visual Studio Code umfasst eine Reihe integrierter git-Features. Weitere Informationen finden Sie unter [Verwenden der Versionskontrolle in VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

Sie können auch jedes andere Quellcodeverwaltungs-Tool Ihrer Wahl verwenden. Git ist lediglich eins der am häufigsten verwendeten und unterstützten.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer statischen Website in Azure in Visual Studio Code](tutorial-vscode-static-website-node-01.md)