---
title: 'Tutorial: Übersicht über Terraform-Tests'
description: Hier erfahren Sie mehr über die verschiedenen Testoptionen, die Sie zum Überprüfen von Terraform-Projekten konfigurieren können.
ms.topic: overview
ms.date: 07/31/2020
ms.custom: devx-track-terraform
adobe-target: true
ms.openlocfilehash: ede7fdfc1dafedacfdc74f657fee0a4ae11feb85
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395325"
---
# <a name="tutorial-terraform-testing-overview"></a>Tutorial: Übersicht über Terraform-Tests

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Terraform ist ein Infrastructure-as-Code-Tool (IAC). Diese Toolkategorie bezieht sich auf die Tatsache, dass Sie Ihre Terraform-Dateien wie den Quellcode des Projekts behandeln. Teil dieses Prozesses sind die Versionskontrolle und Quellcodeverwaltung. Tests sollten ebenfalls ein Bestandteil Ihres Prozesses sein. Dieser Artikel bietet eine Übersicht über die verschiedenen Arten von Tests, die für ein Terraform-Projekt ausgeführt werden können.

## <a name="integration-testing"></a>Integrationstest

Integrationstests stellen sicher, dass eine neu eingeführte Codeänderung vorhandenen Code nicht unterbricht. In DevOps bezieht sich Continuous Integration (CI) auf einen Prozess, durch den das gesamte System bei jeder Änderung der Codebasis erstellt wird, z. B. wenn eine Person einen Pull Request (PR) in einem Git-Repository zusammenführen möchte. Die folgende Liste enthält allgemeine Beispiele für Integrationstests:

- Tools für die statische Codeanalyse, z. B. „lint“ und „format“
- Ausführen von [terraform validate](https://www.terraform.io/docs/commands/validate.html), um die Syntax der Konfigurationsdateien zu überprüfen
- Ausführen von [terraform plan](https://www.terraform.io/docs/commands/validate.html), um sicherzustellen, dass die Konfiguration wie erwartet funktioniert

> [!div class="nextstepaction"]
> [Weitere Informationen zu Integrationstests](best-practices-integration-testing.md)

## <a name="unit-testing"></a>Komponententest

Komponententests stellen sicher, dass sich ein bestimmter Teil oder eine bestimmte Funktion eines Programms korrekt verhält. Komponententests werden vom Entwickler der Funktionalität geschrieben. Diese Art von Tests, die auch als testgesteuerte Entwicklung (Test-Driven Development, TDD) bezeichnet wird, erfordert kontinuierliche kurze Entwicklungszyklen. Bei Terraform-Projekten können Komponententests in Form von `terraform plan` ausgeführt werden, um sicherzustellen, dass die tatsächlichen Werte, die im generierten Plan verfügbar sind, den erwarteten Werten entsprechen. 

Komponententests können besonders dann nützlich sein, wenn Ihre Terraform-Module an Komplexität zunehmen:

- Generieren von dynamischen Blöcken
- Verwenden von Schleifen
- Berechnen lokaler Variablen

Ähnlich wie Integrationstests werden Komponententests oft in den Continuous Integration-Prozess eingebunden.

## <a name="compliance-testing"></a>Kompatibilitätstest

Mit Konformitätstests wird sichergestellt, dass die Konfiguration den für das Projekt definierten Richtlinien entspricht. Möglicherweise definieren Sie geopolitische Namenskonventionen für Ihre Azure-Ressourcen, oder Sie möchten, dass VMs aus einer definierten Teilmenge von Images erstellt werden. Konformitätstests werden verwendet, um diese Regeln zu erzwingen.

Konformitätstests werden in der Regel ebenfalls als Teil des Continuous Integration-Prozesses definiert.

> [!div class="nextstepaction"]
> [Weitere Informationen zu Konformitätstests](best-practices-compliance-testing.md)

## <a name="end-to-end-e2e-testing"></a>End-to-End-Tests (E2E)

Mit E2E-Tests wird überprüft, ob ein Programm funktioniert, bevor es in der Produktionsumgebung bereitgestellt wird. Ein Beispielszenario ist ein Terraform-Modul, das zwei VMs in einem virtuellen Netzwerk bereitstellt. Wenn Sie in diesem Beispiel verhindern möchten, dass die beiden VMs einander pingen, können Sie einen Test definieren, um das gewünschte Ergebnis vor der Bereitstellung zu überprüfen.

Ein E2E-Test besteht in der Regel aus drei Schritten. Zuerst wird die Konfiguration auf eine Testumgebung angewendet. Danach wird der Code ausgeführt, um die Ergebnisse zu überprüfen. Zum Schluss wird die Testumgebung entweder erneut initialisiert oder entfernt (z. B. durch Aufheben der Zuordnung einer VM).

> [!div class="nextstepaction"]
> [Weitere Informationen zu End-to-End-Tests](best-practices-end-to-end-testing.md)

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]
