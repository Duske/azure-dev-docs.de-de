---
title: Installieren des Azure SDK für Python
description: Installieren des Azure Python SDK
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 06/05/2017
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: a0e979ec58cb659873a1bbe85bda4579363a9777
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172341"
---
# <a name="install-the-azure-sdk-for-python"></a>Installieren des Azure SDK für Python

## <a name="which-python-and-which-version-to-use"></a>Welche Python und welche Version sollte verwendet werden?

Verschiedene Python-Interpreter sind verfügbar. Einige Beispiele:

* CPython – der standardmäßige und am häufigsten verwendete Python-Interpreter
* PyPy – schnelle, kompatible alternative Implementierung zu CPython
* IronPython – Python-Interpreter, der unter .Net/CLR ausgeführt wird
* Jython – Python-Interpreter, der in JVM (Java Virtual Machine) ausgeführt wird

**CPython**, v2.7 oder v3.4 und höher und PyPy 5.4.0 wurden getestet und unterstützen das Python Azure SDK.

## <a name="where-to-get-python"></a>Wo erhalte ich Python?

Es gibt mehrere Möglichkeiten, CPython zu beziehen:

* Direkt über [Python](https://www.python.org/)
* Von angesehenen Distributoren wie [Anaconda](https://www.anaconda.com/), [Enthought](https://www.enthought.com/) oder [ActiveState](https://www.activestate.com/)
* Aus der Source erzeugen!

Sofern Sie keine spezifischen Anforderungen haben, empfehlen wir Ihnen die ersten beiden Optionen.

## <a name="installation-with-pip"></a>Installation mit pip

Sie können die einzelnen Azure-Dienstbibliotheken separat installieren:

```bash
pip install azure-batch          # Install the latest Batch runtime library
pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Vorschaupakete können mit dem `--pre` -Flag installiert werden:

```bash
pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Sie können auch eine Gruppe von Azure-Bibliotheken mithilfe des Metapakets `azure` in einer einzelnen Zeile installieren.

```bash
pip install azure
```

Von diesem Paket wird eine Vorschauversion veröffentlicht, auf die Sie über das Flag „--pre“ zugreifen können:

```bash
pip install --pre azure
```

## <a name="install-from-github"></a>Installieren über GitHub

Vorgehensweise bei der Installation von `azure` über die Quelle:

```bash
git clone git://github.com/Azure/azure-sdk-for-python.git
cd azure-sdk-for-python
python setup.py install
```

## <a name="install-an-older-version-with-pip"></a>Installieren einer älteren Version mit pip
Sie können eine ältere Version von `azure` installieren, indem Sie die Versionsdetails „azure==3.0.0“ angeben.
```bash
pip install azure==3.0.0 
```
## <a name="check-sdk-installation-details-with-pip"></a>Überprüfen der SDK-Installationsdetails mit pip
Sie können u. a. den Installationspfad und die Versionsdetails des `azure` SDK überprüfen.
```bash
pip show azure # Show installed version, location details etc.
pip freeze     # Output installed packages in requirements format.
pip list       # List installed packages, including editables.
```
## <a name="to-uninstall-with-pip"></a>So führen Sie eine Deinstallation mit pip aus
Sie können alle Azure-Bibliotheken mithilfe des Metapakets `azure` in einer einzelnen Zeile deinstallieren.
```bash
pip uninstall azure 
```
> [!NOTE]
> `pip uninstall azure` entfernt das Metapaket `azure`, hinterlässt jedoch Pakete vom Typ `azure-*` (sowie weitere Pakete wie `adal` und `msrest` ). Eine Besonderheit von Python und pip besteht darin, dass bei Paketen mit Abhängigkeiten die Deinstallation des ursprünglichen Pakets nicht zur Deinstallation der Abhängigkeiten führt. Wenn Sie `azure-` und die unterstützenden Pakete entfernen möchten, führen Sie den Befehl `pip freeze | grep 'azure-' | xargs pip uninstall -y` aus. (Deinstallieren Sie anschließend adal, msrest und msrestazure.)

