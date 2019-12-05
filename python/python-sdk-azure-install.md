---
title: Installieren des Azure SDK für Python
description: Hier erfahren Sie, wie Sie das Azure SDK für Python unter Verwendung von pip oder GitHub installieren. Das Azure SDK kann in Form von einzelnen Bibliotheken oder als vollständiges Paket installiert werden.
ms.date: 10/31/2019
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: 39de0959f3d73306412c39b32a4e13766d1500e9
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466399"
---
# <a name="install-the-azure-sdk-for-python"></a>Installieren des Azure SDK für Python

Das Azure SDK für Python bietet eine API, über die Sie von Python-Code aus mit Azure interagieren können. Abhängig von Ihren Anforderungen können Sie entweder einzelne Bibliotheken aus dem SDK oder aber alle Bibliotheken auf einmal installieren.

Das Azure SDK für Python wurde mit den CPython-Versionen 2.7 und 3.5.3+ sowie mit PyPy 5.4+ getestet und wird von diesen unterstützt. Entwickler verwenden das SDK auch mit anderen Interpretern wie IronPython und Jython, dabei kann es jedoch vereinzelt zu Problemen und Inkompatibilitäten kommen. Wenn Sie einen Python-Interpreter benötigen, installieren Sie die neueste Version, die unter [python.org/downloads](https://www.python.org/downloads) zur Verfügung steht.

## <a name="install-sdk-libraries-using-pip"></a>Installieren von SDK-Bibliotheken mithilfe von pip

Das Azure SDK für Python besteht aus einer Reihe einzelner Bibliotheken, die jeweils bestimmte Azure-Dienste bereitstellen oder mit diesen zusammenarbeiten. Sie können die einzelnen Bibliotheken installieren, indem Sie `pip install <library>` mit den Namen aus der [Liste mit den SDK-Bibliotheken](https://github.com/Azure/azure-sdk-for-python/blob/master/packages.md) verwenden. (Diese Liste enthält Links zu hilfreichen Infodateien für die jeweilige Bibliothek.)

Bei Verwendung von Azure Storage können Sie beispielsweise die Bibliothek `azure-storage-file`, `azure-storage-blob` oder `azure-storage-queue` installieren. Wenn Sie Azure Cosmos DB-Tabellen verwenden, installieren Sie `azure-cosmosdb-table`. Azure Functions wird über die Bibliothek `azure-functions` unterstützt. Und so weiter. Bibliotheken, die mit `azure-mgmt-` beginnen, stellen die API für die Bereitstellung von Azure-Ressourcen bereit.

### <a name="install-specific-library-versions"></a>Installieren bestimmter Bibliotheksversionen

Wenn Sie eine bestimmte Version einer Bibliothek installieren möchten, geben Sie die Version in der Befehlszeile an:

```bash
pip install azure-storage-blob==12.0.0
```

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
