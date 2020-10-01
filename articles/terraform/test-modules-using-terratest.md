---
title: 'Tutorial: Testen von Terraform-Modulen in Azure mit Terratest'
description: Es wird beschrieben, wie Sie Terratest zum Testen Ihrer Terraform-Module verwenden.
ms.topic: tutorial
ms.date: 10/26/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: ec42182fd20ce073f727b8096d15e2a87a3c1207
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401700"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Tutorial: Testen von Terraform-Modulen in Azure mit Terratest

> [!NOTE]
> Der Beispielcode in diesem Artikel kann nicht mit Version 0.12 (und höher) verwendet werden.

Sie können Terraform-Module verwenden, um wiederverwendbare, zusammensetzbare und testbare Komponenten zu erstellen. Terraform-Module umfassen eine Kapselung, die beim Implementieren von Prozessen vom Typ „Infrastruktur als Code“ hilfreich sind.

Es ist wichtig, eine Qualitätssicherung zu implementieren, wenn Sie Terraform-Module erstellen. Leider gibt es nicht viel Dokumentation dazu, wie Komponententests und Integrationstests in Terraform-Modulen erstellt werden. Dieses Tutorial enthält eine Einführung in eine Testinfrastruktur und bewährte Methoden, die wir beim Erstellen unserer [Azure Terraform-Module](https://registry.terraform.io/browse?provider=azurerm) genutzt haben.

Wir haben uns alle gängigen Testinfrastrukturen angesehen und uns für das Testen unserer Terraform-Module für [Terratest](https://github.com/gruntwork-io/terratest) entschieden. Terratest wird als Go-Bibliothek implementiert. Terratest umfasst eine Sammlung mit Hilfsfunktionen und Mustern für häufige Aufgaben bei Infrastrukturtests, z.B. Senden von HTTP-Anforderungen und Verwenden von SSH zum Zugreifen auf einen bestimmten virtuellen Computer. In der folgenden Liste sind einige Hauptvorteile der Nutzung von Terratest beschrieben:

- **Komfortable Hilfsfunktionen zum Überprüfen der Infrastruktur**: Dieses Feature ist nützlich, wenn Sie Ihre reale Infrastruktur in der realen Umgebung überprüfen möchten.
- **Eindeutige Ordnerstruktur**: Ihre Testfälle sind klar strukturiert und basieren auf der [Standardordnerstruktur für Terraform-Module](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Testfälle werden in Go geschrieben**: Viele Entwickler, die Terraform nutzen, sind Go-Entwickler. Falls Sie ein Go-Entwickler sind, müssen Sie keine andere Programmiersprache lernen, um Terratest zu verwenden.
- **Erweiterbare Infrastruktur**: Sie können eine Erweiterung auf zusätzliche Funktionen zu Terratest durchführen, z. B. Azure-spezifische Features.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Programmiersprache Go**: Terraform-Testfälle werden in [Go](https://golang.org/dl/) geschrieben.
- **dep**: [dep](https://github.com/golang/dep#installation) ist ein Tool zum Verwalten von Abhängigkeiten für Go.
- **Azure CLI**: Die [Azure CLI](/cli/azure/install-azure-cli) ist ein Befehlszeilentool, das Sie zum Verwalten von Azure-Ressourcen verwenden können. (Terraform unterstützt die Authentifizierung für Azure über einen Dienstprinzipal oder die [Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Wir nutzen die [ausführbare mage-Datei](https://github.com/magefile/mage/releases), um zu veranschaulichen, wie Sie die Ausführung von Terratest-Testfällen vereinfachen können. 

## <a name="create-a-static-webpage-module"></a>Erstellen eines statischen Webseitenmoduls

In diesem Tutorial erstellen Sie ein Terraform-Modul, mit dem eine statische Webseite bereitgestellt wird, indem eine einzelne HTML-Datei in ein Azure Storage-Blob hochgeladen wird. Mit diesem Modul erhalten Benutzer weltweit über eine URL, die vom Modul zurückgegeben wird, Zugriff auf die Webseite.

> [!NOTE]
> Erstellen Sie alle Dateien, die in diesem Abschnitt beschrieben werden, an Ihrem [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH)-Speicherort.

Erstellen Sie zunächst den neuen Ordner `src` unter Ihrem GoPath-Ordner `staticwebpage`. Die gesamte Ordnerstruktur dieses Tutorials wird im folgenden Beispiel veranschaulicht. In diesem Abschnitt liegt der Schwerpunkt auf den Dateien, die mit einem Sternchen `(*)` gekennzeichnet sind.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

Das statische Webseitenmodul akzeptiert drei Eingaben. Die Eingaben werden in `./variables.tf` deklariert:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Wie in diesem Artikel bereits erwähnt, wird in diesem Modul auch eine URL ausgegeben, die in `./outputs.tf` deklariert wird:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

Über die Hauptlogik des Moduls werden vier Ressourcen bereitgestellt:
- **Ressourcengruppe**: Der Name der Ressourcengruppe ist die Eingabe `website_name` mit dem Zusatz `-staging-rg`.
- **Speicherkonto**: Der Name des Speicherkontos ist die Eingabe `website_name` mit dem Zusatz `data001`. Um die Namensbeschränkungen für das Speicherkonto einzuhalten, entfernt das Modul alle Sonderzeichen und verwendet für den gesamten Speicherkontonamen Kleinbuchstaben.
- **Container mit festem Namen**: Der Container hat den Namen `wwwroot` und wird im Speicherkonto erstellt.
- **Einzelne HTML-Datei**: Die HTML-Datei, die aus der Eingabe `html_path` ausgelesen und in `wwwroot/index.html` hochgeladen wird.

Die Modullogik der statischen Webseite wird in `./main.tf` implementiert:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Komponententest

Terratest ist für Integrationstests konzipiert. Zu diesem Zweck werden bei Terratest echte Ressourcen in einer realen Umgebung bereitgestellt. Integrationstestaufträge können unter Umständen sehr groß werden. Dies gilt besonders, wenn Sie über eine hohe Zahl von Ressourcen verfügen, die bereitgestellt werden müssen. Die Logik, mit der Speicherkontonamen – wie im vorherigen Abschnitt erwähnt – konvertiert werden, ist ein gutes Beispiel. 

Wir müssen aber nicht unbedingt Ressourcen bereitstellen. Wir möchten nur sicherstellen, dass die Konvertierungslogik für die Benennung korrekt ist. Dank der Flexibilität von Terratest können wir Komponententests verwenden. Bei Komponententests handelt es sich um lokal ausgeführte Testfälle (obwohl Internetzugriff erforderlich ist). Bei Komponententests werden die Befehle `terraform init` und `terraform plan` ausgeführt, um die Ausgabe von `terraform plan` zu analysieren und nach den zu vergleichenden Attributwerten zu suchen.

Im restlichen Teil dieses Abschnitts wird beschrieben, wie Sie Terratest zum Implementieren eines Komponententests verwenden, um sicherzustellen, dass die Logik zum Konvertieren von Speicherkontonamen korrekt ist. Für uns sind nur die Dateien von Interesse, die mit einem Sternchen `(*)` gekennzeichnet sind.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Verwenden Sie zunächst eine leere HTML-Datei mit dem Namen `./test/fixtures/storage-account-name/empty.html` als Platzhalter.

Die Datei `./test/fixtures/storage-account-name/main.tf` ist der Rahmen für den Testfall. Sie akzeptiert nur die Eingabe von `website_name`, und dies ist auch die Eingabe der Komponententests. Die Logik ist hier dargestellt:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Die Hauptkomponente ist die Implementierung der Komponententests in `./test/storage_account_name_unit_test.go`.

Go-Entwickler werden wahrscheinlich merken, dass der Komponententest mit der Signatur einer klassischen Go-Testfunktion übereinstimmt, indem ein Argument vom Typ `*testing.T` akzeptiert wird.

Im Text des Komponententests sind in der Variablen `testCases` fünf Fälle definiert (`key` als Eingabe und `value` als erwartete Ausgabe). Für jeden Komponententestfall führen wir zuerst `terraform init` mit dem Testfixtureordner (`./test/fixtures/storage-account-name/`) als Ziel aus. 

Anschließend wird mit dem Befehl `terraform plan` mit einer spezifischen Testfalleingabe (siehe `website_name`-Definition in `tfOptions`) das Ergebnis in `./test/fixtures/storage-account-name/terraform.tfplan` (nicht in der Ordnergesamtstruktur aufgeführt) gespeichert.

Diese Ergebnisdatei wird in eine für den Code lesbare Struktur analysiert, indem der offizielle Terraform-Planparser verwendet wird.

Wir suchen jetzt nach den Attributen, die für uns interessant sind (hier der `name` von `azurerm_storage_account`), und vergleichen die Ergebnisse mit der erwarteten Ausgabe:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Führen Sie die folgenden Schritte an der Befehlszeile aus, um die Komponententests auszuführen:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

Das herkömmliche Go-Testergebnis wird nach ca. einer Minute zurückgegeben.

### <a name="integration-test"></a>Integrationstest

Im Gegensatz zu Komponententests müssen bei Integrationstests Ressourcen in einer realen Umgebung bereitgestellt werden, um eine End-to-End-Bereitstellung zu erzielen. Terratest ist für diese Art von Aufgaben gut geeignet. 

Die bewährten Methoden für Terraform-Module umfassen auch die Installation des Ordners `examples`. Der Ordner `examples` enthält einige End-to-End-Beispiele. Warum werden diese Beispiele nicht mithilfe von Integrationstests getestet, um die Verwendung von echten Daten zu vermeiden? In diesem Abschnitt konzentrieren wir uns auf die drei Dateien, die in der folgenden Ordnerstruktur mit einem Sternchen `(*)` gekennzeichnet sind:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Wir beginnen mit den Beispielen. Im Ordner `./examples/` wird ein neuer Beispielordner mit dem Namen `hello-world/` erstellt. Hier stellen wir eine einfache HTML-Seite für den Upload bereit: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Das Terraform-Beispiel `./examples/hello-world/main.tf` ähnelt dem Beispiel für den Komponententest. Es gibt aber einen wesentlichen Unterschied: Im Beispiel wird auch die URL des hochgeladenen HTML-Codes als Webseite mit dem Namen `homepage` ausgegeben.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Wir verwenden Terratest und die klassische Go-Testfunktion wieder in der Integrationstestdatei `./test/hello_world_example_test.go`.

Im Gegensatz zu Komponententests werden bei Integrationstests in Azure echte Ressourcen erstellt. Daher müssen Sie mit Bedacht vorgehen, um Namenskonflikte zu vermeiden. (Achten Sie besonders auf einige global eindeutige Namen, z.B. Speicherkontonamen.) Der erste Schritt der Testlogik ist daher das Generieren eines zufälligen `websiteName`-Elements, indem die von Terratest bereitgestellte Funktion `UniqueId()` verwendet wird. Mit dieser Funktion wird ein zufälliger Name generiert, der Kleinbuchstaben, Großbuchstaben oder Ziffern enthält. Mit `tfOptions` werden alle Terraform-Befehle für den Ordner `./examples/hello-world/` ausgeführt. Außerdem wird sichergestellt, dass `website_name` auf den zufällig ausgewählten `websiteName` festgelegt wird.

Anschließend werden `terraform init`, `terraform apply` und `terraform output` einzeln nacheinander ausgeführt. Wir verwenden eine weitere Hilfsfunktion (`HttpGetWithCustomValidation()`), die von Terratest bereitgestellt wird. Mit der Hilfsfunktion sorgen wir dafür, dass HTML-Code an den Ort der `homepage`-URL-Ausgabe hochgeladen wird, die von `terraform output` zurückgegeben wird. Wir vergleichen den HTTP GET-Statuscode mit `200` und suchen im HTML-Inhalt nach Schlüsselwörtern. Abschließend wird die Ausführung von `terraform destroy` sichergestellt, indem das `defer`-Feature von Go genutzt wird.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Führen Sie die folgenden Schritte an der Befehlszeile aus, um die Integrationstests auszuführen:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

Das herkömmliche Go-Testergebnis wird nach ca. zwei Minuten zurückgegeben. Außerdem können Sie sowohl Komponententests als auch Integrationstests durchführen, indem Sie diese Befehle verwenden:

```shell
go fmt
go test
```

Integrationstests dauern deutlich länger als Komponententests (zwei Minuten für einen Integrationstest, während für fünf Komponententests nur eine Minute benötigt wird). Es ist aber Ihre Entscheidung, ob Sie in einem Szenario Komponenten- oder Integrationstests nutzen möchten. Wir bevorzugen in der Regel die Verwendung von Komponententests für komplexe Logik, indem wir Terraform-HCL-Funktionen nutzen. Integrationstests setzen wir normalerweise für die End-to-End-Perspektive eines Benutzers ein.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Verwenden von mage zum Vereinfachen der Ausführung von Terratest-Testfällen 
Bei der Ausführung von Testfällen in Azure Cloud Shell müssen verschiedene Befehle in unterschiedlichen Verzeichnissen verwendet werden. Zur effizienteren Gestaltung dieses Prozesses wird das Buildsystem in unser Projekt eingeführt. In diesem Abschnitt verwenden wir für diese Aufgabe ein Go-Buildsystem (mage).

Für mage ist lediglich die Datei `magefile.go` im Stammverzeichnis Ihres Projekts erforderlich (im folgenden Beispiel mit `(+)` gekennzeichnet):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Hier ist ein Beispiel für `./magefile.go` angegeben. In diesem Buildskript, das in Go geschrieben ist, implementieren wir fünf Buildschritte:
- `Clean`: Mit diesem Schritt werden alle generierten und temporären Dateien entfernt, die während der Testausführungen generiert wurden.
- `Format`: Bei diesem Schritt werden `terraform fmt` und `go fmt` ausgeführt, um Ihre Codebasis zu formatieren.
- `Unit`: In diesem Schritt werden alle Komponententests (mithilfe der Funktionsnamenskonvention `TestUT_*`) unter dem Ordner `./test/` ausgeführt.
- `Integration`: Dieser Schritt ähnelt `Unit`, aber anstelle von Komponententests werden Integrationstests (`TestIT_*`) ausgeführt.
- `Full`: Bei diesem Schritt werden `Clean`, `Format`, `Unit` und `Integration` nacheinander ausgeführt.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Sie können die folgenden Befehle verwenden, um eine vollständige Testsammlung durchzuführen. Der Code ähnelt den Ausführungsschritten, die wir in einem vorherigen Abschnitt verwendet haben. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Sie können die letzte Befehlszeile durch zusätzliche mage-Schritte ersetzen. Beispielsweise können Sie `mage unit` oder `mage clean` verwenden. Es ist ratsam, `dep`-Befehle und `az login` in die mage-Datei einzubetten. Der entsprechende Code ist hier nicht angegeben. 

Bei mage können Sie die Schritte auch aufteilen, indem Sie das Go-Paketsystem verwenden. In diesem Fall können Sie mage-Dateien übergreifend für alle Module vereinfachen, indem Sie nur auf eine gemeinsame Implementierung verweisen und Abhängigkeiten (`mg.Deps()`) deklarieren.

**Optional: Festlegen von Dienstprinzipal-Umgebungsvariablen zum Durchführen von Akzeptanztests**
 
Anstatt `az login` vor Tests auszuführen, können Sie die Azure-Authentifizierung auch durchführen, indem Sie die Dienstprinzipal-Umgebungsvariablen festlegen. Terraform veröffentlicht [eine Liste mit Namen von Umgebungsvariablen](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Nur die ersten vier dieser Umgebungsvariablen sind erforderlich.) Terraform veröffentlicht auch ausführliche Anleitungen, in denen beschrieben wird, wie Sie [die Werte dieser Umgebungsvariablen beschaffen können](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

[!INCLUDE [terraform-troubleshooting.md](includes/terraform-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Terratest-GitHub-Seite](https://github.com/gruntwork-io/terratest)