---
title: Konfigurieren Ihrer lokalen JavaScript-Umgebung für die Azure-Entwicklung
description: Hier erfahren Sie, wie Sie eine lokale JavaScript-Entwicklungsumgebung für die Arbeit mit Azure einrichten – einschließlich einem Editor, den Azure SDK-Bibliotheken, optionalen Tools und den erforderlichen Anmeldeinformationen für die Bibliothekauthentifizierung.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-js, azure-sdk-javascript-ai-text-analytics-5.0.0
ms.openlocfilehash: 2e61facd2065773dcbef7d0cdd20353671121422
ms.sourcegitcommit: c3a1c9051b89870f6bfdb3176463564963b97ba4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437220"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Konfigurieren Ihrer lokalen JavaScript-Entwicklungsumgebung für Azure

Beim Erstellen von Cloudanwendungen ziehen es Entwickler in der Regel vor, Code auf Ihren lokalen Arbeitsstationen zu testen, bevor sie diesen Code in einer Cloudumgebung wie Azure bereitstellen. Die lokale Entwicklung bietet Ihnen den Vorteil einer größeren Vielfalt an Tools und einer vertrauten Entwicklungsumgebung.

Dieser Artikel enthält die Einrichtungsanweisungen zum Erstellen und Überprüfen einer lokalen Entwicklungsumgebung, die für JavaScript mit Azure geeignet ist.

## <a name="one-time-subscription-creation"></a>Einmalige Abonnementerstellung

Azure-Ressourcen werden innerhalb eines Abonnements erstellt, bei dem es sich um die Abrechnungseinheit für die Verwendung von Azure handelt. Sie können zwar kostenlose Ressourcen erstellen (jedes Abonnement bietet eine kostenlose Ressource für die meisten Dienste), die Erstellung von kostenpflichtigen Ressourcen wird jedoch empfohlen, wenn Sie Ihre Ressource in der Produktionsumgebung bereitstellen möchten.

|type|BESCHREIBUNG|
|--|--|
|[Abonnement für die Testversion](https://azure.microsoft.com/free/cognitive-services)|Erstellen Sie ein _kostenloses_ Testabonnement.|
|[Bestehendes Abonnement](https://portal.azure.com)|Wenn Sie bereits über ein Abonnement verfügen, greifen Sie im Azure-Portal, über die Azure CLI oder über JavaScript auf das vorhandene Abonnement zu.|
|[Mehrere Abonnements](/azure/governance/management-groups/create-management-group-javascript)|Wenn Sie mehrere Abonnements verwalten müssen, lesen Sie, wie Sie eine Verwaltungsgruppe mit JavaScript erstellen.|

## <a name="one-time-installation"></a>Einmalige Installation

Um mit einer Azure-Ressource und JavaScript auf Ihrer lokalen Arbeitsstation entwickeln zu können, müssen Sie Folgendes einmalig installieren:

|Name/Installationsprogramm|BESCHREIBUNG|
|--|--|
|[Node.js](https://www.npmjs.com/)|Installieren Sie die aktuelle Laufzeitumgebung mit langfristigem Support (Long-Term Support, LTS) für die Entwicklung auf lokalen Arbeitsstationen. |
| npm (mit modernen Versionen von Node.js installiert) oder [Yarn](https://yarnpkg.com/)|Paket-Manager zum Installieren von Azure SDK-Bibliotheken.|
|[Visual Studio Code](https://code.visualstudio.com/)| Visual Studio Code bietet eine großartige JavaScript-Integrationsumgebung und -Codierungsumgebung, ist aber nicht erforderlich. Sie können einen beliebigen Code-Editor verwenden. Wenn Sie einen anderen Editor verwenden, beachten Sie die Informationen in diesem Dokument zur Integration in Azure, oder verwenden Sie die Azure CLI.|
|[Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)|Sie können die Azure CLI verwenden, um Azure-Ressourcen über eine Befehlszeile, ein Terminal oder eine Bash-Shell zu erstellen und zu verwalten.|

> [!CAUTION]
> Wenn Sie eine Azure-Ressource als Laufzeitumgebung für Ihren Code verwenden möchten, z. B. eine Azure-Web-App oder eine Azure-Containerinstanz, sollten Sie überprüfen, ob Ihre lokale Node.js-Entwicklungsumgebung der Azure-Ressourcenlaufzeit entspricht, die Sie verwenden möchten.

### <a name="optional-local-installations"></a>Optionale lokale Installationen

Die folgenden allgemeinen Installationen lokaler Arbeitsstationen sind optional, um Sie bei den lokalen Entwicklungsaufgaben zu unterstützen.

|Name/Installationsprogramm|BESCHREIBUNG|
|--|--|
| [git](https://git-scm.com/downloads) | Befehlszeilentools für die Quellcodeverwaltung. Wenn Sie es vorziehen, können Sie ein anderes Quellcodeverwaltungstool verwenden. |

## <a name="one-time-configuration-of-service-principal"></a>Einmalkonfiguration des Dienstprinzipals

Jeder Azure-Dienst verfügt über einen Authentifizierungsmechanismus. Dazu können Schlüssel und Endpunkte, Verbindungszeichenfolgen oder andere Mechanismen zählen. Gemäß den bewährten Methoden erstellen Sie Ressourcen und authentifizieren sich bei Ressourcen mithilfe eines [Dienstprinzipals](node-sdk-azure-authenticate-principal.md). Mit einem Dienstprinzipal können Sie den Zugriffsbereich für den unmittelbaren Entwicklungsbedarf konkret definieren.

Schritte zum **Erstellen des Dienstprinzipals** : 

1. Melden Sie sich mit Ihrem Benutzerkonto bei Azure an.
1. Erstellen Sie einen _benannten_ Dienstprinzipal mit einem bestimmten Bereich. Da Sie in den meisten Schnellstarts aufgefordert werden, eine Azure-Ressource zu erstellen, muss der Dienstprinzipal Ressourcen erstellen können.
1. Melden Sie sich mit Ihrem Benutzerkonto bei Azure ab.

Schritte zum **Verwenden eines Dienstprinzipals** :

1. Authentifizieren Sie sich mit dem Dienstprinzipal und mit einem Zertifikat, mit Umgebungsvariablen oder mit einer Datei vom Typ `.json` programmgesteuert bei Azure. 
1. Erstellen Sie mit dem Dienstprinzipal Azure-Ressourcen, und verwenden Sie den Dienst.

Informieren Sie sich, wie Sie [einen Dienstprinzipal erstellen](node-sdk-azure-authenticate-principal.md). Speichern Sie unbedingt die Antwort aus dem Erstellungsschritt. Sie benötigen die Werte für `appId`, `tenant` und `password` der Antwort, damit der Dienstprinzipal verwendet werden kann.

[Erstellen Sie Azure-Ressourcen mit Ihrem Dienstprinzipal.](/cli/azure/create-an-azure-service-principal-azure-cli#create-a-resource-using-service-principal)

## <a name="steps-for-each-new-development-project-setup"></a>Schritte für die Einrichtung aller neuen Entwicklungsprojekte

Die [Azure SDK-Bibliotheken](../azure-sdk-library-package-index.md) werden für jeden Dienst einzeln bereitgestellt. Sie installieren jede Bibliothek basierend auf dem Azure-Dienst, den Sie verwenden müssen.

Für jedes neue Projekt mit Azure müssen folgende Aktionen ausgeführt werden:
- Erstellen Sie Azure-Ressourcen, und speichern Sie zugehörige Schlüssel oder die Konfiguration an einem [sicheren Speicherort]().
- Installieren von Azure SDK-Bibliotheken aus npm oder Yarn. 
- Verwenden Sie den Dienstprinzipal für die Authentifizierung bei Azure SDKs, und verwenden Sie anschließend Konfigurationsinformationen für den Zugriff auf bestimmte Dienste.

## <a name="securing-configuration-information"></a>Schützen von Konfigurationsinformationen

Sie haben mehrere Optionen zum Speichern von Konfigurationsinformationen:
- [Dotenv](https://www.npmjs.com/package/dotenv) ist ein beliebtes npm-Paket zum Lesen von Umgebungsvariablen aus einer Datei vom Typ `.env`. Hinzufügen der `.env`-Datei zur `.gitignore`-Datei, damit die `.env`-Datei nicht in die Quellcodeverwaltung eingecheckt wird.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) zum Erstellen und Verwalten von Schlüsseln für den Zugriff auf und die Verschlüsselung von Cloudressourcen, Apps und Lösungen

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

## <a name="install-npm-packages"></a>NPM-Pakete installieren

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
    npm install @azure/ai-text-analytics@5.0.0
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

* [Erstellen und Verwenden eines Dienstprinzipals](node-sdk-azure-authenticate-principal.md)
* [Authentifizieren mit den Azure-Modulen für Node.js](node-sdk-azure-authenticate.md)
* [Bereitstellen einer statischen Website in Azure in Visual Studio Code](../tutorial-vscode-static-website-node-01.md)