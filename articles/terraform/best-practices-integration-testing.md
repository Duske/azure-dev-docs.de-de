---
title: 'Tutorial: Integrationstests mit Terraform und Azure'
description: Hier erhalten Sie Informationen zu Integrationstests und erfahren, wie Sie Azure DevOps zum Konfigurieren von Continuous Integration für Terraform-Projekte verwenden.
ms.topic: tutorial
ms.date: 10/08/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 7b10693907feedf94db60315d8819d45fdd47412
ms.sourcegitcommit: b19420d1a8d526a81e0835012cccc46717fadd69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524895"
---
# <a name="tutorial-configure-integration-tests-for-terraform-projects-in-azure"></a>Tutorial: Konfigurieren von Integrationstests für Terraform-Projekte in Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Grundlegendes zu Integrationstests für Terraform-Projekte
> * Konfigurieren einer Continuous Integration-Pipeline mit Azure DevOps
> * Ausführen einer statischen Codeanalyse für Terraform-Code
> * Ausführen von `terraform validate`, um Terraform-Konfigurationsdateien auf dem lokalen Computer zu überprüfen
> * Ausführen von `terraform plan`, um Terraform-Konfigurationsdateien aus der Perspektive von Remotediensten zu überprüfen
> * Automatisieren von Continuous Integration mit einer Azure-Pipeline

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Azure DevOps-Organisation und -Projekt**: [Erstellen Sie eine Azure DevOps-Organisation](/azure/devops/organizations/projects/create-project), falls Sie noch keine haben.
- **Terraform Build & Release Tasks-Erweiterung**: [Installieren Sie die Terraform Build & Release Tasks-Erweiterung](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform) in Ihrer Azure DevOps-Organisation.
- **Gewähren des Zugriffs auf Ihr Azure-Abonnement für Azure DevOps**: Erstellen Sie eine [Azure-Dienstverbindung](/azure/devops/pipelines/library/connect-to-azure) mit dem Namen `terraform-basic-testing-azure-connection`, damit Azure-Pipelines eine Verbindung mit Ihren Azure-Abonnements herstellen können.
- **Installieren von Terraform**: Führen Sie [den Download und die Installation von Terraform](https://www.terraform.io/downloads.html) entsprechend den Anforderungen Ihrer Umgebung durch.
- **Forken Sie die Testbeispiele**: Forken Sie das [Terraform-Beispielprojekt auf GitHub](https://github.com/Azure/terraform), und klonen Sie es auf Ihrem Dev/Test-Computer.

## <a name="validate-a-local-terraform-configuration"></a>Überprüfen einer lokalen Terraform-Konfiguration

Der Befehl [terraform validate](https://www.terraform.io/docs/commands/validate.html) wird über die Befehlszeile im Verzeichnis mit Ihren Terraform-Dateien ausgeführt. Diese Befehle dienen in erster Linie zur Überprüfung der Syntax.

1. Öffnen Sie Ihre bevorzugte Befehlszeilenumgebung. Viele Code-Editoren (z. B. Visual Studio Code) stellen eine Befehlszeilenschnittstelle bereit.

1. Wechseln Sie zum Verzeichnis `samples/integration-testing/src` des lokalen Repositorys. Es enthält ein einfaches Terraform-Projekt.

1. Initialisieren Sie die Terraform-Bereitstellung mithilfe des Befehls [terraform init](https://www.terraform.io/docs/commands/init.html). Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```bash
    terraform init
    ```

1. Überprüfen Sie die Terraform-Testdatei mit [terraform validate](https://www.terraform.io/docs/commands/validate.html).

    ```bash
    terraform validate
    ```

    Es sollte eine Meldung angezeigt werden, dass die Konfiguration gültig ist.

1. Öffnen Sie in einem Code-Editor die Datei `main.tf`.

1. Fügen Sie in Zeile 5 einen Tippfehler ein, durch den die Syntax ungültig wird. Ersetzen Sie beispielsweise `var.location` durch `var.loaction`.

1. Speichern Sie die Datei .

1. Führen Sie die Prüfung erneut aus.

    ```bash
    terraform validate
    ```

    Dieses Mal sollte eine Fehlermeldung mit der fehlerhaften Zeile und einer Beschreibung des Fehlers angezeigt werden.

Wie Sie sehen, hat Terraform ein Problem in der Syntax des Konfigurationscodes erkannt. Dieses Problem verhindert die Bereitstellung der Konfiguration.

Es empfiehlt sich, immer `terraform validate` für Ihre Terraform-Dateien auszuführen, bevor Sie sie in das Versionskontrollsystem pushen. Diese Prüfung sollte auch Teil Ihrer Continuous Integration-Pipeline sein. Im weiteren Verlauf dieses Tutorials erfahren Sie, wie Sie eine [Azure-Pipeline für die automatische Überprüfung konfigurieren](#automate-integration-tests-using-azure-pipeline).

## <a name="validate-terraform-configuration-can-be-deployed-on-azure"></a>Überprüfen, ob die Terraform-Konfiguration in Azure bereitgestellt werden kann

Im vorherigen Abschnitt haben Sie erfahren, wie Sie eine Terraform-Konfiguration überprüfen. Bei dieser Prüfung handelte es sich um einen syntaxbezogenen Test. Bereits in Azure bereitgestellter Code wurde dabei nicht berücksichtigt.

Terraform ist eine *deklarative Sprache*. Dies bedeutet, dass Sie das gewünschte Endergebnis deklarieren. Angenommen, Sie haben zehn VMs in einer Ressourcengruppe. Anschließend erstellen Sie eine Terraform-Datei, die drei VMs definiert. Wenn Sie diesen Plan anwenden, erhöht sich die Gesamtanzahl nicht auf 13. Stattdessen löscht Terraform sieben der VMs, sodass Sie letztlich drei VMs haben. Durch die Ausführung von `terraform plan` können Sie die potenziellen Ergebnisse der Anwendung eines Ausführungsplans überprüfen, um Überraschungen zu vermeiden.

Um den Terraform-Ausführungsplan zu generieren, führen Sie [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus. Dieser Befehl stellt eine Verbindung mit dem Azure-Zielabonnement her, um zu überprüfen, welcher Teil der Konfiguration bereits bereitgestellt wurde. Terraform ermittelt dann die Änderungen, die zum Erfüllen der genannten Anforderungen in der Terraform-Datei erforderlich sind. In dieser Phase stellt Terraform nichts bereit. Das Tool zeigt an, was passiert, wenn Sie den Plan anwenden.

Wenn Sie mit dem Tutorial fortfahren und die Schritte im vorherigen Abschnitt durchgeführt haben, führen Sie nun den Befehl `terraform plan` aus:

```bash
terraform plan
```

Nach dem Ausführen von `terraform plan` zeigt Terraform das potenzielle Ergebnis der Anwendung des Ausführungsplans an. Der Ausgabe können Sie entnehmen, welche Azure-Ressourcen hinzugefügt, geändert und zerstört werden.

Standardmäßig speichert Terraform den Zustand im selben lokalen Verzeichnis wie die Terraform-Datei. Dieses Muster funktioniert in Einzelbenutzerszenarien gut. Wenn jedoch mehrere Personen an denselben Azure-Ressourcen arbeiten, kann es vorkommen, dass lokale Zustandsdateien nicht mehr synchron sind. Um dieses Problem zu beheben, unterstützt Terraform das Schreiben von Zustandsdateien in einen Remotedatenspeicher (z. B. Azure Storage). In diesem Szenario kann es problematisch sein, `terraform plan` auf einem lokalen Computer mit einem Remotecomputer als Ziel auszuführen. Daher ist es u. U. sinnvoll, diesen [Prüfungsschritt als Teil der Continuous Integration-Pipeline zu automatisieren](#automate-integration-tests-using-azure-pipeline).

## <a name="run-static-code-analysis"></a>Ausführen einer statischen Codeanalyse

Sie können die statische Codeanalyse direkt im Terraform-Konfigurationscode durchführen, ohne den Code auszuführen. Diese Analyse eignet sich, um Probleme wie Sicherheitsprobleme und Inkonsistenzen in Bezug auf die Konformität zu erkennen.

Die folgenden Tools stellen eine statische Analyse für Terraform-Dateien bereit:

- [Checkov](https://github.com/bridgecrewio/checkov/)
- [Terrascan](https://github.com/accurics/terrascan)
- [tfsec](https://github.com/tfsec/tfsec)
- [Deepsource](https://deepsource.io/blog/release-terraform-static-analysis/) 

Die statische Analyse wird häufig im Rahmen einer Continuous Integration-Pipeline ausgeführt. Diese Tests erfordern weder die Erstellung eines Ausführungsplans noch eine Bereitstellung. Sie sind daher schneller als andere Tests und werden in der Regel als Erstes im Continuous Integration-Prozess ausgeführt.

## <a name="automate-integration-tests-using-azure-pipeline"></a>Automatisieren von Integrationstests mit einer Azure-Pipeline

Bei Continuous Integration wird im Fall einer Änderung das gesamte System getestet. In diesem Abschnitt wird eine Azure-Pipelinekonfiguration zum Implementieren von Continuous Integration vorgestellt.

1. Navigieren Sie in Ihrem bevorzugten Editor zum lokalen Klon des [Terraform-Beispielprojekts auf GitHub](https://github.com/Azure/terraform).

1. Öffnen Sie die Datei `samples/integration-testing/src/azure-pipeline.yaml`.

1. Scrollen Sie nach unten zum Abschnitt **steps**. Dort sehen Sie die Standardschritte zum Ausführen verschiedener Installations- und Prüfungsroutinen.

1. Sehen Sie sich die Zeile **Step 1: run the Checkov Static Code Analysis** an. In diesem Schritt führt das zuvor erwähnte Projekt `Checkov` eine statische Codeanalyse für die Terraform-Beispielkonfiguration aus. 

    ```yaml
    - bash: $(terraformWorkingDirectory)/checkov.sh $(terraformWorkingDirectory)
      displayName: Checkov Static Code Analysis
    ```
    
    Hinweis:
    
    - Dieses Skript ist dafür verantwortlich, Checkov in dem in einem Docker-Container eingebundenen Terraform-Arbeitsbereich auszuführen. Von Microsoft verwaltete Agents sind Docker-fähig. Das Ausführen von Tools in einem Docker-Container ist einfacher, und zudem entfällt die Notwendigkeit, Checkov auf dem Azure-Pipeline-Agent zu installieren.
    - Die Variable `$(terraformWorkingDirectory)` wird in der Datei `azure-pipeline.yaml` definiert.

1. Sehen Sie sich die Zeile **Step 2: install Terraform on the Azure Pipelines agent** an. Die [Terraform Build & Release Tasks-Erweiterung](https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform), die Sie zuvor installiert haben, umfasst einen Befehl zum Installieren von Terraform auf dem Agent, der die Azure-Pipeline ausführt. Diese Aufgabe wird in diesem Schritt ausgeführt.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-installer.TerraformInstaller@0
      displayName: 'Install Terraform'
      inputs:
        terraformVersion: $(terraformVersion)
    ```
    
    Hinweis:

    - Die zu installierende Terraform-Version wird über eine Azure-Pipelinevariable namens `terraformVersion` angegeben und in der Datei `azure-pipeline.yaml` definiert.

1. Sehen Sie sich die Zeile **Step 3: run Terraform init to initialize the workspace** an. Nachdem Terraform auf dem Agent installiert wurde, kann das Terraform-Verzeichnis initialisiert werden.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform init'
      inputs:
        command: init
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
    Hinweise:

    - Die Eingabe `command` gibt an, welcher Terraform-Befehl ausgeführt werden soll.
    - Die Eingabe `workingDirectory` gibt den Pfad des Terraform-Verzeichnisses an.
    - Die Variable `$(terraformWorkingDirectory)` wird in der Datei `azure-pipeline.yaml` definiert.

1. Sehen Sie sich die Zeile **Step 4: run Terraform validate to validate HCL syntax** an. Nachdem das Projektverzeichnis initialisiert wurde, wird `terraform validate` ausgeführt, um die Konfiguration auf dem Server zu überprüfen.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform validate'
      inputs:
        command: validate
        workingDirectory: $(terraformWorkingDirectory)
    ```
    
1. Sehen Sie sich die Zeile **Step 5: run Terraform plan to validate HCL syntax** an. Wie zuvor erläutert, wird der Ausführungsplan generiert, um vor der Bereitstellung zu überprüfen, ob die Terraform-Konfiguration gültig ist.

    ```yaml
    - task: charleszipp.azure-pipelines-tasks-terraform.azure-pipelines-tasks-terraform-cli.TerraformCLI@0
      displayName: 'Run terraform plan'
      inputs:
        command: plan
        workingDirectory: $(terraformWorkingDirectory)
        environmentServiceName: $(serviceConnection)
        commandOptions: -var location=$(azureLocation)
    ```
    
    Hinweise:

    - Die Eingabe `environmentServiceName` bezieht sich auf den Namen der Azure-Dienstverbindung, die Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben. Die Verbindung ermöglicht Terraform den Zugriff auf Ihr Azure-Abonnement.
    - Die Eingabe `commandOptions` wird verwendet, um Argumente an den Terraform-Befehl zu übergeben. In diesem Fall wird ein Speicherort angegeben. Die Variable `$(azureLocation)` wird zuvor in der YAML-Datei definiert.

### <a name="import-the-pipeline-into-azure-devops"></a>Importieren der Pipeline in Azure DevOps

1. Öffnen Sie Ihr Azure DevOps-Projekt, und wechseln Sie zum Abschnitt „Azure Pipelines“.
 
1. Wählen Sie die Schaltfläche **Pipeline erstellen** aus.

1. Wählen Sie unter **Wo befindet sich Ihr Code?** die Option **GitHub (YAML)** aus.

    ![Wo befindet sich Ihr Code?](media/best-practices-integration-testing/new-pipeline-where-github-yaml.png)

1. An diesem Punkt müssen Sie Azure DevOps möglicherweise für den Zugriff auf Ihre Organisation autorisieren. Weitere Informationen zu diesem Thema finden Sie im Artikel zum [Erstellen von GitHub-Repositorys](/azure/devops/pipelines/repos/github).

1. Wählen Sie in der Liste der Repositorys den Fork des Repositorys aus, den Sie in Ihrer GitHub-Organisation erstellt haben.

1. Wählen Sie im Schritt **Pipeline konfigurieren** die Option zum Verwenden einer vorhandenen Azure Pipelines-YAML-Datei aus.

    ![Vorhandene YAML-Pipeline](media/best-practices-integration-testing/new-pipeline-existing-yaml.png)

1. Wenn die Seite **Vorhandene YAML-Datei auswählen** angezeigt wird, geben Sie den Branch `master` an, und geben Sie den Pfad zur YAML-Pipeline ein: `samples/integration-testing/src/azure-pipeline.yaml`.

    ![Auswählen einer vorhandenen YAML-Pipeline](media/best-practices-integration-testing/select-existing-yaml-pipeline.png)

1. Wählen Sie **Weiter** aus, um die Azure-YAML-Pipeline aus GitHub zu laden.

1. Wenn die Seite **Pipeline-YAML überprüfen** angezeigt wird, wählen Sie **Ausführen** aus, um die Pipeline zu erstellen und erstmals manuell auszulösen.

    ![Ausführen der Azure-Pipeline](media/best-practices-integration-testing/run-pipeline.png)

### <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.

Sie können die Pipeline manuell über die Azure DevOps-Benutzeroberfläche ausführen. In diesem Artikel geht es jedoch um die Automatisierung von Continuous Integration. Testen Sie den Prozess, indem Sie eine Änderung am Ordner `samples/integration-testing/src` Ihres geforkten Repositorys vornehmen. Die Änderung löst automatisch eine neue Pipeline in dem Branch aus, in den Sie den Code pushen.

![Über GitHub ausgeführte Pipeline](media/best-practices-integration-testing/pipeline-running-from-github.png)

Nachdem Sie diesen Schritt ausgeführt haben, zeigen Sie die Details in Azure DevOps an, um sicherzustellen, dass alles korrekt ausgeführt wurde.

![Azure DevOps-Pipeline mit grünem Status](media/best-practices-integration-testing/azure-devops-green-pipeline.png)

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Ausführen von Konformitätstests in Terraform-Projekten](best-practices-compliance-testing.md)
