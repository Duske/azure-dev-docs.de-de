---
title: Verwenden der Variablenersetzung mit GitHub Actions für Azure
description: GitHub-Aktion zum Ersetzen von Variablen in parametrisierten Dateien
author: juliakm
ms.author: jukullam
ms.topic: conceptual
ms.service: azure
ms.date: 01/25/2021
ms.custom: github-actions-azure
ms.openlocfilehash: e2a82fbcbe48269339dc672d46aca4cc3601ae12
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759500"
---
# <a name="use-variable-substitution-with-github-actions"></a>Verwenden der Variablenersetzung mit GitHub-Aktionen

Hier erfahren Sie, wie Sie die [Variablenersetzungsaktion](https://github.com/marketplace/actions/variable-substitution) verwenden, um Werte in XML-, JSON- und YAML-basierten Konfigurations- und Parameterdateien zu ersetzen.

Mithilfe der Variablenersetzung können während der Workflowausführung Werte wie etwa [GitHub-Geheimnisse](https://docs.github.com/en/actions/reference/encrypted-secrets) in Dateien in Ihrem Repository eingefügt werden. So können Sie beispielsweise während der Workflowausführung eine API-Anmeldung und ein Kennwort in eine JSON-Datei einfügen.

Die Variablenersetzung funktioniert nur bei Schlüsseln, die in der Objekthierarchie vordefiniert wurden. Mit der Variablenersetzung können keine neuen Schlüssel erstellt werden. Darüber hinaus können nur Variablen, die im Workflow als [Umgebungsvariablen](https://docs.github.com/en/actions/reference/environment-variables) definiert sind, oder bereits verfügbare Systemvariablen für die Ersetzung verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  

## <a name="use-the-variable-substitution-action"></a>Verwenden der Variablenersetzungsaktion

In diesem Beispiel erfahren Sie Schritt für Schritt, wie Sie Werte in `employee.json` mithilfe der [Variablenersetzungsaktion](https://github.com/marketplace/actions/variable-substitution) ersetzen.

1. Erstellen Sie `employee.json` auf der Stammebene Ihres Repositorys.

    ```json
    {
        "first-name": "Toni",
        "last-name": "Cranz",
        "username": "",
        "password": "",
        "url": ""
    }
    ```

2. Öffnen Sie Ihr GitHub-Repository, und navigieren Sie zu **Einstellungen**.

    :::image type="content" source="media/github-repo-settings.png" alt-text="Auswählen von „Einstellungen“ im Navigationsbereich":::

3. Wählen Sie **Geheimnisse** und dann **Neues Geheimnis** aus.

    :::image type="content" source="media/select-secrets.png" alt-text="Auswählen eines Geheimnisses zum Hinzufügen":::

4. Fügen Sie ein neues Geheimnis (`PASSWORD`) mit dem Wert `5v{W<$2B<GR2=t4#` (oder einem von Ihnen gewählten Kennwort) hinzu. Speichern Sie Ihr Geheimnis. 

5. Navigieren Sie zu **Aktionen**, und wählen Sie **Eigenen Workflow einrichten** aus.

6. Fügen Sie eine Workflowdatei hinzu. Der Benutzernamenwert in Ihrer JSON-Datei wird durch `tcranz` ersetzt. Das Kennwort wird durch Ihr GitHub-Geheimnis ersetzt. Das URL-Feld wird mit einer URL aufgefüllt, die die GitHub-Variable `github.repository` enthält.

    ```yaml
    on: [push]
    name: variable substitution in json

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - uses: microsoft/variable-substitution@v1 
        with:
            files: 'employee.json'
        env:
            username: tcranz
            password: ${{ secrets.PASSWORD }}
            url: https://github.com/${{github.repository}}

    ```

7. Navigieren Sie zu **Aktionen**, um Ihre Workflowausführung anzuzeigen. Öffnen Sie die Variablenersetzungsaktion. Die einzelnen Variablen sollten ersetzt worden sein.

    ```text
    SubstitutingValueonKeyWithString username tcranz
    SubstitutingValueonKeyWithString password ***
    SubstitutingValueonKeyWithString url https://github.com/account/variable-sub
    Successfully updated file: employee.json
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie Ihr GitHub-Repository, wenn Sie es nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen in App Service mithilfe von GitHub Actions](/azure/app-service/deploy-github-actions)
