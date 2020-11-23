---
title: 'Azure CLI: Virtueller Linux-Computer'
description: Hier erfahren Sie, wie Sie einen virtuellen Linux-Computer mit einem Klon einer Express.js-basierten App aus einem GitHub-Repository erstellen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 835a452e87fed88ac83085c882a5a40461bd2dd0
ms.sourcegitcommit: 6514a061ba5b8003ce29d67c81a9f0795c3e3e09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94623176"
---
# <a name="1-create-linux-virtual-machine-with-expressjs-app-using-azure-cli"></a>1. Erstellen eines virtuellen Linux-Computers mit Express.js-App mithilfe der Azure CLI

In diesem Tutorial wird ein virtueller Linux-Computer (virtual machine, VM) für eine Express.js-App erstellt. Der virtuelle Computer wird mit einer cloud-init-Konfigurationsdatei konfiguriert und enthält NGINX sowie ein GitHub-Repository für eine Express.js-App. Wenn der virtuelle Computer ausgeführt wird, können Sie eine SSH-Verbindung mit dem virtuellen Computer herstellen, die Web-App ändern, um sie mit Ablaufverfolgungsprotokollierung zu versehen, und die öffentliche Express.js-Server-App in einem Webbrowser anzeigen.

Dieses Lernprogramm enthält die folgenden Aufgaben:

* Anmelden bei Azure mit der Azure CLI
* Erstellen einer Azure-Ressource in Form eines virtuellen Linux-Computers mithilfe der Azure CLI
    * Öffnen des öffentlichen Ports 80
    * Installieren der Express.js-Demo-Web-App aus einem GitHub-Repository
    * Installieren von Web-App-Abhängigkeiten
    * Starten der Web-App
* Erstellen einer Azure Monitor-Ressource mithilfe der Azure CLI
    * Herstellen einer SSH-Verbindung mit dem virtuellen Computer
    * Installieren der Azure SDK-Clientbibliothek mit npm
    * Hinzufügen von Application Insights-Clientbibliothekscode zum Erstellen einer benutzerdefinierten Ablaufverfolgung
* Anzeigen der Web-App über den Browser
    * Anfordern der Route `/trace`, um eine benutzerdefinierte Ablaufverfolgung im Application Insights-Protokoll zu generieren
    * Anzeigen der Anzahl im Protokoll gesammelter Ablaufverfolgungen mithilfe der Azure CLI
    * Anzeigen der Liste der Ablaufverfolgungen mithilfe des Azure-Portals
* Entfernen von Ressourcen mithilfe der Azure CLI

[!INCLUDE [Create or use existing Azure Subscription ](../../includes/environment-subscription-h2.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Installierte [Azure CLI](/cli/azure/install-azure-cli) zum Ausführen von Azure CLI-Befehlen
* SSH zum Herstellen einer Verbindung mit dem virtuellen Computer: Verwenden Sie ein modernes Terminal (beispielsweise Bash-Shell), das SSH beinhaltet.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Erstellen einer Application Insights-Ressource für Webseiten](create-azure-monitoring-application-insights-web-resource.md) 