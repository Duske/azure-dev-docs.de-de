---
title: Verwenden von Azure Storage für statische Websites
description: Enthält Links zur Azure Storage-Dokumentation, in der beschrieben ist, wie Sie Dateien in Speicher laden und diese dann direkt im Web bereitstellen.
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e657a20d2cb5c3d4dde50d5b12fc31a3d9980ece
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118747"
---
# <a name="how-to-create-static-websites-on-azure-storage"></a>Gewusst wie: Erstellen von statischen Websites in Azure Storage

Eine statische Website besteht aus HTML-, CSS-, JavaScript- und anderen statischen Dateien wie beispielsweise Bildern oder Schriftarten. Bei einer statischen Website handelt es sich meist um eine Single-Page-Webanwendung (SPA), die mit mehreren JavaScript-Frameworks, z. B. Angular, React oder Vue, geschrieben werden kann.

Sie können die App aber so entwerfen, dass diese Dateien ohne Nutzung eines Webservers direkt aus Azure Storage bereitgestellt werden. Das Hosten im Speicher ist einfacher und erheblich kostengünstiger als der Betrieb eines Webservers. Für das statische Hosten fallen normalerweise pro Monat nur sehr geringe Kosten an. Je nach dem von Ihnen benötigten Umfang der serverseitigen Verarbeitung können Sie diese Anforderungen häufig mit serverlosen Funktionen erfüllen, die von Azure Functions unterstützt werden.

Unter den unten angegebenen Ressourcen finden Sie alle Details zur Erstellung von statischen Websites.

- [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website): Enthält eine Übersicht über die Konfiguration von Azure Storage für das statische Hosten.

- [Hosten einer statischen Website in Azure Storage](/azure/storage/blobs/storage-blob-static-website-how-to?tabs=azure-cli): Enthält eine exemplarische Vorgehensweise zum Aktivieren des statischen Hostens, Hochladen von Dateien und Durchführen anderer Aufgaben mit dem Azure-Portal, der Azure CLI oder Azure PowerShell.

- [Einrichten eines GitHub Actions-Workflows zum Bereitstellen einer statischen Website in Azure Storage](/azure/storage/blobs/storage-blobs-static-site-github-actions): Enthält eine exemplarische Vorgehensweise zum Konfigurieren von GitHub Actions für die automatische Bereitstellung von aktualisierten Dateien aus einem Quellrepository in Azure Storage.

- [Bereitstellen einer statischen Website in Azure in Visual Studio Code](/azure/developer/javascript/tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-01): Tutorial zur Erstellung einer einfachen SPA in Angular, React, Vue und Svelte und anschließenden Bereitstellung der App in Azure Storage aus Visual Studio Code.
