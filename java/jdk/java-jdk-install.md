---
title: Installieren des Azul Zulu JDK für Azure und Azure Stack
description: Hier erfahren Sie, wie Sie die Azul Zulu Java Development Kits (JDKs) für die Azure-Entwicklung mit Windows, Linux und Mac installieren.
ms.date: 04/19/2019
ms.topic: conceptual
ms.openlocfilehash: dad5665285b15650212ab2410c3965c9f38b727d
ms.sourcegitcommit: 6ffa53b933da524e09911b164bba8515722bfa91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77629384"
---
# <a name="install-the-jdk-for-azure-and-azure-stack"></a>Installieren des JDK für Azure und Azure Stack

Bei Enterprise Edition-JDK-Builds von Azul Zulu für Azure handelt es sich um eine kostenlose, plattformübergreifende und produktionsbereite Distribution von OpenJDK für Azure und Azure Stack, die von Microsoft und Azul Systems unterstützt wird. Sie enthält alle Komponenten, die zum Erstellen und Ausführen von Java SE-Anwendungen benötigt werden.

Es stehen [mehrere unterstützte Downloadpakettypen für die jeweiligen Clientbetriebssysteme](https://www.azul.com/downloads/azure-only/zulu/) zur Verfügung. Für die folgenden Plattformen können Sie auch ein VM-Image aus dem Azure Marketplace-Katalog beziehen:

* [Azul Zulu: Java 8 unter Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)
* [Azul Zulu: Java 8 unter Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-windows-2019)
* [Azul Zulu: Java 11 unter Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-ubuntu-1804)
* [Azul Zulu: Java 11 unter Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu11-windows-2019)

> [!NOTE]
> Diese Anweisungen beziehen sich auf die Java 8-Version (64 Bit) des JDK. Die Java Runtime Environment (JRE) wird von Azul auch als eigenständige Installation angeboten. Die JRE ist in der JDK-Installation enthalten.
>
> Auf der [Azure-Downloadseite von Azul](https://www.azul.com/downloads/azure-only/zulu/) stehen auch Java 11-Pakete zur Verfügung.

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-windows"></a>Herunterladen und Installieren von Enterprise Edition-JDK-Builds von Azul Zulu für Azure für Windows

1. Laden Sie die 64-Bit-Version von Azul Zulu JDK 8 als MSI-Datei herunter:

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-win_x64.msi](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-win_x64.msi)
   * Überprüfen Sie alternativ, ob eine [höhere Zulu 8-Version](http://repos.azul.com/azure-only/zulu/packages/zulu-8) der *MSI*-Datei verfügbar ist.

   Speichern Sie sie an einem Speicherort auf Ihrem Client, etwa unter `C:\Users\<your_login>\Downloads`. (Auf der [Azure-Downloadseite von Azul](https://www.azul.com/downloads/azure-only/zulu/) stehen auch ZIP-Pakete zur Verfügung.)

2. Navigieren Sie zu dem Verzeichnis, und doppelklicken Sie auf die heruntergeladene MSI-Datei, um die Installation zu starten.

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-mac"></a>Herunterladen und Installieren von Enterprise Edition-JDK-Builds von Azul Zulu für Azure für Mac

Mit den folgenden Schritten laden Sie eine ZIP-Datei auf Ihren Mac herunter. Es ist auch eine DMG-Version verfügbar.

1. Laden Sie die 64-Bit-Version von Azul Zulu JDK 8 als ZIP-Datei herunter:

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-macosx_x64.zip](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-macosx_x64.zip)
   * Überprüfen Sie alternativ, ob eine [höhere Zulu 8-Version](http://repos.azul.com/azure-only/zulu/packages/zulu-8) der *ZIP*-Datei verfügbar ist.

   Speichern Sie sie an einem Speicherort auf Ihrem Client, etwa unter `/Library/Java/JavaVirtualMachines/`. (Auf der [Azure-Downloadseite von Azul](https://www.azul.com/downloads/azure-only/zulu/) stehen auch DMG-Pakete zur Verfügung.)

2. Starten Sie den Finder, navigieren Sie zum Downloadverzeichnis, und doppelklicken Sie auf die ZIP-Datei. Alternativ können Sie auch ein Terminalbefehlsfenster starten, zu dem Verzeichnis navigieren, und Folgendes ausführen:

    ```cli
    unzip <name_of_zulu_package>.zip
    ```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdk-builds-for-alpine-linux"></a>Herunterladen und Installieren von Enterprise Edition-JDK-Builds von Azul Zulu für Azure für Alpine Linux

1. Laden Sie die 64-Bit-Version von Azul Zulu JDK 8 als TAR-Datei herunter:

   * [zulu-8-azure-jdk_8.44.0.11-8.0.242-linux_x64.tar.gz](http://repos.azul.com/azure-only/zulu/packages/zulu-8/8u242/zulu-8-azure-jdk_8.44.0.11-8.0.242-linux_x64.tar.gz)
   * Überprüfen Sie alternativ, ob eine [höhere Zulu 8-Version](https://repos.azul.com/azure-only/zulu/packages/zulu-8) der Datei vom Typ *.tar.gz* verfügbar ist.

   Speichern Sie sie an einem Speicherort auf Ihrem Client, etwa unter `/usr/lib/jvm`. (Auf der [Azure-Downloadseite von Azul](https://www.azul.com/downloads/azure-only/zulu/) stehen auch RPM- und DEB-Pakete zur Verfügung.)

2. Navigieren Sie zu Ihrem Verzeichnis, und führen Sie den folgenden Befehl aus, um die Datei zu entzippen und zu erweitern:

    ```cli
    tar -xvf <name_of_zulu_package>.tar
    ```

## <a name="confirm-your-installation"></a>Überprüfen der Installation

Führen Sie zur Überprüfung der Installation an der Befehlszeile Folgendes aus: `java -version`.

Die Ausgabe des Befehls sollte ungefähr so wie das folgende Beispiel aussehen:

```cli
$ java -version

openjdk version "1.8.0_242"
OpenJDK Runtime Environment (Zulu 8.44.0.11-linux64)-Microsoft-Azure-restricted (build 1.8.0_242-b20)
OpenJDK 64-Bit Server VM (Zulu 8.44.0.11-linux64)-Microsoft-Azure-restricted (build 25.242-b20, mixed mode)
```

## <a name="download-and-install-the-azul-zulu-for-azure---enterprise-edition-jdks-from-a-yum-repository"></a>Herunterladen und Installieren von Enterprise Edition-JDK-Builds von Azul Zulu für Azure aus einem Yum-Repository

Die Azul Zulu JDKs werden von Azul in einem [Yum-Repository](https://repos.azul.com/azure-only/zulu-azure.repo) bereitgestellt.

**Führen Sie über Ihre Befehlszeilenschnittstelle die folgenden Befehle aus, um das Azul Zulu JDK für Java 8 zu installieren:**

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-8-azure-jdk
```

Java 11:

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-11-azure-jdk
```

Java 13 (Vorschauversion):

```cli
sudo rpm --import http://repos.azul.com/azul-repo.key
sudo curl http://repos.azul.com/azure-only/zulu-azure.repo -o /etc/yum.repos.d/zulu-azure.repo
sudo yum -q -y update
sudo yum -q -y install zulu-13-azure-jdk
```

**So aktualisieren Sie ein Zulu JDK 8-Paket aus einem Yum-Repository:**

```cli
sudo yum -q -y install zulu-8-azure-jdk
```

(Bei Verwendung einer anderen Version muss die Versionsnummer im obigen Befehl entsprechend geändert werden.)

**So entfernen Sie ein Zulu JDK 8-Paket aus einem Yum-Repository:**

```cli
sudo yum -y erase zulu-8-azure-jdk
```

(Bei Verwendung einer anderen Version muss die Versionsnummer im obigen Befehl entsprechend geändert werden.)

## <a name="download-and-install-the-azul-zulu-jdks-from-an-apt-get-repository"></a>Herunterladen und Installieren der Azul Zulu JDKs aus einem apt-get-Repository

Die Azul Zulu JDKs werden von Azul auch in einem [apt-get-Repository](https://repos.azul.com/azure-only/zulu/apt) bereitgestellt.

**Führen Sie über Ihre Befehlszeilenschnittstelle die folgenden Befehle aus, um das Azul Zulu JDK für Java 8 mit apt-get zu installieren:**

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

Java 11:

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-11-azure-jdk
```

Java 13 (Vorschauversion):

```cli
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
sudo apt-get -q update
sudo apt-get -y install zulu-13-azure-jdk
```

**So aktualisieren Sie ein Zulu JDK 8-Paket aus einem apt-get-Repository:**

```cli
sudo apt-get -q update
sudo apt-get -y install zulu-8-azure-jdk
```

Das vorherige Release wird automatisch entfernt.
(Bei Verwendung einer anderen Version muss die Versionsnummer im obigen Befehl entsprechend geändert werden.)

**So entfernen Sie ein Zulu JDK 8-Paket aus einem apt-get-Repository:**

```cli
sudo apt-get -y purge zulu-8-azure-jdk
```

(Bei Verwendung einer anderen Version muss die Versionsnummer im obigen Befehl entsprechend geändert werden.)

Ausführlichere Informationen zum Vorbereiten, Installieren und Verwalten Ihrer Azul Zulu JDKs für die Azure-Entwicklung finden Sie in der [offiziellen Zulu-Dokumentation](https://docs.azul.com/zulu/zuludocs/index.htm).
