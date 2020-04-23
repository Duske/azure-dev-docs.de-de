---
title: Azure CLI-Erweiterungen
description: Verwenden von Erweiterungen mit der Azure CLI
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 73488fc464ed052f22f071f6373fb6b8f682b778
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030982"
---
# <a name="use-extensions-with-azure-cli"></a>Verwenden von Erweiterungen mit der Azure CLI 

Die Azure CLI umfasst eine Funktion zum Laden von Erweiterungen. Erweiterungen sind Python-Wheels, die zusammen mit der CLI bereitgestellt, aber als CLI-Befehle ausgeführt werden.
Mit Erweiterungen erhalten Sie Zugriff auf experimentelle Befehle und Vorabversionen von Befehlen und können eigene CLIs schreiben. Dieser Artikel behandelt die Verwaltung von Erweiterungen und enthält Antworten auf allgemeine Fragen zu ihrer Verwendung.

## <a name="find-extensions"></a>Suchen von Erweiterungen

Verwenden Sie den Befehl [az extension list-available](/cli/azure/extension#az-extension-list-available), um die von Microsoft bereitgestellten und verwalteten Erweiterungen anzuzeigen.

```azurecli-interactive
az extension list-available --output table
```

Auf der Dokumentationswebsite wird darüber hinaus eine [Liste der Erweiterungen](azure-cli-extensions-list.md) bereitgestellt.

## <a name="install-extensions"></a>Installieren von Erweiterungen

Nachdem Sie eine zu installierende Erweiterung gefunden haben, können Sie [az extension add](https://docs.microsoft.com/cli/azure/extension#az-extension-add) verwenden, um sie abzurufen. Wenn die Erweiterung in `az extension list-available` aufgeführt wird, können Sie die Erweiterung anhand des Namens installieren.

```azurecli-interactive
az extension add --name <extension-name>
```

Wenn die Erweiterung von einer externen Ressource stammt oder Sie über einen direkten Link dafür verfügen, geben Sie die Quell-URL oder den lokalen Pfad an. Die Erweiterung _muss_ eine kompilierte Python-Wheeldatei sein.

```azurecli-interactive
az extension add --source <URL-or-path>
```

Nach der Installation einer Erweiterung befindet sich diese unter dem Wert der Shellvariablen `$AZURE_EXTENSION_DIR`. Wenn diese Variable nicht festgelegt ist, befindet sich der Wert standardmäßig unter `$HOME/.azure/cliextensions` (Linux und macOS) bzw. `%USERPROFILE%\.azure\cliextensions` (Windows).

## <a name="update-extensions"></a>Aktualisieren von Erweiterungen

Wenn eine Erweiterung anhand des Namens installiert wurde, aktualisieren Sie sie mithilfe von [az extension update](https://docs.microsoft.com/cli/azure/extension#az-extension-update).

```azurecli-interactive
az extension update --name <extension-name>
```

Andernfalls kann eine Erweiterung anhand der Quelle gemäß den Anweisungen unter [Installieren von Erweiterungen](#install-extensions) aktualisiert werden.

Falls der Name einer Erweiterung von der CLI nicht aufgelöst werden kann, deinstallieren Sie die Erweiterung, und installieren Sie sie anschließend erneut. Die Erweiterung kann auch Teil der Basis-CLI geworden sein.
Versuchen Sie, die CLI wie unter [Installieren der Azure CLI](install-azure-cli.md) beschrieben zu installieren, und überprüfen Sie, ob die Befehle der Erweiterung hinzugefügt wurden.

## <a name="uninstall-extensions"></a>Deinstallieren von Erweiterungen

Wenn Sie eine Erweiterung nicht mehr benötigen, entfernen Sie sie mit [az extension remove](https://docs.microsoft.com/cli/azure/extension#az-extension-remove).

```azurecli-interactive
az extension remove --name <extension-name>
```

Sie können eine Erweiterung auch manuell entfernen, indem Sie sie am Installationsspeicherort löschen. Die Shellvariable `$AZURE_EXTENSION_DIR` legt fest, welche Module installiert werden.
Wenn diese Variable nicht festgelegt ist, befindet sich der Wert standardmäßig unter `$HOME/.azure/cliextensions` (Linux und macOS) bzw. `%USERPROFILE%\.azure\cliextensions` (Windows).

```bash
rm -rf $AZURE_EXTENSION_DIR/<extension-name>
```

## <a name="faq"></a>Häufig gestellte Fragen

Hier sind einige Antworten auf andere häufig gestellte Fragen zu CLI-Erweiterungen aufgeführt.

### <a name="what-file-formats-are-allowed-for-installation"></a>Welche Dateiformate sind für die Installation zulässig?

Derzeit können nur kompilierte Python-Wheels als Erweiterungen installiert werden.

### <a name="can-extensions-replace-existing-commands"></a>Können Erweiterungen vorhandene Befehle ersetzen?

Ja. Erweiterungen können vorhandene Befehle ersetzen, aber vor dem Ausführen eines ersetzten Befehls wird von der CLI eine Warnung ausgegeben.

### <a name="how-can-i-tell-if-an-extension-is-in-pre-release"></a>Woran erkenne ich, dass es sich bei einer Erweiterung um eine Vorabversion handelt?

Anhand der Dokumentation und der Versionsverwaltung können Sie erkennen, ob es sich bei einer Erweiterung um eine Vorabversion handelt. Microsoft veröffentlicht häufig Vorschaubefehle als CLI-Erweiterungen, damit sie später ggf. in das CLI-Hauptprodukt aufgenommen werden können. Wenn Befehle aus Erweiterungen verschoben werden, muss die alte Erweiterung deinstalliert werden. 

### <a name="can-extensions-depend-upon-each-other"></a>Können Erweiterungen voneinander abhängig sein?

Nein. Da die CLI keine Ladereihenfolge garantiert, werden Abhängigkeiten unter Umständen nicht ordnungsgemäß berücksichtigt. Das Entfernen einer Erweiterung wirkt sich nicht auf andere Erweiterungen aus.

### <a name="are-extensions-updated-along-with-the-cli"></a>Werden Erweiterungen zusammen mit der CLI aktualisiert?

Nein. Erweiterungen müssen separat aktualisiert werden, wie unter [Aktualisieren von Erweiterungen](#update-extensions) beschrieben.

### <a name="how-to-develop-our-own-extension"></a>Wie entwickeln Sie Ihre eigene Erweiterung?
Sehen Sie sich das offizielle Repository an, wenn Sie weitere Unterstützung benötigen. [Azure/azure-cli-extensions](https://github.com/Azure/azure-cli/tree/master/doc/extensions)
