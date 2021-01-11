---
title: Java JDKs und mittel- und langfristiger Support der Azure-Entwicklung
description: In diesem Artikel werden Downloads und eine Erklärung des Azure-Supports für das Entwickeln und Ausführen von Java-Anwendungen vorgestellt.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-september2019, devx-track-java
ms.openlocfilehash: 165fcf08089690685067a694a2f813b67a50ed33
ms.sourcegitcommit: 84f64dec74b4b041b8830a4e7489e22f0e943440
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97864267"
---
# <a name="java-long-term-support-and-medium-term-support-on-azure-and-azure-stack"></a>Langfristiger und mittelfristiger Java-Support für Azure und Azure Stack

Java-Entwickler, die mit Azure und Azure Stack arbeiten, können Java-Produktionsanwendungen mithilfe von JDK-Builds von [Azul Zulu für Azure – Enterprise Edition](https://www.azul.com/downloads/azure-only/zulu/) erstellen und ausführen, ohne dass zusätzliche Supportkosten anfallen. Sie können in Azure eine beliebige Java-Runtime verwenden. Bei Verwendung von Zulu erhalten Sie jedoch kostenlose Wartungsupdates und können Supporttickets bei Microsoft erstellen.

Bei einem als LTS (Long Term Support, langfristiger Support) gekennzeichneten Release handelt es sich um die gleichen LTS-Releases, die auch von Oracle und von der OpenJDK-Community angegeben werden. Für LTS-Releases bieten wir bei Bedarf mindestens acht Jahre lang Zugriff auf Fehlerbehebungen, Sicherheitsupdates und andere Fixes (Produktionssupport). Außerdem bieten wir zwei Jahre lang zusätzliche Unterstützung für Benutzer, die zu einer neueren JDK-Version migrieren (erweiterter Support).

Für als MTS (Medium Term Support, mittelfristiger Support) gekennzeichnete Releases bieten wir Produktionssupport für mindestens 1,5 Jahre ab der allgemeinen Verfügbarkeit des nächsten LTS-Release. Außerdem bieten wir ein zusätzliches Jahr mit erweitertem Support.

> [!div class="nextstepaction"]
> [Java herunterladen und installieren](java-jdk-install.md)

## <a name="long-term-support-lts"></a>Langfristiger Support (Long-Term Support, LTS)

* [Java 11](https://www.azul.com/downloads/azure-only/zulu/?version=java-11-lts)
* [Java 8](https://www.azul.com/downloads/azure-only/zulu/?version=java-8-lts)
* [Java 7](https://www.azul.com/downloads/azure-only/zulu/?version=java-7-lts)

## <a name="medium-term-support-mts"></a>Mittelfristiger Support (Medium Term Support, MTS)

* [Java 15](https://www.azul.com/downloads/azure-only/zulu/?version=java-15)
* [Java 13](https://www.azul.com/downloads/azure-only/zulu/?version=java-13)

## <a name="what-is-the-zulu-openjdk-for-azure"></a>Was ist Zulu OpenJDK für Azure?

Bei Enterprise Edition-Builds von Azul Zulu für Azure von OpenJDK handelt es sich um eine kostenlose, plattformübergreifende und produktionsbereite Distribution von OpenJDK für Azure und Azure Stack. Sie werden von Microsoft und Azul Systems unterstützt. Merkmale dieser Distributionen:

* Hundertprozentige Open-Source-Builds von OpenJDK, verpackt als JDKs (Java Development Kits), JREs (Java Runtime Environments) und monitorlose JREs. Bei diesen Binärdateien handelt es sich um vollständig kompatible und konforme kommerzielle Builds von Java Standard Edition (SE), die mit Java-Anwendungen oder -Komponenten in Azure und Azure Stack verwendet werden können.
* Langfristiger Support – einschließlich Fehlerbehebungen, Leistungsoptimierungen und Sicherheitspatches.
* Verfügbar für die Entwicklung und Ausführung von Java-Anwendungen unter Windows, Linux und macOS.
* Verfügbar als Containerimages in Docker Hub sowie als virtuelle Computer (Windows und Linux) im Azure Marketplace
* Sie werden von Microsoft Azure für verschiedenste Azure-Dienste genutzt. Beispiele:
  * App Service unter Windows
  * App Service unter Linux
  * Azure-Funktionen
  * Azure Service Fabric
  * Azure HDInsight
  * Azure Cognitive Search
  * Azure DevOps
  * Azure Cloud Shell  

## <a name="supported-java-versions-and-update-schedule"></a>Unterstützte Java-Versionen und Zeitplan für Updates

Azul Systems stellt vollständig unterstützte [Enterprise Edition-Builds von Azul Zulu für Azure](https://www.azul.com/downloads/azure-only/zulu/) für alle LTS- und MTS-Versionen von Java bereit (einschließlich Java SE 7, 8, 11, 13 und 15). Weitere Informationen finden Sie in der [Azul-Pressemitteilung](https://www.azul.com/press_release/free-java-production-support-for-microsoft-azure-azure-stack) sowie in der Roadmap zum [Supportlebenszyklus für Azul-Produkte](https://www.azul.com/products/azul_support_roadmap/).

|Java SE-Version  |Ende der Unterstützung  |
|---------|----------|
|[![Java 7-Logo](media/supported-java-versions-java-7.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-7-lts) |Juli 2023 (LTS)|
|[![Java 8-Logo](media/supported-java-versions-java-8.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-8-lts) |Dezember 2030 (LTS)|
|[![Java 11-Logo](media/supported-java-versions-java-11.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-11-lts) |September 2027 (LTS)|
|[![Java 13-Logo](media/supported-java-versions-java-13.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-13) |März 2023 (MTS)|
|[![Java 15-Logo](media/supported-java-versions-java-15.png)](https://www.azul.com/downloads/azure-only/zulu/?version=java-15) |März 2023 (MTS)|

Für LTS- und MTS-JDK-Releases werden vierteljährliche Sicherheitsupdates, Fehlerbehebungen sowie wichtige Out-of-band-Updates und -Patches nach Bedarf bereitgestellt. Dieser Support umfasst Zurückportierungen von Sicherheitsupdates auf Java 7 und 8 sowie die Behebung von Fehlern, die in neueren Versionen von Java (etwa in Java 11) gemeldet wurden. Diese Zurückportierung gewährleistet die kontinuierliche Stabilität und Sicherheit von älteren Java-Versionen. Azure-Kunden können diese Sicherheitsupdates und Fehlerbehebungen für Plattformen beziehen, ohne dass ungeplante Gebühren für Java SE-Abonnements anfallen.

Derzeit ist für Azure Functions Java 8 erforderlich, und die Unterstützung für Java 11 befindet sich noch in der Entwicklung.

## <a name="benefits-for-developers"></a>Vorteile für Entwickler

Releases des Enterprise Edition-JDK von Azul Zulu für Azure:

- Werden von Microsoft und Azul Systems unterstützt.

   * Zulu-Binärdateien sind produktionsbereit und werden von Microsoft und Azul Systems unterstützt.
   * Zulu bietet kostenlosen langfristigen Support (Long-Term Support, LTS) für Java 7, 8 und 11 sowie mittelfristigen Support (Medium-Term Support, MTS) für Java 13 und 15. (LTS wird auch für Java 17 bereitgestellt.) Java-Versionen müssen nur bei Bedarf aktualisiert werden.
   * Java 7 wird bis Juli 2023 unterstützt. Support für Java 8 wird bis Dezember 2030 bereitgestellt. Support für Java 11 wird bis September 2027 bereitgestellt. Support für Java 13 und 15 wird bis März 2023 bereitgestellt.
   * Microsoft ist bestrebt, Zulu intern auf Computern auszuführen, die vielen Azure-Diensten zugrunde liegen.

- Sind produktionsbereit.

   * 100-prozentige Open-Source-Builds von OpenJDK.
   * Direkter Ersatz für zahlreiche Java SE-Distributionen
   * JDK, JRE und monitorlose JRE (headless).
   * Java 7, 8, 11, 13 und 15
   * Konformität mit den Java SE-Spezifikationen über das OpenJDK Community Technology Compatibility Kit (TCK) geprüft.
   * Enthält Produktionsupdates für Java SE – einschließlich Fehlerbehebungen, Leistungsverbesserungen und Sicherheitspatches für Java SE 7, 8, 11, 13 und 15.

- Werden für mehrere Plattformen unterstützt. Zulu unterstützt Binärdateien für mehrere Plattformen und Versionen:

   * Windows
     * 10
     * 8.1
     * 8
     * 7
   * Windows Server
     * 2016 R2
     * 2016
     * 2012 R2
     * 2012
     * 2008 R2
   * Linux
     * RHEL
     * CentOS
     * Ubuntu
     * SLES
     * Debian
     * Oracle Linux
   * Mac OS X
   * Bereitgestellt in mehreren Pakettypen:
     * MSI, ZIP, TAR, DEB, RPM und DMG

     Auf Docker Hub stehen zertifizierte Docker-Containerimages für das Zulu JDK, die JRE und die monitorlose JRE für mehrere Basisbetriebssystem-Images zur Verfügung:

     * [JDK](https://hub.docker.com/_/microsoft-java-jdk)
     * [JRE](https://hub.docker.com/_/microsoft-java-jre)
     * [Monitorlose JRE](https://hub.docker.com/_/microsoft-java-jre-headless)

- Sind kostenlos.

   * Microsoft stellt Ihnen kostenlos alles zur Verfügung, was Sie zum Erstellen und Skalieren von Java-Apps in Azure benötigen. Über Zulu erhalten Sie kostenlose Sicherheitsupdates und plattformspezifische Fehlerbehebungen für Java-Apps.
   * [Java Flight Recorder und Zulu Mission Control](java-jdk-flight-recorder-and-mission-control.md) stehen in Zulu Java 8, 11 und höheren Versionen zur Verfügung.

- Enthalten technische Vorschauversionen von Nicht-LTS-/MTS-Versionen.

   * Mit technischen Vorschauversionen können Sie neues Features schrittweise testen, während diese in kurzfristigen Versionen bereitgestellt werden, die letztendlich zu Java 17 LTS weiterentwickelt werden.

- Enthalten Upstreamänderungen an OpenJDK.
   * Azul Systems-Committer pushen Zulu-Änderungen in OpenJDK. Durch diese Commits wird das Upstreamrepository umfassend und inklusiv.

Java-Entwickler können wie gewohnt ihre eigenen Java-Runtimes (einschließlich Oracle JDK und Red Hat JDK) in Azure verwenden und dabei die sichere Infrastruktur und die umfangreichen Dienste nutzen. Die Produktionsedition von Oracle Java SE ist ebenfalls für Java-Entwickler verfügbar, um Java-Workloads auf virtuellen Windows- oder Linux-Computern in Azure ausführen zu können.

## <a name="use-for-local-development"></a>Verwendung für lokale Entwicklung

Entwickler können [Java-JDKs für Azure und Azure Stack](https://www.azul.com/downloads/azure-only/zulu/) zur Verwendung in lokalen Entwicklungsumgebungen herunterladen. Downloads sind für Windows, Linux und MacOS verfügbar. Unter Linux können Entwickler Pakete auch über die Paket-Manager [yum](https://www.azul.com/downloads/azure-only/zulu/#yum-repo) und [apt](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) abrufen.

Weitere Informationen finden Sie unter [Docker-Images für Azure](java-jdk-docker-images.md).
