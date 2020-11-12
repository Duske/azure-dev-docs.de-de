---
title: Konfigurieren Ihrer lokalen Python-Umgebung für die Azure-Entwicklung
description: Hier erfahren Sie, wie Sie eine lokale Python-Entwicklungsumgebung für die Arbeit mit Azure einrichten – einschließlich Visual Studio Code, den Azure SDK-Bibliotheken und den erforderlichen Anmeldeinformationen für die Bibliothekauthentifizierung.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b9e3c36199cfe9fa94fa518587b6065f4d9ef9b0
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515151"
---
# <a name="configure-your-local-python-dev-environment-for-azure"></a>Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure

Beim Erstellen von Cloudanwendungen ziehen es Entwickler in der Regel vor, Code auf Ihren lokalen Arbeitsstationen zu testen, bevor sie diesen Code in einer Cloudumgebung wie Azure bereitstellen. Die lokale Entwicklung bietet Ihnen den Vorteil der Geschwindigkeit und einer größeren Vielfalt an Debugtools.

Dieser Artikel enthält die einmalig durchzuführenden Einrichtungsanweisungen zum Erstellen und Überprüfen einer lokalen Entwicklungsumgebung, die für Python in Azure geeignet ist:

- [Installieren der erforderlichen Komponenten](#required-components), nämlich ein Azure-Konto, Python und die Azure CLI.
- [Konfigurieren der Authentifizierung](#configure-authentication), wenn Sie Azure-Bibliotheken für die Bereitstellung, Verwaltung und den Zugriff auf Azure-Ressourcen verwenden.
- Überprüfen des Prozesses der [Verwendung virtueller Python-Umgebungen](#use-python-virtual-environments) für jedes Ihrer Projekte.

Nachdem Sie Ihre Arbeitsstation konfiguriert haben, müssen Sie nur noch minimale zusätzliche Konfigurationen vornehmen, um verschiedene Schnellstarts und Tutorials an anderer Stelle in diesem Developer Center sowie in der Azure-Dokumentation durchzuführen.

Dieses Setup für die lokale Entwicklung ist von der [Bereitstellung von Ressourcen](cloud-development-flow.md) getrennt, aus denen die  *Cloudumgebung* Ihrer Anwendung in Azure besteht. In Ihrem Entwicklungsprozess führen Sie Code in Ihrer lokalen Entwicklungsumgebung aus, der auf diese Cloudressourcen zugreifen kann, aber Ihr Code wird noch nicht für einen [geeigneten Hostingdienst](quickstarts-app-hosting.md) in der Cloud bereitgestellt. Dieser Bereitstellungsschritt wird später ausgeführt, wie im Artikel [Azure-Entwicklungsablauf](cloud-development-flow.md) beschrieben.

## <a name="install-components"></a>Installieren von Komponenten

### <a name="required-components"></a>Erforderliche Komponenten

| Name/Installationsprogramm | Beschreibung |
| --- | --- |
| [Azure-Konto mit einem aktiven Abonnement](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=python-dev-center&mktingSource=environment-setup) | Konten/Abonnements sind kostenlos und umfassen viele kostenlose Dienste. |
| [Python 2.7+ oder 3.5.3+](https://www.python.org/downloads) | Die Runtime der Programmiersprache Python. Wir empfehlen die aktuelle Version von Python 3.x, es sei denn, Sie haben besondere Anforderungen an die Version. |
| [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli) | Bietet eine vollständige Sammlung von CLI-Befehlen zum Bereitstellen und Verwalten von Azure-Ressourcen. Python-Entwickler verwenden die Azure-Befehlszeilenschnittstelle häufig in Verbindung mit benutzerdefinierten Python-Skripts, von denen die Azure-Verwaltungsbibliotheken genutzt werden. |

Hinweise:

- Für jedes Projekt müssen abhängig von Ihren Anforderungen einzelne Azure-Bibliothekspakete installiert werden. Wir empfehlen, dass Sie [virtuelle Python-Umgebungen](#use-python-virtual-environments) für jedes Projekt verwenden. Es steht kein eigenständiges SDK-Installationsprogramm für Python zur Verfügung.
- Obwohl Azure PowerShell in der Regel äquivalent zur Azure CLI ist, empfehlen wir, die Azure CLI bei der Arbeit mit Python zu verwenden.

### <a name="recommended-components"></a>Empfohlene Komponenten

| Name/Installationsprogramm | Beschreibung |
| --- | --- |
| [Visual Studio Code](https://code.visualstudio.com) | Obwohl Sie mit jedem passenden Editor oder jeder geeigneten IDE arbeiten können, ist die kostenlose, schlanke IDE von Microsoft bei Python-Entwicklern sehr beliebt. Eine Einführung finden Sie unter [Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial). |
| [Python-Erweiterungen für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Fügt VS Code Python-Unterstützung hinzu. |
| [Azure-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) | Fügt Unterstützung für eine Vielzahl von Azure-Diensten zu VS Code hinzu. Unterstützung für bestimmte Dienste kann auch einzeln installiert werden. |
| [git](https://git-scm.com/downloads) | Befehlszeilentools für die Quellcodeverwaltung. Sie können verschiedene Quellcodeverwaltungstools verwenden, wenn Sie dies vorziehen. |

### <a name="optional-components"></a>Optionale Komponenten

| Name/Installationsprogramm | Beschreibung |
| --- | --- |
| [Docker-Erweiterung für VS Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Fügt VS Code Docker-Unterstützung hinzu, was hilfreich ist, wenn Sie regelmäßig mit Containern arbeiten. |

### <a name="verify-components"></a>Überprüfen von Komponenten

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung.
1. Überprüfen Sie Ihre Python-Version, indem Sie den Befehl `python --version` ausführen.
1. Überprüfen Sie die Azure-CLI-Version, indem Sie `az --version` ausführen.
1. Überprüfen der VS Code-Installation:
    1. Führen Sie `code .` aus, um VS Code im aktuellen Ordner zu öffnen.
    1. Wählen Sie in VS Code den Befehl **Ansicht** > **Erweiterungen** aus, um die Ansicht mit den Erweiterungen zu öffnen, und vergewissern Sie sich dann, dass in der Liste „Python“ und „Azure-Konto“ angezeigt werden (neben anderen „Azure“-Erweiterungen und „Docker“, wenn Sie diese Erweiterung ebenfalls installiert haben).

## <a name="sign-in-to-azure-from-the-cli"></a>Anmelden bei Azure über die CLI

Melden Sie sich in einem Terminal oder einer Eingabeaufforderung bei Ihrem Azure-Abonnement an:

```azurecli
az login
```

Der Befehl `az` ist der Stammbefehl der Azure CLI. Auf `az` folgen ein oder mehrere spezifische Befehle, z. B. `login`. Informationen finden Sie in der Referenz zum Befehl [az login](/cli/azure/authenticate-azure-cli).

Die Azure CLI behält Ihre Anmeldung normalerweise sitzungsübergreifend bei, aber es ist eine bewährte Methode, immer `az login` auszuführen, wenn Sie ein neues Terminal oder eine neue Eingabeaufforderung öffnen.

## <a name="configure-authentication"></a>Konfigurieren der Authentifizierung

Wie unter [Authentifizieren von Apps mit Azure](azure-sdk-authenticate.md#identity-when-running-the-app-locally) beschrieben, benötigt jeder Entwickler einen Dienstprinzipal, der beim lokalen Testen des App-Codes als Anwendungsidentität verwendet werden kann.

In den folgenden Abschnitten erfahren Sie, wie Sie einen Dienstprinzipal und die Umgebungsvariablen erstellen, durch die die Eigenschaften des Dienstprinzipals bei Bedarf für die Azure-Bibliotheken bereitgestellt werden.

Jeder Entwickler in Ihrer Organisation sollte diese Schritte einzeln ausführen.

### <a name="create-a-service-principal-and-environment-variables-for-development"></a>Erstellen eines Dienstprinzipals und von Umgebungsvariablen für die Entwicklung

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung, in dem bzw. der Sie sich bei der Azure CLI angemeldet haben (`az login`).

1. Erstellen des Dienstprinzipals:

    ```azurecli
    az ad sp create-for-rbac --name localtest-sp-rbac --skip-assignment --sdk-auth > local-sp.json
    ```

    Die Ausgabe dieses Befehls wird in der Datei *local-sp.json* gespeichert. Ausführlichere Informationen zu dem Befehl und den zugehörigen Argumenten finden Sie unter [Aktionen des Befehls „create-for-rbac“](#what-the-create-for-rbac-command-does).

    Wenn Sie in einer Organisation sind, verfügen Sie im Abonnement möglicherweise nicht über die Berechtigung zum Ausführen dieses Befehls. Wenden Sie sich in diesem Fall an die Besitzer des Abonnements, damit sie den Dienstprinzipal für Sie erstellen.

1. Verwenden Sie die folgenden Befehle zum Erstellen der Umgebungsvariablen, die von den Azure-Bibliotheken benötigt werden. (Vom Objekt `DefaultAzureCredential` der Bibliothek „azure-identity“ wird nach diesen Variablen gesucht.)

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
    set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
    set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
    AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
    AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
    ```

    ---

    Ersetzen Sie die in diesen Befehlen angezeigten Werte durch die Werte Ihres spezifischen Dienstprinzipals.

    Um Ihre Abonnement-ID abzurufen, führen Sie den Befehl [`az account show`](/cli/azure/account#az-account-show) aus, und suchen Sie in der Ausgabe nach der Eigenschaft `id`.

    Erstellen Sie als Erleichterung eine Befehlszeilen-Skriptdatei (z. B. *setenv.sh* unter macOS/Linux oder *setenv.cmd* unter Windows), die diese Befehle enthält. Anschließend können Sie das Skript jeweils ausführen, um die Variablen festzulegen, wenn Sie ein Terminal oder eine Eingabeaufforderung für lokale Tests öffnen. Fügen Sie auch diese Skriptdatei nicht der Quellcodeverwaltung hinzu, damit sie nur unter Ihrem Benutzerkonto vorhanden ist.

1. Schützen Sie die Client-ID und den geheimen Clientschlüssel (und alle Dateien, in denen diese gespeichert sind), sodass sie immer innerhalb eines bestimmten Benutzerkontos auf einer Arbeitsstation verbleiben. Speichern Sie diese Eigenschaften niemals in der Quellcodeverwaltung, oder geben Sie sie für andere Entwickler frei. Falls erforderlich, können Sie den Dienstprinzipal löschen und einen neuen erstellen.

    Um eine zusätzliche Sicherheitsebene zu erzeugen, können Sie eine Richtlinie erstellen, die Dienstprinzipale regelmäßig löscht und neu erstellt, wodurch vorherige IDs und geheime Schlüssel ungültig werden.

    Ferner ist ein Dienstprinzipal für die Entwicklung idealerweise nur für nicht produktive Ressourcen autorisiert oder wird innerhalb eines Azure-Abonnements erstellt, das nur zu Entwicklungszwecken verwendet wird. Die Produktionsanwendung würde dann ein gesondertes Abonnement sowie gesonderte Produktionsressourcen verwenden, die nur für die bereitgestellte Cloudanwendung autorisiert sind.

1. Informationen, wie Sie Dienstprinzipale später noch ändern oder löschen können, finden Sie unter [Verwalten von Dienstprinzipalen](how-to-manage-service-principals.md).

#### <a name="what-the-create-for-rbac-command-does"></a>Aktionen des Befehls „create-for-rbac“

Durch den Befehl `az ad create-for-rbac` wird ein Dienstprinzipal für die rollenbasierte Authentifizierung (RBAC) erstellt.

- `ad` bedeutet Azure Active Directory, `sp` bedeutet „Dienstprinzipal“, und `create-for-rbac` bedeutet „Für rollenbasierte Zugriffssteuerung erstellen“, die primäre Form der Autorisierung in Azure. Informationen finden Sie in der Referenz zum Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac).

- Das Argument `--name` sollte innerhalb Ihrer Organisation eindeutig sein und in der Regel den Namen des Entwicklers verwenden, der den Dienstprinzipal verwendet. Wenn Sie dieses Argument weglassen, verwendet die Azure CLI einen generischen Namen der Form `azure-cli-<timestamp>`. Sie können den Dienstprinzipal auch über das Azure-Portal umbenennen, wenn Sie möchten.

- Das Argument `--skip-assignment` erstellt einen Dienstprinzipal ohne Standardberechtigungen. Anschließend müssen Sie dann dem Dienstprinzipal bestimmte Berechtigungen zuweisen, um lokal ausgeführtem Code den Zugriff auf Ressourcen zu gewähren. Verschiedene Schnellstarts und Tutorials enthalten Details zum Autorisieren eines Dienstprinzipals für die beteiligten Ressourcen.

- Durch den Befehl wird eine JSON-Ausgabe bereitgestellt, die in diesem Beispiel in einer Datei namens *local-sp.json* gespeichert wird.

- Das Argument `--sdk-auth` generiert eine JSON-Ausgabe ähnlich den folgenden Werten. Ihre ID-Werte und Ihr Geheimnis unterscheiden sich alle davon:

    <pre>
    {
      "clientId": "12345678-1111-2222-3333-1234567890ab",
      "clientSecret": "abcdef00-4444-5555-6666-1234567890ab",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "tenantId": "00112233-7777-8888-9999-aabbccddeeff",
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
      "resourceManagerEndpointUrl": "https://management.azure.com/",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
      "galleryEndpointUrl": "https://gallery.azure.com/",
      "managementEndpointUrl": "https://management.core.windows.net/"
    }
    </pre>

    Ohne das Argument `--sdk-auth` generiert der Befehl eine einfachere Ausgabe:

    <pre>
    {
      "appId": "12345678-1111-2222-3333-1234567890ab",
      "displayName": "localtest-sp-rbac",
      "name": "http://localtest-sp-rbac",
      "password": "abcdef00-4444-5555-6666-1234567890ab",
      "tenant": "00112233-7777-8888-9999-aabbccddeeff"
    }
    </pre>

    In diesem Fall ist `tenant` die Mandanten-ID, `appId` ist die Client-ID, und `password` ist der geheime Clientschlüssel.

    > [!IMPORTANT]
    > Die Ausgabe dieses Befehls ist die einzige Stelle, an der Sie den geheimen Clientschlüssel/das Kennwort sehen können. Sie können das Geheimnis/Kennwort später nicht mehr abrufen. Sie können aber bei Bedarf ein neues Geheimnis hinzufügen, ohne den Dienstprinzipal oder vorhandene Geheimnisse ungültig zu machen.

## <a name="use-python-virtual-environments"></a>Verwenden virtueller Python-Umgebungen

Wir empfehlen, dass Sie für jedes Projekt immer eine *virtuelle Umgebung* mithilfe der folgenden Schritte erstellen und aktivieren:

1. Öffnen Sie ein Terminal oder eine Eingabeaufforderung.

1. Erstellen Sie einen Ordner für Ihr Projekt.

1. Erstellen der virtuellen Umgebung:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```bash
    # py -3 uses the global python interpreter. You can also use python -m venv .venv.
    py -3 -m venv .venv
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    # On Windows, use py -3 -m venv .venv
    python3 -m venv .venv
    ```

    ---

    Dieser Befehl führt das Python-Modul `venv` aus und erstellt eine virtuelle Umgebung in einem Ordner namens `.venv`.

1. Aktivieren der virtuellen Umgebung:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    .venv\scripts\activate
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    source .venv/bin/activate
    ```

    ---

Eine virtuelle Umgebung ist ein Ordner innerhalb eines Projekts, der eine Kopie eines bestimmten Python-Interpreters isoliert. Sobald Sie diese Umgebung aktiviert haben (was in Visual Studio Code automatisch erfolgt), wird durch das Ausführen von `pip install` nur eine Bibliothek in dieser Umgebung installiert. Wenn Sie dann Ihren Python-Code ausführen, wird er im exakten Kontext der Umgebung mit spezifischen Versionen jeder einzelnen Bibliothek ausgeführt. Und wenn Sie dann `pip freeze` ausführen, erhalten Sie die genaue Liste dieser Bibliotheken. (In vielen der Beispiele in dieser Dokumentation erstellen Sie eine Datei *requirements.txt* für die benötigten Bibliotheken und verwenden dann `pip install -r requirements.txt`. Eine Datei mit den Anforderungen ist generell erforderlich, wenn Sie Code in Azure bereitstellen.)

Wenn Sie keine virtuelle Umgebung verwenden, wird Python in seiner *globalen Umgebung* ausgeführt. Zwar ist die Verwendung der globalen Umgebung schnell und komfortabel, doch neigt sie im Laufe der Zeit zur Überfrachtung durch all die Bibliotheken, die Sie für jedes Projekt oder Experiment installieren. Ferner könnten Sie, wenn Sie eine Bibliothek für ein Projekt aktualisieren, andere Projekte beschädigen, die auf anderen Versionen dieser Bibliothek basieren. Und da die Umgebung von einer beliebigen Anzahl von Projekten gemeinsam genutzt wird, können Sie nicht `pip freeze` verwenden, um eine Liste der Abhängigkeiten eines beliebigen Projekts abzurufen.

Die globale Umgebung ist der Ort, an dem Sie Toolpakete installieren sollten, die Sie in mehreren Projekten verwenden möchten. Beispielsweise können Sie `pip install gunicorn` in der globalen Umgebung ausführen, um den gunicorn-Webserver überall verfügbar zu machen.

## <a name="use-source-control"></a>Verwenden der Quellcodeverwaltung

Wir empfehlen, sich anzugewöhnen, bei jedem Start eines Projekts ein Quellcodeverwaltungs-Repository zu erstellen. Wenn Sie Git installiert haben, führen Sie einfach den folgenden Befehl aus:

```cmd
git init
```

Von dort aus können Sie Befehle wie `git add` und `git commit` ausführen, um Änderungen zu übertragen. Durch regelmäßige Commits von Änderungen erstellen Sie einen Commitverlauf, über den Sie jeden vorherigen Zustand wiederherstellen können.

Um eine Onlinesicherung Ihres Projekts zu erstellen, empfehlen wir außerdem, dass Sie Ihr Repository auf [GitHub](https://github.com) oder [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops&preserve-view=true) hochladen. Wenn Sie zuerst ein lokales Repository initialisiert haben, verwenden Sie `git remote add`, um das lokale Repository an GitHub oder Azure DevOps anzufügen.

Dokumentation zu git finden Sie auf [git-scm.com/docs](https://git-scm.com/docs) und im Internet.

Visual Studio Code umfasst eine Reihe integrierter git-Features. Weitere Informationen finden Sie unter [Verwenden der Versionskontrolle in VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

Sie können auch jedes andere Quellcodeverwaltungs-Tool Ihrer Wahl verwenden. Git ist lediglich eins der am häufigsten verwendeten und unterstützten.

## <a name="next-step"></a>Nächster Schritt

Sehen Sie sich nach der Einrichtung Ihrer lokalen Entwicklungsumgebung die gängigen Verwendungsmuster für die Azure-Bibliotheken an:

> [!div class="nextstepaction"]
> [Azure-Bibliotheken für Python: Verwendungsmuster](azure-sdk-library-usage-patterns.md)
