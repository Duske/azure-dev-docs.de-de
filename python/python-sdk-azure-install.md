---
title: Installieren des Azure SDK für Python
description: Hier erfahren Sie, wie Sie das Azure SDK für Python unter Verwendung von pip oder GitHub installieren. Das Azure SDK kann in Form von einzelnen Bibliotheken oder als vollständiges Paket installiert werden.
ms.date: 10/31/2019
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: de36effc1787c83d137f84f8e0d670c97c71cdcf
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422505"
---
# <a name="install-the-azure-sdk-for-python"></a>Installieren des Azure SDK für Python

Das Azure SDK für Python bietet eine API, über die Sie von Python-Code aus mit Azure interagieren können. Abhängig von Ihren Anforderungen können Sie entweder einzelne Bibliotheken aus dem SDK oder aber alle Bibliotheken auf einmal installieren.

Das Azure SDK für Python wurde mit den CPython-Versionen 2.7 und 3.5.3+ sowie mit PyPy 5.4+ getestet und wird von diesen unterstützt. Entwickler verwenden das SDK auch mit anderen Interpretern wie IronPython und Jython, dabei kann es jedoch vereinzelt zu Problemen und Inkompatibilitäten kommen. Wenn Sie einen Python-Interpreter benötigen, installieren Sie die neueste Version, die unter [python.org/downloads](https://www.python.org/downloads) zur Verfügung steht.

## <a name="install-sdk-libraries-using-pip"></a>Installieren von SDK-Bibliotheken mithilfe von pip

Das Azure SDK für Python besteht aus einer Reihe einzelner Bibliotheken, die jeweils bestimmte Azure-Dienste bereitstellen oder mit diesen zusammenarbeiten. Sie können sie jeweils mit `pip install <library>` installieren. Auf der [Seite für SDK-Veröffentlichungen](https://azure.github.io/azure-sdk/releases/latest/python.html) finden Sie spezifische Anweisungen und Dokumentationen zu jeder Bibliothek.

Bei Verwendung von Azure Storage können Sie beispielsweise die Bibliothek `azure-storage-file`, `azure-storage-blob` oder `azure-storage-queue` installieren. Wenn Sie Azure Cosmos DB-Tabellen verwenden, installieren Sie `azure-cosmosdb-table`. Azure Functions wird über die Bibliothek `azure-functions` unterstützt. Und so weiter. Bibliotheken, die mit `azure-mgmt-` beginnen, stellen die API für die Bereitstellung von Azure-Ressourcen bereit.

### <a name="install-specific-library-versions"></a>Installieren bestimmter Bibliotheksversionen

Wenn Sie eine bestimmte Version einer Bibliothek installieren möchten, geben Sie die Version in der Befehlszeile an:

```bash
pip install azure-storage-blob==12.0.0
```

> [!NOTE]
> Auf Linux-Systemen wird `sudo pip install` vom SDK nicht unterstützt, um eine Bibliothek für alle Benutzer zu installieren. Jeder Benutzer muss `pip install` separat verwenden. 

### <a name="install-preview-packages"></a>Installieren von Vorschaupaketen

Microsoft veröffentlicht regelmäßig SDK-Vorschaubibliotheken, die zukünftige Features unterstützen. Schließen Sie das Flag `--pre` in die Befehlszeile ein, um die neueste Vorschauversion einer Bibliothek zu installieren. 

```bash
# Install all preview versions of the Azure SDK for Python
pip install --pre azure

# Install the preview version for azure-storage-blob only.
pip install --pre azure-storage-blob
```

## <a name="verify-sdk-installation-details-with-pip"></a>Überprüfen der SDK-Installationsdetails mit pip

Verwenden Sie den Befehl `pip show <library>`, um sich zu vergewissern, dass eine Bibliothek installiert wurde. Ist die Bibliothek installiert, zeigt der Befehl die Version und andere Zusammenfassungsinformationen an. Ist die Bibliothek nicht installiert, zeigt der Befehl nichts an.

```bash
# Check installation of the Azure SDK for Python
pip show azure

# Check installation of a specific library
pip show azure-storage-blob
```

Sie können auch `pip freeze` oder `pip list` verwenden, um alle Bibliotheken anzuzeigen, die in Ihrer aktuellen Python-Umgebung installiert sind.

## <a name="uninstall-azure-sdk-for-python-libraries"></a>Deinstallieren von Bibliotheken des Azure SDK für Python

Verwenden Sie `pip uninstall <library>`, um eine einzelne Bibliothek zu deinstallieren.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zur Verwendung des SDK](python-sdk-azure-get-started.yml)
