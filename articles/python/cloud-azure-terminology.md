---
title: Spickzettel für Azure-Terminologie
description: Enthält eine kurze Liste mit den wichtigsten Begriffen und Konzepten, mit denen Sie bei der Nutzung von Microsoft Azure vertraut sein müssen.
ms.date: 12/07/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: f79864b39bb70c7ae703468f205e420417698200
ms.sourcegitcommit: c8330128d5d6a71859933a890ecdf047cb950996
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521981"
---
# <a name="azure-terminology-in-brief"></a>Kurzübersicht über die Azure-Terminologie

| Begriff | Kurze Beschreibung |
| --- | --- |
| [Konto und Abonnements](#account-and-subscriptions) | Umfasst die Abrechnungsinformationen und die grundlegende Organisationsstruktur für die Verwaltung von Ressourcen in Azure. [Weitere Informationen...](#account-and-subscriptions)
| [Ressource](#resource) | Der allgemeine Name einer spezifischen Zuordnung von Funktionen in einem Azure-Rechenzentrum. [Weitere Informationen...](#resource) |
| [Ressourcengruppe](#resource-group) | Ein logischer Container für mehrere Ressourcen, die Sie dann gemeinsam verwalten können. [Weitere Informationen...](#resource-group) |
| [Region](#region-location) | Ein Verweis auf ein bestimmtes Azure-Rechenzentrum, in dem Ressourcen zugeordnet werden. [Weitere Informationen...](#region-location) |
| [Azure App Service](#azure-app-service) | Der verwaltete Hostingdienst von Azure für Webanwendungen. [Weitere Informationen...](#azure-app-service) |
| [App Service-Plan](#app-service-plan) | Eine Ressource, mit der der von Azure App Service verwendete virtuelle Computer definiert wird. [Weitere Informationen...](#app-service-plan) |
| [Azure portal](#azure-portal) | Die webbasierte Benutzeroberfläche zum Erstellen und Verwalten von Azure-Ressourcen. [Weitere Informationen...](#azure-portal) |
| [Azure CLI](#azure-command-line-interface-cli) | Umfasst textbasierte Befehle zum Erstellen und Verwalten von Azure-Ressourcen. [Weitere Informationen...](#azure-command-line-interface-cli) |
| [az webapp, az webapp up](#az-webapp-az-webapp-up) | Die Azure CLI-Befehle für Azure App Service. [Weitere Informationen...](#az-webapp-az-webapp-up) |

## <a name="account-and-subscriptions"></a>Konto und Abonnements

Ein **Azure-Konto** enthält Ihre grundlegenden Kontakt- (Telefonnummer, E-Mail-Adresse) und Abrechnungsinformationen (Kreditkarte).

Für ein Abrechnungskonto können Sie Ihre Aktivitäten in einem oder mehreren **Abonnements** organisieren. Jedes Abonnement verfügt über eigene Berechtigungen. Sie können unter einem Konto separate Abonnements für Einzelpersonen oder Abteilungen erstellen. Eine Einzelperson kann über Zugriff auf beliebig viele Abonnements verfügen.

Die Erstellung von Ressourcen in Azure erfolgt immer innerhalb des Kontexts eines Abonnements. Sie können [Ressourcen zwischen Abonnements verschieben](/azure/azure-resource-manager/management/move-resource-group-and-subscription), aber nicht zwischen Konten.

## <a name="resource"></a>Resource

**Ressource** ist der allgemeine Name einer spezifischen Zuordnung von Funktionen in einem Azure-Rechenzentrum. Bei einer Ressource kann es sich um einen virtuellen Computer, ein virtuelles Netzwerk, verschiedene Speicherebenen, eine Datenbank, ein Machine Learning-Modell, einen IoT-Hub für die Erfassung usw. handeln.

Da bei einer Ressource reale Computingfunktionen zugeordnet werden, fallen für jede Ressource je nach benötigter Leistungsebene potenziell laufende Kosten an. Für Entwicklungs- und Testzwecke können viele Ressourcen im Free-Tarif erstellt werden (also kostenlos). Weitere Informationen finden Sie unter [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

## <a name="resource-group"></a>Ressourcengruppe

Innerhalb eines Abonnements handelt es sich bei einer *Ressourcengruppe* um einen logischen Container für mehrere Ressourcen, die Sie dann gemeinsam verwalten können.

Eine Ressourcengruppe gehört in der Regel zu einem bestimmten Projekt, und Sie müssen immer eine Ressourcengruppe angeben, wenn Sie eine Ressource bereitstellen. Der erste Schritt bei einem neuen Projekt besteht in der Regel darin, eine geeignete Ressourcengruppe zu erstellen.

Beim Löschen einer Ressourcengruppe wird die Zuordnung aller darin enthaltenen Ressourcen aufgehoben, und die Ressourcen werden gelöscht. Dies ist komfortabler als das Löschen von einzelnen Ressourcen.

## <a name="region-location"></a>Region (Standort)

Mit einer **Region** wird der jeweilige Standort des Azure-Rechenzentrums angegeben, in dem eine Ressource bereitgestellt wird.

Unterschiedliche Ressourcen können immer regionsübergreifend kommunizieren, aber die Effizienz ist höher, wenn sie sich in derselben Region befinden.

Für Anwendungen, die für Kunden weltweit bestimmt sind, kann Azure so eingerichtet werden, dass Ressourcen automatisch in mehreren Regionen repliziert werden. Auf diese Weise wird die allgemeine Reaktionsfähigkeit und Leistung der Anwendung verbessert.

## <a name="azure-app-service"></a>Azure App Service

**App Service** ist der verwaltete Hostingdienst von Azure für Webanwendungen. Azure verwaltet die zugrunde liegende Hardware und Serverinfrastruktur, und Sie stellen den Code und die Konfiguration bereit.

Bei Verwendung von App Service stellen Sie den Host bereit, der als App Service-**Web-App** bezeichnet wird, und laden Ihren Code hoch. Darüber hinaus konfigurieren Sie auch verschiedene Merkmale des Hosts, z. B. Lastenausgleich, Skalierung, serverseitige Umgebungsvariablen und mehr.

Die direkte URL für eine App Service-Web-App hat immer das folgende Format: `<web-app-name>.azurewebsites.net`. Sie können die Web-App auch so konfigurieren, dass eine benutzerdefinierte Domäne verwendet wird.

## <a name="app-service-plan"></a>App Service-Plan

Ein **App Service-Plan** ist eine Ressource, mit der der von Azure App Service verwendete virtuelle Computer definiert wird. Dies ist dann die Basis für die Ermittlung der grundlegenden Kosten für das Hosten Ihrer Anwendung. Sie definieren einen App Service-Plan im Rahmen der App Service-Bereitstellung, bevor Sie Ihren Code bereitstellen.

## <a name="azure-portal"></a>Azure-Portal

Das [Azure-Portal](https://portal.azure.com) ist die webbasierte Benutzeroberfläche, über die Sie Ihr Azure-Konto, die Abonnements und die Ressourcen nutzen können.

## <a name="azure-command-line-interface-cli"></a>Azure-Befehlszeilenschnittstelle (CLI)

Die [Azure CLI](/cli/azure/what-is-azure-cli) enthält Befehle zum Erstellen und Verwalten von Azure-Ressourcen. Dies ist besonders in Bezug auf die Automatisierung hilfreich. Der Azure CLI ist auf allen Betriebssystemen verfügbar und funktioniert für die meisten Azure-Dienste.

Falls Sie PowerShell bevorzugen, können Sie auch das [Azure PowerShell-Modul](/powershell/azure) verwenden.

## <a name="az-webapp-az-webapp-up"></a>az webapp, az webapp up

Mit dem Befehl **az webapp** können Sie über die Azure CLI mit allen Bereichen von Azure App Service interagieren.

Der Befehl **az webapp up** dient zur Vereinfachung der Bereitstellung von Webanwendungen. Mit diesem Befehl können Sie eine Ressourcengruppe, einen App Service-Plan und die App Service-Web-App bereitstellen und dann Ihren Code hochladen – alles in einem Schritt.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Übersicht über die Cloudentwicklung >>>](cloud-development-overview.md)
