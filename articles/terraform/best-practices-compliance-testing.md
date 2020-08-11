---
title: 'Tutorial: Konformitätstests mit Terraform und Azure'
description: Hier erfahren Sie, wie Sie Konformitätstests nach dem Prinzip der verhaltensgesteuerten Entwicklung (Behavior Driven Development, BDD) auf Terraform-Konfigurationen anwenden.
ms.topic: tutorial
ms.date: 07/31/2020
ms.openlocfilehash: 38358be8bc7626d7b8a6b2df7e5da9b53d98618e
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810613"
---
# <a name="tutorial-compliance-testing-with-terraform-and-azure"></a>Tutorial: Konformitätstests mit Terraform und Azure

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Grundlegendes zur Verwendung von Konformitätstests
> * Durchführen einer Konformitätsprüfung

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Installieren von Terraform**: Führen Sie [den Download und die Installation von Terraform](https://www.terraform.io/downloads.html) entsprechend den Anforderungen Ihrer Umgebung durch.
- **Docker**: [Installieren von Docker](https://docs.docker.com/get-docker/)
- **terraform-compliance**: [Installieren Sie das Tool „terraform-compliance“](https://terraform-compliance.com/pages/installation/docker).
- **Forken Sie die Testbeispiele**: Forken Sie das [Terraform-Beispielprojekt auf GitHub](https://github.com/Azure/terraform), und klonen Sie es auf Ihrem Dev/Test-Computer.

## <a name="what-is-compliance-testing"></a>Was sind Konformitätstests?

Konformitätstests sind eine nicht funktionale Testmethode, mit der Sie feststellen können, ob ein System vorgeschriebenen Standards entspricht. Konformitätstests werden auch als *Übereinstimmungstests* bezeichnet.

Die meisten Softwareteams führen eine Analyse durch, um zu überprüfen, ob die Standards korrekt erzwungen und implementiert werden. Häufig versuchen sie dabei gleichzeitig, die Standards zu verbessern, wodurch wiederum die Qualität erhöht wird.

Konformitätstests stellen sicher, dass das Ergebnis jeder Phase des Entwicklungslebenszyklus den vereinbarten Anforderungen entspricht.

Konformitätsprüfungen sollten zu Beginn der Projekte in den Entwicklungszyklus integriert werden. Das Hinzufügen von Konformitätsprüfungen zu einem späteren Zeitpunkt wird zunehmend schwieriger, wenn die Anforderung selbst nicht ausreichend dokumentiert ist.

## <a name="understanding-compliance-checks"></a>Grundlegendes zu Konformitätsprüfungen

Das Durchführen von Konformitätsprüfungen ist unkompliziert. Für jede Phase des Entwicklungslebenszyklus wird eine Reihe von Standards und Prozeduren entwickelt und dokumentiert. Das Ergebnis der einzelnen Phasen wird mit den dokumentierten Anforderungen verglichen. Die Ergebnisse des Tests sind alle „Lücken“, die nicht den vordefinierten Standards entsprechen. Konformitätstests werden anhand des Überprüfungsprozesses durchgeführt, und das Ergebnis des Review-Prozesses sollte dokumentiert werden.

Sehen wir uns ein spezifisches Beispiel an.

Ein häufiges Problem sind Umgebungen, in denen es zu Unterbrechungen kommt, wenn mehrere Entwickler inkompatible Änderungen anwenden. Angenommen, eine Person arbeitet an einer Änderung und wendet Ressourcen an, z. B. durch Erstellen einer VM in einer Testumgebung. Eine weitere Person wendet dann eine andere Version des Codes an, durch die eine andere Version dieser VM bereitgestellt wird. In Fällen wie diesem ist eine Überwachung erforderlich, um die Konformität mit definierten Regeln sicherzustellen.

Eine Möglichkeit zur Lösung dieses Problems besteht darin, eine Richtlinie für das Taggen der Ressourcen zu definieren, z. B. mit den Tags `role` und `creator`. Nachdem Sie die Richtlinien definiert haben, stellen Sie mit einem Tool wie [terraform-compliance](https://terraform-compliance.com) sicher, dass sie befolgt werden.

Bei „terraform-compliance“ liegt der Schwerpunkt auf *negativen Tests*. Mit negativen Tests wird sichergestellt, dass ein System eine unerwartete Eingabe oder ein unerwünschtes Verhalten ordnungsgemäß behandeln kann. *Fuzzing* ist ein Beispiel für negative Tests. Mittels Fuzzing wird ein System getestet, das Eingaben empfängt, um sicherzustellen, dass es unerwartete Eingaben sicher verarbeiten kann.

Glücklicherweise ist Terraform eine Abstraktionsebene für beliebige APIs, die Cloudinfrastrukturentitäten erstellen, aktualisieren oder zerstören. Terraform stellt außerdem sicher, dass die lokale Konfiguration und die Antworten der Remote-API synchronisiert sind. Da Terraform hauptsächlich für Cloud-APIs verwendet wird, benötigen wir dennoch eine Möglichkeit, um sicherzustellen, dass der für die Infrastruktur bereitgestellte Code bestimmten Richtlinien entspricht. Das kostenloses Open-Source-Tool „terraform-compliance“ bietet diese Funktionalität für Terraform-Konfigurationen.

Beim VM-Beispiel könnte eine Konformitätsrichtlinie wie folgt lauten: *„Wenn Sie eine Azure-Ressource erstellen, muss sie ein Tag enthalten.“*

Das Tool „terraform-compliance“ stellt ein Testframework bereit, in dem Sie Richtlinien wie das obige Beispiel erstellen. Anschließend führen Sie die Richtlinien für den Terraform-Ausführungsplan aus.

Mit „terraform-compliance“ können Sie Prinzipien der *verhaltensgesteuerten Entwicklung* (Behavior-Driven Development, BDD) anwenden. BDD ist ein kollaborativer Prozess, bei dem alle Beteiligten zusammenarbeiten, um das gewünschte Verhalten eines Systems zu definieren. Zu den Beteiligten zählen in der Regel die Entwickler und Tester sowie alle Benutzer, die ein besonderes Interesse am entwickelten System haben oder von diesem betroffen sind. BDD soll Teams dazu ermutigen, konkrete Beispiele zu entwickeln, die ein gemeinsames Verständnis des gewünschten Verhaltens des Systems darstellen.

## <a name="looking-at-an-example"></a>Beispiel

Weiter oben in diesem Artikel wurde als Beispiel für einen Konformitätstest die Erstellung einer VM für eine Testumgebung genannt. In diesem Abschnitt erfahren Sie, wie sich dieses Beispiel in ein BDD-Feature und -Szenario übersetzen lässt. Die Regel wird zunächst mithilfe von *Cucumber* formuliert, einem Tool, das zur Unterstützung von BDD verwendet wird.

```Cucumber
when creating Azure resources, every new resource should have a tag
```

Die vorherige Regel wird wie folgt übersetzt:

```Cucumber
If the resource supports tags
Then it must contain a tag
And its value must not be null
```

Der Terraform-HCL-Code würde die Regel dann wie folgt einhalten.

```hcl
resource "random_uuid" "uuid" {}

resource "azurerm_resource_group" "rg" {
  name     = "rg-hello-tf-${random_uuid.uuid.result}"
  location = var.location

  tags = {
    environment = "dev"
    application = "Azure Compliance"
  } 
}
```

Die erste Richtlinie könnte wie folgt als [BDD-Featureszenario](https://cucumber.io/docs/gherkin/reference/) geschrieben werden:

```Cucumber
Feature: Test tagging compliance  # /target/src/features/tagging.feature
    Scenario: Ensure all resources have tags
        If the resource supports tags
        Then it must contain a tag
        And its value must not be null
```

Der folgende Code zeigt einen Test für ein bestimmtes Tag:

```Cucumber
Scenario Outline: Ensure that specific tags are defined
    If the resource supports tags
    Then it must contain a tag <tags>
    And its value must match the "<value>" regex

    Examples:
      | tags        | value              |
      | Creator     | .+                 |
      | Application | .+                 |
      | Role        | .+                 |
      | Environment | ^(prod\|uat\|dev)$ |
```

## <a name="running-the-sample"></a>Ausführen des Beispiels

In diesem Abschnitt laden Sie das Beispiel herunter und testen es.

1. [Laden Sie das Beispiel für den Konformitätstest herunter](https://github.com/Azure/terraform/tree/master/samples/compliance-testing).

1. Wechseln Sie in das Verzeichnis `src`.

1. Führen Sie [terraform init](https://www.terraform.io/docs/commands/init.html) aus, um das Arbeitsverzeichnis zu initialisieren. Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```bash
    terraform init
    ```
    
1. Führen Sie [terraform validate](https://www.terraform.io/docs/commands/validate.html) aus, um die Syntax der Konfigurationsdateien zu überprüfen.

    ```bash
    terraform validate
    ```
    
1. Führen Sie [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus, um einen Ausführungsplan zu erstellen.

    ```bash
    terraform plan -out tf.out
    ```
    
1. Führen Sie zum Anwenden des Ausführungsplans den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus.

    ```bash
    terraform apply -target=random_uuid.uuid
    ```
    
1. Führen Sie [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) aus, um das terraform-compliance-Image herunterzuladen.

    ```bash
    docker pull eerkunt/terraform-compliance
    ```
    
1. Führen Sie [docker run](https://docs.docker.com/engine/reference/commandline/run/) aus, um die Tests in einem Docker-Container auszuführen. **Der Test schlägt fehl**. Die erste Regel, die das Vorhandensein von Tags erfordert, wird erfolgreich ausgeführt. Bei der zweiten Regel tritt jedoch ein Fehler auf, da die Tags `Role` und `Creator` fehlen.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```
    
    ![Beispiel für einen fehlgeschlagenen Test](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-fail.png)

1. Ändern Sie `main.tf` wie folgt, um den Fehler zu beheben.

    ```terraform
      tags = {
        Environment = "dev"
        Application = "Azure Compliance"
        Creator     = "Azure Compliance"
        Role        = "Azure Compliance"
      } 
    
    ```
    
1. Führen Sie `terraform validate` erneut aus, um die Syntax zu überprüfen.

    ```bash
    terraform validate
    ```
    
1. Führen Sie `terraform plan` erneut aus, um einen neuen Ausführungsplan zu erstellen.

    ```bash
    terraform plan -out tf.out
    ```
    
1. Führen Sie [docker run](https://docs.docker.com/engine/reference/commandline/run/) erneut aus, um die Konfiguration zu testen. Dieses Mal ist der Test erfolgreich, da die vollständige Spezifikation implementiert wurde.

    ```bash
    docker run --rm -v $PWD:/target -it eerkunt/terraform-compliance -f features -p tf.out
    ```

    ![Beispiel für einen erfolgreichen Test](media/best-practices-compliance-testing/best-practices-compliance-testing-tagging-succeed.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Ausführen von End-to-End-Tests in Terraform-Projekten](best-practices-end-to-end-testing.md)
