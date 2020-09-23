---
title: Installieren des Azure-Toolkits für IntelliJ
description: Hier erfahren Sie, wie Sie das Plug-In für das Azure-Toolkit für IntelliJ installieren, um in Azure Cloudanwendungen zu erstellen und bereitzustellen.
documentationcenter: java
ms.assetid: c6817c7b-f28c-4c06-8216-41c7a8117de3
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: fe8b07257ff3a9fc5523d13dd13e19982103ab05
ms.sourcegitcommit: a139e25190960ba89c9e31f861f0996a6067cd6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534462"
---
# <a name="installing-the-azure-toolkit-for-intellij"></a>Installieren des Azure-Toolkits für IntelliJ

Das Azure-Toolkit für IntelliJ stellt Vorlagen und Funktionen bereit, die Ihnen das einfache Erstellen, Entwickeln, Testen und Bereitstellen von Cloudanwendungen in Azure mithilfe der IntelliJ IDEA-Entwicklungsumgebung ermöglichen.

> [!NOTE] 
> 
> Das Azure-Toolkit für IntelliJ ist ein Open-Source-Projekt, dessen Quellcode unter der MIT-Lizenz auf der GitHub-Website des Projekts verfügbar ist: 
> 
> <https://github.com/microsoft/azure-tools-for-java> 
> 

Es gibt zwei Methoden zum Installieren des Azure-Toolkits für IntelliJ: über das Dialogfeld **Einstellungen** und über das Menü **Konfigurieren** auf dem Startbildschirm. Beide Installationsmethoden werden in den folgenden Schritten veranschaulicht.

## <a name="prerequisites"></a>Voraussetzungen

Für das Azure-Toolkit für IntelliJ sind folgende Softwarekomponenten erforderlich:

* Ein installiertes Java Development Kit (JDK) ab Version 8, beispielsweise: [OpenJDK](https://openjdk.java.net/) oder [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Eine installierte Instanz von [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) (Ultimate Edition oder Community Edition)

> [!NOTE]
> 
> Auf der Seite [Azure-Toolkit für IntelliJ](https://plugins.jetbrains.com/plugin/8053) im Repository für JetBrains-Plug-Ins sind die mit dem Toolkit kompatiblen Builds aufgeführt.
> 

<!--
> [!IMPORTANT]
> 
> If you are using the Azure Toolkit for IntelliJ on Windows, the toolkit requires installing the Azure SDK 2.9.6 or later in order to use the Azure emulator. You have two options for installing the Azure SDK:
> 
> * You can download and install the Azure SDK by using the [Web Platform Installer (WebPI)](https://go.microsoft.com/fwlink/?LinkID=252838).
> * If you do not have the Azure SDK installed when you create your first Azure deployment project, you will be prompted to automatically download install the requisite version of the Azure SDK.
> 
> Note that the Azure SDK is only required on Windows.
> 
-->


## <a name="from-the-settings-dialog-box"></a>Über das Dialogfeld „Einstellungen“

1. Klicken Sie auf der IntelliJ-Symbolleiste auf **Datei** und dann auf **Einstellungen**.

1. Klicken Sie im Dialogfeld „Einstellungen“ im linken Navigationsmenü auf **Plug-Ins**.

1. Geben Sie in der **Marketplace**-Suchleiste den Suchbegriff „Azure“ ein, um die Liste mit den Plug-Ins zu filtern. Wählen Sie **Azure Toolkit for IntelliJ** (Azure-Toolkit für IntelliJ) aus, und klicken Sie anschließend auf **Install** (Installieren). Lesen Sie den Text unter *Third-party Plugins Privacy Note* (Datenschutzhinweis für Drittanbieter-Plug-Ins) für IntelliJ, und klicken Sie auf **Accept** (Akzeptieren).

   :::image type="content" source="media/installation/03-intellij-search-plugin.png" alt-text="Suchen nach dem Plug-In „Azure-Toolkit für IntelliJ“"::: 

1. Klicken Sie nach Abschluss der Installation auf **Restart IDE** (IDE neu starten).

1. Wenn die Aufforderung zum Neustarten von IntelliJ IDEA angezeigt wird, klicken Sie auf **Restart**(Neu starten).
   
   :::image type="content" source="media/installation/07-restart-intellij.png" alt-text="Neustarten von IntelliJ IDEA"::: 

## <a name="from-the-start-screen"></a>Über den Startbildschirm

1. Klicken Sie auf dem Startbildschirm von IntelliJ IDEA auf **Configure** (Konfigurieren) und anschließend auf **Plugins** (Plug-Ins).

   :::image type="content" source="media/installation/01-intellij-configure-dropdown.png" alt-text="Plug-Ins über den Startbildschirm"::: 

1. Geben Sie in der **Marketplace**-Suchleiste den Suchbegriff „Azure“ ein, um die Liste mit den Plug-Ins zu filtern. Wählen Sie **Azure Toolkit for IntelliJ** (Azure-Toolkit für IntelliJ) aus, und klicken Sie anschließend auf **Install** (Installieren). Lesen Sie den Text unter *Third-party Plugins Privacy Note* (Datenschutzhinweis für Drittanbieter-Plug-Ins) für IntelliJ, und klicken Sie auf **Accept** (Akzeptieren).

   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace.png" alt-text="Plug-In-Marketplace über den Startbildschirm":::

1. Klicken Sie nach Abschluss der Installation auf **Restart IDE** (IDE neu starten).

1. Wenn die Aufforderung zum Neustarten von IntelliJ IDEA angezeigt wird, klicken Sie auf **Restart**(Neu starten).
   
   :::image type="content" source="media/installation/01-intellij-start-screen-marketplace-restart.png" alt-text="Neustarten für die Installation über den Startbildschirm":::

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [additional-resources](includes/additional-resources.md)]

