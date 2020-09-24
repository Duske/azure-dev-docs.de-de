---
title: Tools für Entwickler, die das Azure SDK für Go nutzen
description: Tools zur Verwendung mit dem Azure SDK für Go und Azure-Diensten
ms.date: 09/05/2018
ms.topic: conceptual
ms.openlocfilehash: e52845218de7d874e045a11c004360b7a35baa03
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831306"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Tools für Entwickler, die das Azure SDK für Go nutzen

Hier finden Sie einige Empfehlungen für Tools, mit denen Sie Go-Code effizient schreiben und problemlos in Azure-Diensten nutzen können.

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Die Azure CLI stellt eine Befehlszeilenschnittstelle zum Erstellen und Konfigurieren von Azure-Ressourcen in Ihren Abonnements bereit. Die CLI erleichtert Ihnen den schnellen Einstieg in die Erstellung allgemeiner und gemeinsam genutzter Azure-Ressourcen, sodass Sie sich auf die komplexere Nutzung von Diensten konzentrieren können. Die CLI bietet Abfrage- und Filterfunktionen, sodass Sie Ausgaben direkt an Ihre bevorzugten Befehlszeilentools weiterreichen können. Die CLI steht für die Installation auf Ihrem lokalen System, als Docker-Image oder über [Azure Cloud Shell](/azure/cloud-shell/overview) zur Verfügung.

> [!div class="nextstepaction"]
> [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code ist ein einfacher Editor, der Go unterstützt. Diese Erweiterung bietet Features wie AutoVervollständigen, `impl`-Vorlagen, Refactoring und Debuggen. Visual Studio Code bietet außerdem Unterstützung für den Zugriff auf die Quellcodeverwaltung im Editor sowie Erweiterungen für die Verwendung von Azure-Diensten.

* [Installieren von Visual Studio Code](https://code.visualstudio.com/Download)
* [Abrufen der Visual Studio Code-Erweiterung für Go](https://code.visualstudio.com/docs/languages/go)
* [Abrufen der Visual Studio Code-Erweiterung für Azure-Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>CI/CD mit Azure DevOps-Projekt

Azure DevOps-Projektpipelines ermöglichen die Einrichtung eines Continuous Integration-Systems für Ihre Go-Anwendungen. Dafür ist lediglich ein Git-Repository erforderlich, und Sie können direkt in Azure bereitstellen und testen.

> [!div class="nextstepaction"]
> [Informationen zum Erstellen einer CI/CD-Pipeline mit Azure DevOps-Projekt](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>Verwaltung von Abhängigkeiten mit dep

Das Azure SDK für Go nutzt dep für die Verwaltung von Abhängigkeiten. Der dep-Befehl ermöglicht das Abrufen und Vendoring von Anforderungen für Ihre Go-Anwendung, um Versionskonflikte zu vermeiden und die ordnungsgemäße Funktion Ihres Projekts sicherzustellen.

> [!div class="nextstepaction"]
> [Abrufen des dep-Abhängigkeit-Managers](https://github.com/golang/dep)