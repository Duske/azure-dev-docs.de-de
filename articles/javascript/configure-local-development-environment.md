---
title: Konfigurieren Ihrer lokalen JavaScript-Umgebung für die Azure-Entwicklung
description: Hier erfahren Sie, wie Sie eine lokale JavaScript-Entwicklungsumgebung für die Arbeit mit Azure einrichten – einschließlich einem Editor, den Azure SDK-Bibliotheken, optionalen Tools und den erforderlichen Anmeldeinformationen für die Bibliothekauthentifizierung.
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 93d76b4268d9d9b1533599ce474fe0c554723e28
ms.sourcegitcommit: 823d5e5b8bbac36fa08a578a0eb2efaa0239bfb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2020
ms.locfileid: "91128949"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Konfigurieren Ihrer lokalen JavaScript-Entwicklungsumgebung für Azure

Beim Erstellen von Cloudanwendungen ziehen es Entwickler in der Regel vor, Code auf Ihren lokalen Arbeitsstationen zu testen, bevor sie diesen Code in einer Cloudumgebung wie Azure bereitstellen. Die lokale Entwicklung bietet Ihnen den Vorteil einer größeren Vielfalt an Tools und einer vertrauten Entwicklungsumgebung.

Dieser Artikel enthält die Einrichtungsanweisungen zum Erstellen und Überprüfen einer lokalen Entwicklungsumgebung, die für JavaScript mit Azure geeignet ist.

## <a name="one-time-subscription-creation"></a>Einmalige Abonnementerstellung

Azure-Ressourcen werden innerhalb eines Abonnements erstellt, bei dem es sich um die Abrechnungseinheit für die Verwendung von Azure handelt. Sie können zwar kostenlose Ressourcen erstellen (jedes Abonnement bietet eine kostenlose Ressource für die meisten Dienste), die Erstellung von kostenpflichtigen Ressourcen wird jedoch empfohlen, wenn Sie Ihre Ressource in der Produktionsumgebung bereitstellen möchten.

* Wenn Sie bereits ein Abonnement haben, müssen Sie kein neues erstellen. Greifen Sie über das [Azure-Portal](https://portal.azure.com) auf Ihr vorhandenes Abonnement zu.
* [Abonnement mit kostenloser Testversion starten](https://azure.microsoft.com/free/cognitive-services)

## <a name="one-time-installation"></a>Einmalige Installation

Um mit einer Azure-Ressource und JavaScript auf Ihrer lokalen Arbeitsstation entwickeln zu können, müssen Sie Folgendes einmalig installieren:

|Name/Installationsprogramm|BESCHREIBUNG|
|--|--|
|[Node.js](https://www.npmjs.com/)|Installieren Sie die aktuelle Laufzeitumgebung mit langfristigem Support (Long-Term Support, LTS) für die Entwicklung auf lokalen Arbeitsstationen. |
| npm (mit modernen Versionen von Node.js installiert) oder [Yarn](https://yarnpkg.com/)|Paket-Manager zum Installieren von Azure SDK-Bibliotheken.|
|[Visual Studio Code](https://code.visualstudio.com/)| Visual Studio Code bietet eine großartige JavaScript-Integrationsumgebung und -Codierungsumgebung, ist aber nicht erforderlich. Sie können einen beliebigen Code-Editor verwenden. Wenn Sie einen anderen Editor verwenden, beachten Sie die Informationen in diesem Dokument zur Integration in Azure, oder verwenden Sie die Azure CLI.|
|[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)|Sie können die Azure CLI verwenden, um Azure-Ressourcen über eine Befehlszeile, ein Terminal oder eine Bash-Shell zu erstellen und zu verwalten.|

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

Informieren Sie sich, wie Sie [einen Dienstprinzipal erstellen](node-sdk-azure-authenticate-principal.md). Speichern Sie unbedingt die Antwort aus dem Erstellungsschritt. Sie benötigen die Werte für `appId`, `tenant` und `password`, damit der Dienstprinzipal verwendet werden kann.

## <a name="steps-for-each-new-development-project-setup"></a>Schritte für die Einrichtung aller neuen Entwicklungsprojekte

Da die Azure SDK-Bibliotheken für jeden Dienst einzeln bereitgestellt werden, gibt es nicht ein einziges herunterladbares Paket für den Zugriff auf alle Azure-Ressourcen. Sie installieren jede Bibliothek basierend auf dem Azure-Dienst, den Sie verwenden möchten.

Für jedes neue Projekt mit Azure müssen folgende Aktionen ausgeführt werden:
- Erstellen von Azure-Ressourcen oder Suchen nach Authentifizierungsinformationen für vorhandene Azure-Ressourcen
- Installieren von Azure SDK-Bibliotheken aus npm oder Yarn. Informationen zu [Bibliotheksversionen](#library-versions).
- Sicheres Verwalten von Authentifizierungsinformationen innerhalb des Projekts. Eine gängige Methode ist die Verwendung von **[Dotenv](https://www.npmjs.com/package/dotenv)** , um Umgebungsvariablen aus einer `.env`-Datei zu lesen. Hinzufügen der `.env`-Datei zur `.gitignore`-Datei, damit die `.env`-Datei nicht in die Quellcodeverwaltung eingecheckt wird.

### <a name="library-versions"></a>Bibliotheksversionen

[Azure-Bibliotheken](azure-sdk-library-package-index.md) verwenden in der Regel den Bereich `@azure`.

Die aktuellen Bibliotheken verwenden den Bereich `@azure`. Ältere Pakete von Microsoft beginnen in der Regel mit `azure-`. Viele Pakete, die nicht von Microsoft erstellt werden, beginnen mit diesem Namen. Stellen Sie sicher, dass der Besitzer des Pakets entweder Microsoft oder Azure ist.

## <a name="create-azure-resource-with-service-principal"></a>Erstellen einer Azure-Ressource mit einem Dienstprinzipal

Verwenden Sie die Azure-Befehlszeilenschnittstelle, um [eine Azure-Ressource mithilfe des Dienstprinzipals zu erstellen](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-a-resource-using-service-principal).

## <a name="use-service-principal-in-javascript"></a>Verwenden des Dienstprinzipals in JavaScript

[Verwenden Sie den Dienstprinzipal](node-sdk-azure-authenticate-principal.md#using-the-service-principal), wenn Sie sich bei einer Azure-Clientbibliothek authentifizieren, und nicht Ihr persönliches Benutzerkonto.

## <a name="create-environment-variables-for-the-azure-libraries"></a>Erstellen von Umgebungsvariablen für die Azure-Bibliotheken

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

## <a name="install-npm-packages"></a>Installieren von npm-Paketen

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
* [Bereitstellen einer statischen Website in Azure in Visual Studio Code](tutorial-vscode-static-website-node-01.md)