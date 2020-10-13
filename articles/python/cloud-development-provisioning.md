---
title: Bereitstellen und Verwalten von und Zugreifen auf Ressourcen in Azure
description: Hier finden Sie eine Übersicht über die Methoden zur Verwendung von Azure-Ressourcen. Dazu zählen das Azure-Portal, die Azure-Befehlszeilenschnittstelle und die Azure-Bibliotheken (SDK).
ms.date: 10/06/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0dd8de6c1d42ecbb77f34f48034cc9e4c43cd9e3
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764721"
---
# <a name="provisioning-accessing-and-managing-resources-on-azure"></a>Bereitstellen und Verwalten von und Zugreifen auf Ressourcen in Azure

[Vorheriger Artikel: Übersicht](cloud-development-overview.md)

Wie im vorherigen Artikel dieser Reihe beschrieben, besteht ein wesentlicher Bestandteil der Entwicklung einer Cloudanwendung darin, die erforderlichen Ressourcen in Azure bereitzustellen, mit denen Sie dann Ihren Code und Ihre Daten bereitstellen können. Das bedeutet Folgendes: Die Entwicklung einer Cloudanwendung beginnt mit der Entwicklung des Systems, das im Grunde der Zielcloudcomputer ist, auf dem Sie Ihre Anwendung bereitstellen. (Informationen zu den Typen verfügbarer Ressourcen finden Sie im [Azure-Entwicklerhandbuch](/azure/guides/developer/azure-developer-guide).)

Wie genau erfolgt diese Bereitstellung? Wie weisen Sie Azure an, Ressourcen für Ihre Anwendung zuzuordnen, und wie können Sie diese Ressourcen dann konfigurieren und anderweitig darauf zugreifen? Kurz gesagt: Wie kommunizieren Sie mit Azure, um all diese Ressourcen anzufordern und bereitzustellen?

## <a name="means-of-communicating-with-azure"></a>Mittel für die Kommunikation mit Azure

Die Antwort ist einfach. Wie bei den meisten Betriebssystemen können Sie mit Azure über drei Optionen kommunizieren: eine Benutzeroberfläche, eine Befehlszeilenschnittstelle (CLI) und eine API.

![Die verschiedenen Möglichkeiten der Kommunikation mit Azure zum Bereitstellen von Ressourcen](media/cloud-development/communication-with-azure.png)

Sie können beliebige oder alle dieser sich ergänzenden Methoden verwenden, um beliebige Azure-Ressourcen zu erstellen, zu konfigurieren und zu verwalten, die Sie benötigen. Tatsächlich verwenden Sie in der Regel alle drei Verfahren im Verlauf eines Entwicklungsprojekts, und es lohnt sich, sich mit jeder dieser Methoden vertraut zu machen.

Im Rahmen dieses Developer Centers liegt der Fokus auf der Verwendung der Befehlszeilenschnittstelle und des Python-Codes, von dem die Azure-Bibliotheken verwendet werden, da die Verwendung des Portals in der Dokumentation für die einzelnen Dienste ausführlich erläutert ist.

> [!NOTE]
> Die Azure-Bibliotheken für Python werden manchmal auch als Azure SDK für Python bezeichnet. Abgesehen von den Bibliotheken, die Sie über den Python-Paket-Manager (pip) erhalten, sind allerdings keine weiteren SDK-Komponenten vorhanden.

## <a name="azure-portal"></a>Azure-Portal

Das [Azure-Portal](https://portal.azure.com) ist die vollständig anpassbare, browserbasierte Benutzeroberfläche von Azure, über die Sie Ressourcen mit allen Azure-Diensten bereitstellen und verwalten können. Für den Zugriff auf das Portal müssen Sie sich zunächst mit einem Microsoft-Konto anmelden und dann ein kostenloses Azure-Konto mit einem Abonnement erstellen. (Nachdem Sie sich angemeldet haben, können Sie das Symbol **?** und dann **Interaktive Tour starten** auswählen, um eine einfache exemplarische Vorgehensweise für die wichtigsten Funktionen des Portals anzuzeigen.)

**Vorteile:** Die Benutzeroberfläche erleichtert das Durchsuchen von Diensten und Ermitteln ihrer verschiedenen Konfigurationsoptionen. Das Festlegen von Konfigurationswerten ist sicher, da keine Informationen auf der lokalen Arbeitsstation gespeichert werden.

**Nachteile:** Das Arbeiten mit dem Portal ist ein manueller Vorgang, der nicht einfach automatisiert werden kann. Um beispielsweise nachvollziehen zu können, welche Aktionen Sie zum Ändern einer Konfiguration ausgeführt haben, zeichnen Sie in der Regel die jeweiligen Schritte in einem separaten Dokument auf.

## <a name="azure-cli"></a>Azure CLI

Die [Azure CLI](/cli/azure/) ist die [Open-Source](https://github.com/Azure/azure-cli)-Befehlszeilenschnittstelle von Azure. Nachdem Sie sich bei der CLI angemeldet haben (mit dem Befehl `az login`), können Sie dieselben Aufgaben ausführen, die Sie auch über das Portal ausführen können.
  
**Vorteile:** Einfache Automatisierung durch Skripts und die Verarbeitung von Ausgaben. Bietet Befehle auf höherer Ebene, die mehrere Ressourcen gemeinsam für häufige Aufgaben bereitstellen, z. B. für das Bereitstellen einer Web-App. Skripts können in der Quellcodeverwaltung verwaltet werden.

**Nachteile:** Steilere Lernkurve als bei der Verwendung des Portals, und Befehle können zu Fehlern führen. Fehlermeldungen sind nicht immer hilfreich.

Sie können auch [Azure PowerShell](/powershell/) anstelle der Azure CLI verwenden, obwohl die Befehle der Azure CLI im Linux-Stil Python-Entwicklern in der Regel vertrauter sind.

Anstatt der lokalen CLI oder PowerShell können Sie Azure Cloud Shell direkt über [https://shell.azure.com/](https://shell.azure.com/) verwenden. Die Cloud Shell ist praktisch, da Sie automatisch bei Azure authentifiziert wird, nachdem Sie geöffnet wurde, und die gleichen Funktionen wie das Azure-Portal aufweist. Cloud Shell ist außerdem mit zahlreichen verschiedenen Tools vorkonfiguriert, deren lokale Installation insbesondere dann unpraktisch wäre, wenn Sie nur ein oder zwei Befehle ausführen müssen.

Da es sich bei Cloud Shell nicht um eine lokale Umgebung handelt, eignet sie sich eher für einzelne Vorgänge, wie Sie sie über das Portal ausführen würden, als für eine skriptbasierte Automatisierung. Dennoch können Sie in Cloud Shell Quellrepositorys (z. B. GitHub-Repositorys) klonen. Infolgedessen können Sie Automatisierungsskripts lokal entwickeln, in einem Repository speichern, das Repository in Cloud Shell klonen und dann dort ausführen.

## <a name="azure-rest-api-and-azure-libraries"></a>Azure-REST-API und Azure-Bibliotheken

Die [Azure-REST-API](/rest/api/?view=Azure&preserve-view=true) ist die programmgesteuerte Schnittstelle von Azure, die über Secure REST über HTTP bereitgestellt wird, da die Rechenzentren von Azure grundsätzlich mit dem Internet verbunden sind. Jeder Ressource wird eine eindeutige URL zugewiesen, die eine ressourcenspezifische API unterstützt. Dies gilt für strikte Authentifizierungsprotokolle und Zugriffsrichtlinien. (Das Azure-Portal und die Azure CLI erledigen ihre Aufgaben letztlich über die REST-API.)

Für Entwickler bieten die Azure-Bibliotheken (manchmal als Azure SDKs bezeichnet) sprachspezifische Bibliotheken, die die Funktionen der REST-API in weitaus praktischere Programmierparadigmen (z. B. Klassen und Objekte) übersetzen. Für Python installieren Sie immer einzelne Bibliotheken mit `pip install`, anstatt ein eigenständiges SDK als Ganzes zu installieren. (Informationen zu anderen Sprachen finden Sie unter [Downloads](https://azure.microsoft.com/downloads/).)

**Vorteile:** Genaue Kontrolle über alle Vorgänge, einschließlich einer viel direkteren Methode zur Verwendung von Ausgaben eines Vorgangs als Eingaben für einen anderen Vorgang im Vergleich zur Azure CLI. Python-Entwickler können mit vertrauten Sprachparadigmen arbeiten, anstatt die CLI zu verwenden. Kann auch aus Anwendungscode zum Automatisieren von detaillierten Verwaltungsszenarien verwendet werden.
  
**Nachteile:** Für Vorgänge, die mit einem CLI-Befehl durchgeführt werden können, sind in der Regel mehrere Codezeilen erforderlich, die fehlerhaft sein können. Es werden keine Vorgänge auf höherer Ebene wie mit der Azure CLI bereitgestellt.

## <a name="automatic-on-demand-provisioning"></a>Automatische bedarfsgesteuerte Bereitstellung

Viele Azure-Dienste ermöglichen es Ihnen, Skalierungsmerkmale so zu konfigurieren, dass sie variablen Anforderungen entsprechen. In diesem Fall kann Azure bei Bedarf automatisch zusätzliche Ressourcen bereitstellen und die Zuordnung nach Bedarf wieder aufheben. Eine solche automatische Skalierung ist einer der Hauptvorteile einer Cloudplattform, die durch die Ressourcen von mehreren Rechenzentren unterstützt wird. Anstatt Ihre Umgebung für Spitzenbedarf zu entwerfen und für Kapazitäten zu bezahlen, die Sie normalerweise nicht nutzen, können Sie die Umgebung für Baseline- oder durchschnittliche Nutzung entwerfen und für zusätzliche Funktionen nur bei Bedarf bezahlen.

Weitere Informationen finden Sie in Azure Architecture Center unter [Automatische Skalierung](/azure/architecture/best-practices/auto-scaling).

## <a name="subscriptions-resource-groups-and-regions"></a>Abonnements, Ressourcengruppen und Regionen

Sie können sich vorstellen, dass Sie im Ressourcenmodell von Azure im Laufe der Zeit viele verschiedene Ressourcen für zahlreiche Azure-Dienste für verschiedene Anwendungen bereitstellen. Es gibt drei Hierarchieebenen, die Sie verwenden können, um diese Ressourcen zu organisieren:

1. **Abonnements**: Jedes Azure-Abonnement verfügt über ein eigenes Abrechnungskonto und stellt häufig ein bestimmtes Team oder eine Abteilung innerhalb einer Organisation dar. Im Allgemeinen stellen Sie alle benötigten Ressourcen für eine bestimmte Anwendung innerhalb desselben Abonnements bereit, sodass sie von Features wie der freigegebenen Authentifizierung profitieren kann. Da jedoch über öffentliche URLs und die erforderlichen Autorisierungstoken auf alle Ressourcen zugegriffen werden kann, ist es sicherlich möglich, Ressourcen über mehrere Abonnements zu verteilen.

1. **Ressourcengruppen**: Innerhalb eines Abonnements sind Ressourcengruppen Container für andere Ressourcen, die Sie dann als eine Gruppe verwalten können. (Aus diesem Grund bezieht sich eine Ressourcengruppe in der Regel auf ein bestimmtes Projekt.) Jedes Mal, wenn Sie eine Ressource bereitstellen, müssen Sie in der Tat die Gruppe angeben, zu der sie gehört. Der erste Schritt bei einem neuen Projekt besteht in der Regel darin, eine geeignete Ressourcengruppe zu erstellen. Wenn Sie die Ressourcengruppe löschen, heben Sie die Zuordnung aller enthaltenen Ressourcen auf, anstatt jede Ressource einzeln löschen zu müssen. Vertrauen Sie uns, wenn wir sagen, dass die Vernachlässigung der Organisation Ihrer Ressourcengruppen später zu vielen Schwierigkeiten führen kann, wenn Sie nicht mehr wissen, welche Ressource zu welchem Projekt gehört!

1. **Benennen von Ressourcen**: innerhalb einer Ressourcengruppe können Sie dann die gewünschten Benennungsstrategien verwenden, um Gemeinsamkeiten oder Beziehungen zwischen Ressourcen auszudrücken. Da der Name häufig in der URL der Ressource verwendet wird, gibt es möglicherweise Einschränkungen hinsichtlich der Zeichen, die Sie verwenden können. (Einige Namen lassen z. B. nur Buchstaben und Ziffern zu, wohingegen in anderen Bindestriche und Unterstriche zulässig sind.)

Während der Arbeit mit Azure entwickeln Sie Ihre eigenen Präferenzen für die Organisation Ihrer Ressourcen und Ihre eigenen Konventionen für die Benennung von Abonnements, Ressourcengruppen und einzelnen Ressourcen.

### <a name="regions-and-geographies"></a>Regionen und geografische Regionen

Ein wichtiges Merkmal einer Ressourcengruppe besteht darin, dass Sie immer einem bestimmten Azure-*Region* zugeordnet ist. Dies ist der Standort des jeweiligen Rechenzentrums. Alle Ressourcen in derselben Gruppe befinden sich gemeinsam in diesem Rechenzentrum und können so viel effizienter interagieren, als wenn sie sich in verschiedenen Regionen befinden würden. Entwickler wählen häufig Regionen aus, die ihren Kunden am nächsten liegen, und optimieren so die Reaktionsfähigkeit einer Anwendung. Azure bietet auch Features für die Georeplikation, um Kopien Ihrer Anwendung und Datenbanken in mehreren Regionen zu synchronisieren, sodass Sie eine globale Kundenbasis besser bedienen können.

Aufgrund lokaler Gesetze und Vorschriften, die durch die *Geografie* bestimmt werden, in der Sie ein Abonnement erstellen, haben Sie möglicherweise nur Zugang zu bestimmten Regionen, und diese Regionen unterstützen möglicherweise nicht alle Azure-Dienste. Weitere Informationen finden Sie unter [Globale Azure-Infrastruktur](https://azure.microsoft.com/global-infrastructure/).

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Azure-Entwicklungsablauf >>>](cloud-development-flow.md)
