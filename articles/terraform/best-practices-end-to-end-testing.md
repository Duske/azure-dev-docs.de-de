---
title: 'Tutorial: Einrichten von End-to-End-Terratest-Tests für Terraform-Projekte'
description: Hier erfahren Sie, wie Sie mit Terratest End-to-End-Tests für Terraform-Projekte ausführen.
ms.topic: tutorial
ms.date: 07/31/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: b760908bf1950751b93ba1787f444ca37ee8bf83
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401710"
---
# <a name="tutorial-setup-end-to-end-terratest-testing-on-terraform-projects"></a>Tutorial: Einrichten von End-to-End-Terratest-Tests für Terraform-Projekte

[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
> * Grundlegendes zu End-to-End-Tests mit [Terratest](https://github.com/gruntwork-io/terratest)
> * Schreiben eines End-to-End-Tests mit Golang
> * Verwenden von Azure DevOps zum automatischen Auslösen von End-to-End-Tests, wenn Code in Ihr Repository committet wird

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Installieren von Terraform**: Führen Sie [den Download und die Installation von Terraform](https://www.terraform.io/downloads.html) entsprechend den Anforderungen Ihrer Umgebung durch.
- **Forken der Testbeispiele:** Damit Sie schnell loslegen können, empfehlen wir, [dieses Repository](https://github.com/Azure/terraform) in Ihrer eigenen GitHub-Organisation zu forken.
- **Go-Programmiersprache**: Terraform-Testfälle sind in [Go](https://golang.org/dl/) geschrieben. Im Beispiel in diesem Artikel werden [Go-Module](https://blog.golang.org/using-go-modules) verwendet. Für diesen Artikel wird Go 1.13 (oder höher) empfohlen.

## <a name="what-is-end-to-end-testing"></a>Was sind End-to-End-Tests?

Mit End-to-End-Tests wird überprüft, ob ein System als Ganzes funktioniert. Im Gegensatz zu diesem Testtyp steht das Testen einzelner Module. Bei Terraform-Projekten ermöglichen End-to-End-Tests die Validierung des bereitgestellten Codes. Diese Tests unterscheiden sich von vielen anderen Testtypen, mit denen Szenarien vor der Bereitstellung getestet werden. End-to-End-Tests sind wichtig für das Testen komplexer Systeme, die mehrere Module enthalten und Aktionen für mehrere Ressourcen ausführen. In solchen Szenarien sind End-to-End-Tests die einzige Möglichkeit, um festzustellen, ob die verschiedenen Module korrekt miteinander interagieren.

Den Schwerpunkt dieses Artikels bildet die Verwendung von [Terratest](https://github.com/gruntwork-io/terratest) zum Implementieren von End-to-End-Tests. Terratest bietet alle Basisfunktionen, die Sie zum Ausführen der folgenden Aufgaben benötigen:

- Bereitstellen einer Terraform-Konfiguration
- Schreiben eines Tests mit der Sprache Go, um den bereitgestellten Code zu überprüfen
- Orchestrieren der Tests in Phasen
- Entfernen der bereitgestellten Infrastruktur

## <a name="tutorial-scenario"></a>Tutorialszenario

In diesem Tutorial verwenden wir ein Beispiel, das im [Azure/Terraform-Beispielrepository](https://github.com/Azure/terraform/blob/master/samples/end-to-end-testing/README.md) verfügbar ist.

Dieses Beispiel definiert eine Terraform-Konfiguration, die zwei Linux-VMs im gleichen virtuellen Netzwerk bereitstellt. Die VM `vm-linux-1` verfügt über eine öffentliche IP-Adresse. Nur Port 22 wird geöffnet, um SSH-Verbindungen zuzulassen. Die zweite VM `vm-linux-2` hat keine definierte öffentliche IP-Adresse.

Mit unserem Test sollen die folgenden Szenarien überprüft werden:

- Wurde die Infrastruktur korrekt bereitgestellt?
- Ist es möglich, über Port 22 eine SSH-Sitzung mit `vm-linux-1` zu öffnen?
- Ist es möglich, `vm-linux-2` unter Verwendung der SSH-Sitzung auf `vm-linux-1` zu pingen?

![Beispiel für ein End-to-End-Testszenario](media/best-practices-end-to-end-testing/scenario.png)

Wenn Sie das [Beispiel heruntergeladen](#prerequisites) haben, finden Sie die Terraform-Konfiguration für dieses Szenario in der Datei `src/main.tf`. Diese Datei enthält alles, was Sie zur Bereitstellung der oben dargestellten Azure-Infrastruktur benötigen.

Falls Sie nicht mit dem Erstellen von VMs vertraut sind, lesen Sie [Erstellen einer Linux-VM mit Infrastruktur in Azure mit Terraform](create-linux-virtual-machine-with-infrastructure.md).

> [!CAUTION]
> Das in diesem Artikel verwendete Beispielszenario dient nur zur Veranschaulichung. Das Szenario wurde bewusst einfach gehalten, damit wir uns auf die Schritte eines End-to-End-Tests konzentrieren können. Wir raten davon ab, Produktions-VMs zu verwenden, die SSH-Ports über eine öffentliche IP-Adresse verfügbar machen.

## <a name="end-to-end-test"></a>End-to-End-Test

Der End-to-End-Test ist in der Sprache Go geschrieben und verwendet das Terratest-Framework. Wenn Sie das [Beispiel heruntergeladen](#prerequisites) haben, ist er in der Datei `src/test/end2end_test.go` definiert.

Der folgende Quellcode zeigt die Standardstruktur eines Golang-Tests mit Terratest:

```Go
package test

import (
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    test_structure "github.com/gruntwork-io/terratest/modules/test-structure"
)

func TestEndToEndDeploymentScenario(t *testing.T) {
    t.Parallel()

    fixtureFolder := "../"

    // User Terratest to deploy the infrastructure
    test_structure.RunTestStage(t, "setup", func() {
        terraformOptions := &terraform.Options{
            // Indicate the directory that contains the Terraform configuration to deploy
            TerraformDir: fixtureFolder,
        }

        // Save options for later test stages
        test_structure.SaveTerraformOptions(t, fixtureFolder, terraformOptions)

        // Triggers the terraform init and terraform apply command
        terraform.InitAndApply(t, terraformOptions)
    })

    test_structure.RunTestStage(t, "validate", func() {
        // run validation checks here
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
            publicIpAddress := terraform.Output(t, terraformOptions, "public_ip_address")
    })

    // When the test is completed, teardown the infrastructure by calling terraform destroy
    test_structure.RunTestStage(t, "teardown", func() {
        terraformOptions := test_structure.LoadTerraformOptions(t, fixtureFolder)
        terraform.Destroy(t, terraformOptions)
    })
}
```

Wie Sie im obigen Codeausschnitt sehen können, besteht der Test aus drei Phasen:

- **setup**: Führt Terraform aus, um die Konfiguration bereitzustellen.
- **validate**: Führt die Validierungsüberprüfungen und Assertionen aus.
- **teardown**: Bereinigt die Infrastruktur, nachdem der Test ausgeführt wurde.

In der folgenden Liste sind einige der wichtigsten Funktionen des Terratest-Frameworks aufgeführt:

- **terraform.InitAndApply**: Ermöglicht das Ausführen von `terraform init` und `terraform apply` über Go-Code.
- **terraform.Output**: Ruft den Wert der Ausgabevariable der Bereitstellung ab.
- **terraform.Destroy**: Führt den Befehl `terraform destroy` über den Go-Code aus.
- **test_structure.LoadTerraformOptions**: Lädt Terraform-Optionen (z. B. die Konfiguration und Variablen) aus dem Zustand.
- **test_structure.SaveTerraformOptions**: Speichert Terraform-Optionen (z. B. die Konfiguration und Variablen) im Zustand.

## <a name="run-the-end-to-end-test"></a>Durchführen des End-to-End-Tests

In diesem Abschnitt führen Sie den Test für die Beispielkonfiguration und -bereitstellung aus. 

1. Öffnen Sie ein Bash-/Terminalfenster.

1. Melden Sie sich beim Azure-Konto an. Informationen zum Anmelden bei Ihrem Azure-Abonnement finden Sie im Abschnitt [Authentifizierung bei Azure](get-started-cloud-shell.md#authenticate-to-azure).

1. Zum Ausführen dieses Beispieltests benötigen Sie den Namen eines privaten/öffentlichen Schlüsselpaars (`id_rsa` und `id_rsa.pub`) in Ihrem Basisverzeichnis. Ersetzen Sie `USER` durch den Namen Ihres Basisverzeichnisses.

```bash
export TEST_SSH_KEY_PATH="/home/USER/.ssh/id_rsa"
```

1. Wechseln Sie in das Verzeichnis `test`.

1. Führen Sie den Test aus.

```go
go test -v ./ -timeout 10m
```

Die Ergebnisse des Tests sehen in etwa wie in der folgenden Ausgabe aus:

```output
--- PASS: TestEndToEndDeploymentScenario (390.99s)
PASS
ok      test    391.052s
```

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übersicht über Terraform-Tests](best-practices-testing-overview.md)