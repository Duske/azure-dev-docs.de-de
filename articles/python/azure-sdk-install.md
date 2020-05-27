---
title: Installieren des Azure SDK für Python-Bibliotheken
description: Es wird beschrieben, wie Sie das Azure SDK für Python-Bibliotheken mit pip installieren, deinstallieren und überprüfen. Der Artikel enthält Details zur Installation bestimmter Versionen und Vorschaupakete.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 302d17211480f9c8793d7be1de20a6ab5dec3c95
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2020
ms.locfileid: "83403673"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Installieren des Azure SDK für Python-Bibliotheken

Das Azure SDK für Python bietet eine API, über die Sie von Python-Code aus mit Azure interagieren können. Die Namen aller aktuellen Bibliotheken finden Sie auf der [Seite mit dem Index zum Azure SDK für Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

Bibliotheken, deren Namen mit `azure-mgmt` beginnen, gehören zum Bereich *Verwaltung*. Sie verwenden sie zum Bereitstellen und Verwalten von Azure-Ressourcen, wie Sie dies auch im [Azure-Portal](https://portal.azure.com) oder mit der [Azure CLI](/cli/azure/install-azure-cli) durchführen. Sie nutzen beispielsweise die Bibliothek `azure-mgmt-storage`, um Azure Storage-Ressourcen bereitzustellen und zu verwalten.

Alle anderen Bibliotheken im SDK sind *Clientbibliotheken*, die Sie im Anwendungscode verwenden, um bereits bereitgestellte Ressourcen zu nutzen. Für die Arbeit mit Azure Storage-Blobs im Anwendungscode verwenden Sie beispielsweise die Bibliothek `azure-storage-blob`.

## <a name="install-the-latest-version-of-a-library"></a>Installieren der aktuellen Version einer Bibliothek

```bash
pip install azure-storage-blob
```

`pip install` installiert die neueste Version einer Bibliothek in Ihrer aktuellen Python-Umgebung.

Auf Linux-Systemen wird `sudo pip install` vom SDK nicht unterstützt, um eine Bibliothek für alle Benutzer zu installieren. Jeder Benutzer muss `pip install` separat verwenden.

## <a name="install-specific-library-versions"></a>Installieren bestimmter Bibliotheksversionen

```bash
pip install azure-storage-blob==12.0.0
```

Geben Sie die Version in der Befehlszeile mit `pip install` an.

## <a name="install-preview-packages"></a>Installieren von Vorschaupaketen

```bash
pip install --pre azure-storage-blob
```

Schließen Sie das Flag `--pre` in die Befehlszeile ein, um die neueste Vorschauversion einer Bibliothek zu installieren.

Microsoft veröffentlicht regelmäßig Vorschauversionen der SDK-Bibliotheken, die über Unterstützung für die neuen Features verfügen. Eine Einschränkung besteht hierbei aber darin, dass Bibliotheken jederzeit geändert werden können und daher nicht für Produktionsprojekte genutzt werden dürfen.

## <a name="verify-a-library-installation"></a>Überprüfen der Installation einer Bibliothek

```bash
pip show azure-storage-blob
```

Verwenden Sie `pip show <library>`, um zu überprüfen, ob eine Bibliothek installiert ist. Wenn die Bibliothek installiert wurde, werden mit dem Befehl die Version und andere zusammenfassende Informationen angezeigt. Andernfalls wird nach dem Ausführen des Befehls nichts angezeigt.

Sie können auch `pip freeze` oder `pip list` verwenden, um alle Bibliotheken anzuzeigen, die in Ihrer aktuellen Python-Umgebung installiert sind.

## <a name="uninstall-a-library"></a>Deinstallieren einer Bibliothek

```bash
pip uninstall azure-storage-blob
```

Verwenden Sie `pip uninstall <library>`, um eine Bibliothek zu deinstallieren.

## <a name="next-steps"></a>Nächste Schritte

Sie sind jetzt uneingeschränkt bereit, Code zu schreiben und auszuführen. Dazu können Sie eines der folgenden Beispiele verwenden:

> [!div class="nextstepaction"]
> [Beispiel: Erstellen einer Ressourcengruppe >>>](azure-sdk-example-resource-group.md)
