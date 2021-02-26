---
title: 'Cloudentwicklung mit Azure: Was ist Azure?'
description: Eine Übersicht über die Entwicklung von Cloudanwendungen in Microsoft Azure, beginnend mit der Beziehung zwischen Rechenzentren, Diensten und Ressourcen.
ms.date: 02/16/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c6bfc9a9de48ab474b5bf99d5804a111ea10f62d
ms.sourcegitcommit: b882128a763f81dba83913bfff1e9cd1ec70818f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642296"
---
# <a name="cloud-development-on-azure"></a>Cloudentwicklung in Azure

Sie sind Python-Entwickler und bereit für die Entwicklung von Cloudanwendungen für Microsoft Azure. Um Sie bei der Vorbereitung auf eine lange und produktive Karriere zu unterstützen, werden Sie sich in dieser Reihe von drei Artikeln mit den grundlegenden Rahmenbedingungen der Cloudentwicklung in Azure vertraut machen.

## <a name="what-is-azure-data-centers-services-and-resources"></a>Was ist Azure? Rechenzentren, Dienste und Ressourcen

Der CEO von Microsoft, Satya Nadella, bezeichnet Azure häufig als „den Computer der Welt“. Wie Sie wissen, ist ein Computer eine Sammlung von Hardware, die von einem Betriebssystem verwaltet wird und eine Plattform bietet, auf der Sie Software entwickeln können, mit der Sie die Rechenleistung des Systems für eine beliebige Anzahl von Aufgaben *einsetzen* können. (Aus diesem Grund verwenden wir das Wort „Anwendung“, um diese Software zu beschreiben.)

Im Fall von Azure ist die Hardware des Computers nicht ein einzelner Rechner, sondern ein riesiger Pool von virtualisierten Servercomputern, die in [Dutzenden von riesigen Rechenzentren auf der ganzen Welt](https://azure.microsoft.com/global-infrastructure/regions/) untergebracht sind. Das „Betriebssystem“ von Azure besteht aus *Diensten*, die verschiedene Teile dieses Ressourcenpools dynamisch zuordnen und freigeben, wenn sie von Anwendungen benötigt werden. Mit diesen dynamischen Zuordnungen können Anwendungen schnell auf sich ändernde Bedingungen, z. B. die Kundennachfrage, reagieren.

Jede Zuordnung wird als *Ressource* bezeichnet, und jeder Ressource wird sowohl eine eindeutige *Objekt-ID* (eine GUID) als auch eine eindeutige URL zugewiesen. Zu den Ressourcentypen zählen virtuelle Computer (CPU-Kerne und Arbeitsspeicher), Speicher, Datenbanken, virtuelle Netzwerke, Containerregistrierungen, Containerorchestratoren, Webhosts, KI- und Analysemodule usw.

![Ebenen von Azure, vom Rechenzentrum bis zu Azure-Diensten zum Zuordnen von Ressourcen](media/cloud-development/azure-layers.png)

Ressourcen sind die Bausteine einer Cloudanwendung. Der Cloudentwicklungsprozess beginnt daher mit der Erstellung der entsprechenden Umgebung, in der Sie die verschiedenen Teile der Anwendung bereitstellen können. Einfach ausgedrückt: Sie können keinen Code oder keine Daten in Azure bereitstellen, bis Sie die entsprechenden Zielressourcen zugeordnet und konfiguriert – d. h. *bereitgestellt* – haben.

Der Prozess zum Erstellen der Umgebung für Ihre Anwendung umfasst also die Identifizierung der relevanten Dienste und Ressourcentypen, die beteiligt sind, und die anschließende Bereitstellung dieser Ressourcen. Der Bereitstellungsprozess ist im Wesentlichen die Art und Weise, wie Sie das Computingsystem erstellen, in dem Sie Ihre Anwendung bereitstellen. Die Bereitstellung ist auch der Zeitpunkt, ab dem Sie beginnen, diese Ressourcen bei Azure zu mieten.

Ihnen stehen Hunderte verschiedener Arten von Ressourcen zur Verfügung, von grundlegenden „Infrastrukturressourcen“ wie virtuellen Computern, bei denen Sie die volle Kontrolle und Verantwortung für die von Ihnen bereitgestellte Software behalten, bis hin zu übergeordneten „Plattformdiensten“, die eine stärker verwaltete Umgebung bieten, in der Sie sich nur um Daten und Anwendungscode kümmern.

Es kann schwierig sein, die richtigen Dienste für Ihre Anwendung zu finden und die relativen Kosten auszugleichen. Dies ist aber auch ein Teil des kreativen Aspekts bei der Cloudentwicklung. Informationen zu den zahlreichen Optionen finden Sie im [Azure-Entwicklerhandbuch](/azure/guides/developer/azure-developer-guide). Als Nächstes soll erläutert werden, wie Sie tatsächlich mit all diesen Diensten und Ressourcen arbeiten.

> [!NOTE]
> Wahrscheinlich haben Sie die Begriffe *IaaS* (Infrastructure-as-a-Service), *PaaS* (Platform-as-a-Service) usw. schon einmal gehört und sind ihrer bereits überdrüssig. Der Teil *as-a-Service* dieser Begriffe spiegelt die Tatsache wider, dass Sie im Allgemeinen keinen physischen Zugriff auf die Rechenzentren selbst besitzen. Stattdessen verwenden Sie Tools wie das Azure-Portal, die Azure CLI oder die REST-API von Azure, um *Infrastrukturressourcen*, *Plattformressourcen* usw. bereitzustellen. Als *Service* (Dienst) steht Azure immer zur Verfügung und wartet darauf, Ihre Anforderungen zu empfangen.
>
> In diesem Developer Center ersparen wir Ihnen den IaaS-, PaaS- und anderen Jargon, weil „as-a-Service“ der Cloud inhärent ist!

> [!NOTE]
> Eine *Hybrid Cloud* bezieht sich auf die Kombination von privaten Computern und Rechenzentren mit Cloudressourcen wie Azure. Hierfür gelten eigene Aspekte, die über das hinausgehen, was in den vorherigen Abschnitten behandelt wurde. Darüber wird bei diesem Aspekt von der Entwicklung neuer Anwendungen ausgegangen. Szenarien, die eine Umgestaltung der Architektur und Migration vorhandener lokaler Anwendungen beinhalten, werden hier nicht behandelt.

> [!NOTE]
> Es kann sein, dass Ihnen die Ausdrücke *cloudnativ* und *cloudfähig* für die Bezeichnung von Anwendungen begegnen. Diese Ausdrücke werden häufig synonym verwendet. Es gibt aber Unterschiede. Für eine cloudfähige Anwendung gilt häufig, dass sie im Ganzen aus einem lokalen Rechenzentrum zu cloudbasierten Servern migriert wird. Anwendungen dieser Art behalten oft ihre Struktur bei und werden lediglich auf virtuellen Computern in der Cloud (und somit übergreifend in mehreren geografischen Regionen) bereitgestellt. Bei dieser Migration kann die Anwendung skaliert werden, um den globalen Bedarf zu decken, ohne dass Sie in Ihrem eigenen Rechenzentrum neue Hardware bereitstellen müssen. Die Skalierung muss aber auf der Ebene des virtuellen Computers (bzw. der Infrastruktur) durchgeführt werden. Dies gilt auch, wenn die Leistung nur für einen Teil der Anwendung erhöht werden muss.
>
> Eine *cloudnative* Anwendung wird dagegen von Grund auf für die Nutzung der vielen unterschiedlichen Dienste geschrieben, die in der Cloud verfügbar sind und unabhängig skaliert werden können (z. B. Azure). Cloudnative Anwendungen weisen eine weniger starre Struktur auf (z. B. bei Verwendung von Microservicearchitekturen). Dies ermöglicht es Ihnen, die Bereitstellung und Skalierung für die einzelnen Teile präziser zu konfigurieren. Mit einer Struktur dieser Art wird die Wartung vereinfacht und häufig eine erhebliche Reduzierung der Kosten erzielt, da Sie nur für Premium-Dienste zahlen müssen, wenn Sie diese benötigen.
>
> Weitere Informationen finden Sie unter [Erstellen cloudnativer Anwendungen in Azure](https://azure.microsoft.com/overview/cloudnative/) und [Entwickeln cloudnativer .NET-Anwendungen für Azure](/dotnet/architecture/cloud-native/). Die in diesen Artikeln beschriebenen grundlegenden Vorgehensweisen gelten für Anwendungen, die in unterschiedlichen Sprachen geschrieben wurden.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Bereitstellen und Verwalten von und Zugreifen auf Ressourcen >>>](cloud-development-provisioning.md)
