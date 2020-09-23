---
title: Installieren des Azure-Toolkits für Eclipse
description: Hier erfahren Sie, wie Sie das Plug-In für das Azure-Toolkit für Eclipse installieren, um in Azure Cloudanwendungen zu erstellen und bereitzustellen.
documentationcenter: java
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.date: 08/25/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 27793d827b60a5977968529377b20c7033170ffe
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534660"
---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>Installieren des Azure-Toolkits für Eclipse

Das Azure-Toolkit für Eclipse verfügt über Funktionen, die Ihnen das leichte Erstellen, Entwickeln, Konfigurieren, Testen und Bereitstellen von einfachen, hoch verfügbaren und skalierbaren Java-Web-Apps und HDInsight Spark-Aufträgen in Azure über die Eclipse-Entwicklungsumgebung ermöglichen.

> [!NOTE] 
> 
> Das Azure Toolkit für Eclipse ist ein Open-Source-Projekt, dessen Quellcode unter der MIT-Lizenz auf der GitHub-Website des Projekts verfügbar ist: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

Es gibt zwei Methoden, mit denen Sie das Azure-Toolkit für Eclipse installieren können: über den **Eclipse-Marketplace** und mit der Option **Neue Software installieren** im Menü „Hilfe“. Diese beiden Installationsmethoden werden in den folgenden Abschnitten veranschaulicht.

## <a name="eclipse-marketplace"></a>Eclipse-Marketplace

Mit dem Assistenten für den Eclipse-Marketplace in der Eclipse-IDE können Benutzer den [Eclipse-Marketplace](https://marketplace.eclipse.org/) durchsuchen und Lösungen installieren. Sie können die folgenden beiden Optionen verwenden, um auf den Eclipse-Marketplace zuzugreifen:

   * Ziehen Sie die folgende Schaltfläche in Ihren aktiven Eclipse-Arbeitsbereich. Mit dieser Schaltfläche wird der Eclipse-Marketplace so geöffnet, dass das Azure-Toolkit für Eclipse bereits ausgewählt ist.

      [![Ziehen Sie dieses Element in Ihren aktiven Eclipse*-Arbeitsbereich. *Eclipse Marketplace-Client erforderlich](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Ziehen Sie dieses Element in Ihren aktiven Eclipse*-Arbeitsbereich. *Eclipse Marketplace-Client erforderlich")

   * Klicken Sie in der Eclipse-IDE auf das Menü **Hilfe**, navigieren Sie zum **Eclipse-Marketplace**, suchen Sie nach „Azure-Toolkit für Eclipse“, und klicken Sie auf **Installieren**.

      :::image type="content" source="media/installation/eclipse-marketplace-button.png" alt-text="Marketplace-Fenster: Menü „Hilfe“"::: 

1. Ein Assistent für den Eclipse-Marketplace mit einer Installationsanleitung wird geöffnet. Zusätzlich wird eine Liste mit den Komponenten angezeigt, die installiert werden. Vergewissern Sie sich, dass alle Features richtig ausgewählt sind, und klicken Sie auf **Bestätigen >** .

   | Funktion | BESCHREIBUNG | 
   |---|---| 
   | **Application Insights Plugin for Java** (Application Insights-Plug-In für Java) | Ermöglicht Ihnen die Verwendung der Telemetriedatenprotokollierung und der Analysedienste von Azure für Ihre Anwendungen und Serverinstanzen. | 
   | **Azure Common Plugin** (Allgemeines Azure-Plug-In) | Stellt die allgemeine Funktionalität bereit, die für andere Komponenten des Toolkits erforderlich ist. | 
   | **Azure Container Tools for Eclipse** (Azure-Containertools für Eclipse) | Ermöglicht das Erstellen und Bereitstellen einer WAR-Datei als Docker-Container auf einem Docker-Computer. | 
   | **Azure Containers for Eclipse** (Azure-Container für Eclipse) | Ermöglicht das Bereitstellen eines WAR- oder JAR-Artefakts als Docker-Container auf einem virtuellen Azure-Computer. | 
   | **Azure Explorer for Eclipse** (Azure Explorer für Eclipse) | Bietet eine dem Explorer ähnliche Oberfläche zum Verwalten Ihrer Azure-Ressourcen. | 
   | **Azure HDInsight-Plug-In für Java** | Ermöglicht die Apache Spark-Anwendungsentwicklung in Scala. |
   | **Microsoft JDBC-Treiber 6.1 für SQL Server** | Stellt die JDBC-API für SQL Server und Microsoft Azure SQL-Datenbank für Java Platform Enterprise Edition 8 bereit. | 
   | **Package for Microsoft Azure Libraries for Java** (Paket für Microsoft Azure-Bibliotheken für Java) | Stellt APIs für den Zugriff auf Microsoft Azure-Dienste wie Storage, Service Bus, Service Runtime usw. bereit. | 
   | **Web-App-Plug-In für Eclipse** | Ermöglicht Ihnen die Bereitstellung Ihrer Webanwendungen als Azure App Services. | 

1. Überprüfen Sie im Dialogfeld **Review Licences** die Bedingungen der Lizenzverträge. Wenn Sie dem Lizenzvertrag zustimmen, klicken Sie auf **I accept the terms of the license agreements** (Ich akzeptiere die Bedingungen der Lizenzvereinbarungen) und dann auf **Fertig stellen**. 

   > [!NOTE]
   > Sie können den Status der Installation unten rechts in Ihrem Eclipse-Arbeitsbereich überprüfen.

4. Nach Abschluss des Installationsvorgangs werden Sie aufgefordert, die Eclipse-IDE neu zu starten, um das Softwareupdate anzuwenden. Klicken Sie auf **Jetzt neu starten**.

## <a name="install-new-software"></a>Installieren neuer Software

Sie können das Azure-Toolkit für Eclipse direkt über das Menü *Hilfe* als neue Software installieren.

1. Klicken Sie auf das Menü **Hilfe** und dann auf **Neue Software installieren**.

   :::image type="content" source="media/installation/eclipse-install-software-button.png" alt-text="Installieren neuer Software: Menü „Hilfe“"::: 

1. Geben Sie im Dialogfeld **Verfügbare Software** im Textfeld **Arbeiten mit** den Text `http://dl.microsoft.com/eclipse/` ein.

1. Aktivieren Sie im Fenster **Name** die Option **Azure Toolkit for Java**, und deaktivieren Sie **Contact all update sites during install to find required software** (Während der Installation alle Updatesites kontaktieren, um erforderliche Software zu finden). Der Bildschirm sieht dann etwa wie folgt aus:

   ![Installieren des Azure-Toolkits für Eclipse][02]

1. Wenn Sie das **Azure-Toolkit für Java** erweitern, wird eine Liste mit den Komponenten angezeigt, die installiert werden, z. B.:

   | Funktion | BESCHREIBUNG | 
   |---|---| 
   | **Application Insights Plugin for Java** (Application Insights-Plug-In für Java) | Ermöglicht Ihnen die Verwendung der Telemetriedatenprotokollierung und der Analysedienste von Azure für Ihre Anwendungen und Serverinstanzen. | 
   | **Azure Common Plugin** (Allgemeines Azure-Plug-In) | Stellt die allgemeine Funktionalität bereit, die für andere Komponenten des Toolkits erforderlich ist. | 
   | **Azure Container Tools for Eclipse** (Azure-Containertools für Eclipse) | Ermöglicht das Erstellen und Bereitstellen einer WAR-Datei als Docker-Container auf einem Docker-Computer. | 
   | **Azure Containers for Eclipse** (Azure-Container für Eclipse) | Ermöglicht das Bereitstellen eines WAR- oder JAR-Artefakts als Docker-Container auf einem virtuellen Azure-Computer. | 
   | **Azure Explorer for Eclipse** (Azure Explorer für Eclipse) | Bietet eine dem Explorer ähnliche Oberfläche zum Verwalten Ihrer Azure-Ressourcen. | 
   | **Azure HDInsight-Plug-In für Java** | Ermöglicht die Apache Spark-Anwendungsentwicklung in Scala. |
   | **Microsoft JDBC-Treiber 6.1 für SQL Server** | Stellt die JDBC-API für SQL Server und Microsoft Azure SQL-Datenbank für Java Platform Enterprise Edition 8 bereit. | 
   | **Package for Microsoft Azure Libraries for Java** (Paket für Microsoft Azure-Bibliotheken für Java) | Stellt APIs für den Zugriff auf Microsoft Azure-Dienste wie Storage, Service Bus, Service Runtime usw. bereit. | 
   | **Web-App-Plug-In für Eclipse** | Ermöglicht Ihnen die Bereitstellung Ihrer Webanwendungen als Azure App Services. | 

1. Klicken Sie auf **Weiter**. (Wenn ungewöhnliche Verzögerungen bei der Installation des Toolkits auftreten, achten Sie darauf, dass **Contact all update sites during install to find required software** deaktiviert ist.)

1. Klicken Sie im Dialogfeld **Install Details** (Installationsdetails) auf **Weiter**.

1. Überprüfen Sie im Dialogfeld **Review Licences** die Bedingungen der Lizenzverträge. Wenn Sie dem Lizenzvertrag zustimmen, klicken Sie auf **I accept the terms of the license agreements** (Ich akzeptiere die Bedingungen der Lizenzvereinbarungen), und klicken Sie dann auf **Fertig stellen**. (Bei den restlichen Schritten wird davon ausgegangen, dass Sie dem Lizenzvertrag zugestimmt haben. Wenn Sie die Bedingungen der Lizenzverträge nicht akzeptieren, beenden Sie den Installationsprozess.)

   > [!NOTE]
   > Sie können den Status der Installation unten rechts in Ihrem Eclipse-Arbeitsbereich überprüfen.

1. Wenn Sie aufgefordert werden, Eclipse zum Abschließen der Installation neu zu starten, klicken Sie auf **Restart Now**.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

<!-- IMG List -->
[02]: media/installation/eclipse-installation-02.png
