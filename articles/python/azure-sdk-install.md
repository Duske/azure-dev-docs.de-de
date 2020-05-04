---
title: Installieren des Azure SDK für Python-Bibliotheken
description: Es wird beschrieben, wie Sie das Azure SDK für Python-Bibliotheken mit pip installieren, deinstallieren und überprüfen. Der Artikel enthält Details zur Installation bestimmter Versionen und Vorschaupakete.
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 172091bcdff5ba55ccc7c2f13c60f3c7d645efc1
ms.sourcegitcommit: e6cdb0ce11a8272195a0072c7c91cc9b7e89b0b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138746"
---
# <a name="install-azure-sdk-for-python-libraries"></a>Installieren des Azure SDK für Python-Bibliotheken

Das Azure SDK für Python bietet eine API, über die Sie von Python-Code aus mit Azure interagieren können. Die Namen aller aktuellen Bibliotheken finden Sie auf der [Seite mit dem Index zum Azure SDK für Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

Bibliotheken, deren Namen mit `azure-mgmt` beginnen, gehören zum Bereich *Verwaltung*. Sie verwenden sie zum Bereitstellen und Verwalten von Azure-Ressourcen, wie Sie dies auch im [Azure-Portal](https://portal.azure.com) oder mit der [Azure CLI](/cli/azure/install-azure-cli) durchführen. Sie nutzen beispielsweise die Bibliothek `azure-mgmt-storage`, um Azure Storage-Ressourcen bereitzustellen und zu verwalten.

Alle anderen Bibliotheken im SDK sind *Clientbibliotheken*, die Sie im Anwendungscode verwenden, um bereits bereitgestellte Ressourcen zu nutzen. Für die Arbeit mit Azure Storage-Blobs im Anwendungscode verwenden Sie beispielsweise die Bibliothek `azure-storage-blob`.

## <a name="install-the-latest-version-of-a-library"></a>Installieren der aktuellen Version einer Bibliothek

Wenn Sie `pip install` ausführen, wird die neueste Version einer Bibliothek in Ihrer aktuellen Python-Umgebung installiert:

```bash
pip install azure-storage-blob
```

Auf Linux-Systemen wird `sudo pip install` vom SDK nicht unterstützt, um eine Bibliothek für alle Benutzer zu installieren. Jeder Benutzer muss `pip install` separat verwenden.

## <a name="install-specific-library-versions"></a>Installieren bestimmter Bibliotheksversionen

Wenn Sie eine bestimmte Version einer Bibliothek installieren möchten, geben Sie die Version in der Befehlszeile an:

```bash
pip install azure-storage-blob==12.0.0
```

## <a name="install-preview-packages"></a>Installieren von Vorschaupaketen

Microsoft veröffentlicht regelmäßig Vorschauversionen der SDK-Bibliotheken, die über Unterstützung für die neuen Features verfügen. Eine Einschränkung besteht hierbei aber darin, dass Bibliotheken jederzeit geändert werden können und daher nicht für Produktionsprojekte genutzt werden dürfen.

Schließen Sie das Flag `--pre` in die Befehlszeile ein, um die neueste Vorschauversion einer Bibliothek zu installieren.

```bash
pip install --pre azure-storage-blob
```

## <a name="verify-a-library-installation"></a>Überprüfen der Installation einer Bibliothek

Verwenden Sie `pip show <library>`, um zu überprüfen, ob eine Bibliothek installiert ist. Wenn die Bibliothek installiert wurde, werden mit dem Befehl die Version und andere zusammenfassende Informationen angezeigt. Andernfalls wird nach dem Ausführen des Befehls nichts angezeigt.

```bash
pip show azure-storage-blob
```

Sie können auch `pip freeze` oder `pip list` verwenden, um alle Bibliotheken anzuzeigen, die in Ihrer aktuellen Python-Umgebung installiert sind.

## <a name="uninstall-a-library"></a>Deinstallieren einer Bibliothek

Verwenden Sie `pip uninstall <library>`, um eine Bibliothek zu deinstallieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Verwendung des SDK](azure-sdk-get-started.yml)
