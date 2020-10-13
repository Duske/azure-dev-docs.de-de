---
title: 'Cloudentwicklung mit Azure: Was ist Azure?'
description: Eine Übersicht über die Entwicklung von Cloudanwendungen in Microsoft Azure, beginnend mit der Beziehung zwischen Rechenzentren, Diensten und Ressourcen.
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: f2bf8c0c8b35881c31fcf88c0faf38669cb261b3
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764532"
---
# <a name="cloud-development-on-azure"></a>Cloudentwicklung in Azure

Sie sind Python-Entwickler und bereit für die Entwicklung von Cloudanwendungen für Microsoft Azure. Um Sie bei der Vorbereitung auf eine lange und produktive Karriere zu unterstützen, werden Sie sich in dieser Reihe von drei Artikeln mit den grundlegenden Rahmenbedingungen der Cloudentwicklung in Azure vertraut machen.

## <a name="what-is-azure-data-centers-services-and-resources"></a>Was ist Azure? Rechenzentren, Dienste und Ressourcen

Der CEO von Microsoft, Satya Nadella, bezeichnet Azure häufig als „den Computer der Welt“. Ein Computer ist, wie Sie wissen, eine Sammlung von Hardware, die von einem Betriebssystem verwaltet wird und eine Plattform bietet, auf der Sie Software entwickeln können, mit der Sie die Rechenleistung des Systems für eine beliebige Anzahl von Aufgaben einsetzen können. (Aus diesem Grund verwenden wir das Wort „Anwendung“, um diese Software zu beschreiben.)

Im Fall von Azure ist die Hardware des Computers nicht ein einzelner Rechner, sondern ein riesiger Pool von virtualisierten Servercomputern, die in [Dutzenden von riesigen Rechenzentren auf der ganzen Welt](https://azure.microsoft.com/global-infrastructure/regions/) untergebracht sind. Das „Betriebssystem“ von Azure besteht aus *Diensten*, die verschiedene Teile dieses Ressourcenpools dynamisch zuordnen und freigeben, wenn sie von Anwendungen benötigt werden. Jede Zuordnung &mdash; sei es Rechenleistung (CPU-Kerne und Arbeitsspeicher), Speicher, Datenbanken, Netzwerke usw. &mdash; wird als „*Ressource*“ bezeichnet. Und jeder diskreten Ressource wird dementsprechend ein eindeutiger *Objektbezeichner* (eine GUID) und eine eindeutige URL zugewiesen.

![Ebenen von Azure, vom Rechenzentrum bis zu Azure-Diensten zum Zuordnen von Ressourcen](media/cloud-development/azure-layers.png)

Ressourcen sind die Bausteine einer Cloudanwendung. Der Cloudentwicklungsprozess beginnt daher mit der Erstellung der entsprechenden Umgebung, in der Sie die verschiedenen Teile der Anwendung bereitstellen können. Vereinfacht ausgedrückt, können Sie keinen Code und keine Daten in Azure bereitstellen, bevor Sie nicht eine geeignete Zielressource zugewiesen und konfiguriert (also *bereitgestellt*) haben, z. B. einen virtuellen Computer, eine Datenbank, ein Speicherkonto, eine Containerregistrierung, einen Containerorchestrator, einen Webhost, ein virtuelles Netzwerk, KI- und Analyse-Engines usw.

Der Prozess der Erstellung des Umfelds für Ihre Anwendung umfasst also die Identifizierung der relevanten Dienste und Ressourcentypen, die beteiligt sind, und die anschließende Bereitstellung dieser Ressourcen (ab diesem Zeitpunkt beginnen Sie, diese bei Azure zu mieten). Der Bereitstellungsprozess ist im Wesentlichen die Art und Weise, wie Sie das Computingsystem erstellen, in dem Sie Ihre Anwendung bereitstellen.

Ihnen stehen Hunderte verschiedener Arten von Ressourcen zur Verfügung, von grundlegenden „Infrastrukturressourcen“ wie virtuellen Computern, bei denen Sie die volle Kontrolle und Verantwortung für die von Ihnen bereitgestellte Software behalten, bis hin zu übergeordneten „Plattformdiensten“, die eine stärker verwaltete Umgebung bieten, in der Sie sich nur um Daten und Anwendungscode kümmern.

Es kann schwierig sein, die richtigen Dienste für Ihre Anwendung zu finden und die relativen Kosten auszugleichen. Dies ist aber auch ein Teil des kreativen Aspekts bei der Cloudentwicklung. Informationen zu den zahlreichen Optionen finden Sie im [Azure-Entwicklerhandbuch](/azure/guides/developer/azure-developer-guide). Als Nächstes soll erläutert werden, wie Sie tatsächlich mit all diesen Diensten und Ressourcen arbeiten.

> [!NOTE]
> Wahrscheinlich haben Sie die Begriffe „IaaS“ (Infrastructure-as-a-Service), „PaaS“ (Platform-as-a-Service) usw. schon einmal gehört und sind ihrer bereits überdrüssig. Der „as-a-Service“-Teil dieser Begriffe spiegelt die Tatsache wider, dass Sie im Allgemeinen keinen physischen Zugriff auf die Rechenzentren selbst besitzen. Stattdessen verwenden Sie Tools wie das Azure-Portal, die Azure CLI oder die REST-API von Azure, um „Infrastrukturressourcen“, „Plattformressourcen“ usw. bereitzustellen. Als „Dienst“ steht Azure immer zur Verfügung und wartet darauf, Ihre Anforderungen zu empfangen.
>
> In diesem Developer Center ersparen wir Ihnen den IaaS-, PaaS- und anderen Jargon, weil „as-a-Service“ der Cloud inhärent ist!

> [!NOTE]
> Eine „Hybridcloud“ bezieht sich auf die Kombination von privaten Computern und Rechenzentren mit Cloudressourcen wie Azure und bedarf eigener Überlegungen, die über das hinausgehen, was in den vorherigen Abschnitten behandelt wurde. Darüber wird bei diesem Aspekt von der Entwicklung neuer Anwendungen ausgegangen. Szenarien, die eine Umgestaltung der Architektur und Migration vorhandener lokaler Anwendungen beinhalten, werden hier nicht behandelt.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Bereitstellen und Verwalten von und Zugreifen auf Ressourcen >>>](cloud-development-provisioning.md)
