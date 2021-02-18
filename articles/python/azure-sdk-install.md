---
title: Installieren von Azure SDK-Bibliothekspaketen für Python
description: Es wird beschrieben, wie Sie das Azure SDK für Python-Bibliotheken mit pip installieren, deinstallieren und überprüfen. Der Artikel enthält Details zur Installation bestimmter Versionen und Vorschaupakete.
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 7c29e9793fbf995276ea2b860d8c8743bd40d3b3
ms.sourcegitcommit: b380f6e637b47e6e3822b364136853e1d342d5cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100395205"
---
# <a name="how-to-install-azure-library-packages-for-python"></a>Installieren von Azure-Bibliothekspaketen für Python

Das Azure SDK für Python besteht ausschließlich aus vielen einzelnen Bibliotheken. Diese werden im [Paketindex](azure-sdk-library-package-index.md) aufgeführt. Die spezifischen Bibliothekspakete, die Sie für ein Projekt benötigen, werden mithilfe von `pip install` installiert.

Mit diesen Bibliotheken können Sie Ressourcen für Azure-Dienste bereitstellen und verwalten (unter Verwendung der Verwaltungsbibliotheken, deren Name `-mgmt` enthält) und von App-Code aus eine Verbindung mit diesen Ressourcen herstellen (unter Verwendung der Clientbibliotheken).

## <a name="install-the-latest-version-of-a-library"></a>Installieren der aktuellen Version einer Bibliothek

```cmd
pip install azure-storage-blob
```

```cmd
pip install azure-mgmt-storage
```

Von `pip install` wird die neueste Version einer Bibliothek in Ihrer aktuellen Python-Umgebung abgerufen.

Bei Linux-Systemen muss für jeden Benutzer separat eine Bibliothek installiert werden. Das Installieren von Bibliotheken für alle Benutzer mit `sudo pip install` wird nicht unterstützt.

## <a name="install-specific-library-versions"></a>Installieren bestimmter Bibliotheksversionen

```cmd
pip install azure-storage-blob==12.0.0
```

```cmd
pip install azure-mgmt-storage==10.0.0
```

Geben Sie in der Befehlszeile mithilfe von `pip install` die gewünschte Version an.

## <a name="install-preview-packages"></a>Installieren von Vorschaupaketen

```cmd
pip install --pre azure-storage-blob
```

```cmd
pip install --pre azure-mgmt-storage
```

Schließen Sie das Flag `--pre` in die Befehlszeile ein, um die neueste Vorschauversion einer Bibliothek zu installieren.

Microsoft veröffentlicht regelmäßig Vorschauversionen von Bibliothekspaketen mit Unterstützung neuer Features. Diese Bibliotheken können sich jedoch jederzeit ändern und sind daher nicht für Produktionsprojekte geeignet.

## <a name="verify-a-library-installation"></a>Überprüfen der Installation einer Bibliothek

```cmd
pip show azure-storage-blob
```

```cmd
pip show azure-mgmt-storage
```

Verwenden Sie `pip show <library>`, um zu überprüfen, ob eine Bibliothek installiert ist. Wenn die Bibliothek installiert wurde, werden mit dem Befehl die Version und andere zusammenfassende Informationen angezeigt. Andernfalls wird nach dem Ausführen des Befehls nichts angezeigt.

Sie können auch `pip freeze` oder `pip list` verwenden, um alle Bibliotheken anzuzeigen, die in Ihrer aktuellen Python-Umgebung installiert sind.

## <a name="uninstall-a-library"></a>Deinstallieren einer Bibliothek

```cmd
pip uninstall azure-storage-blob
```

Verwenden Sie `pip uninstall <library>`, um eine Bibliothek zu deinstallieren.
