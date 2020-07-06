---
title: Grundlegendes zur Verwendung von mobilen Anwendungen und zum Benutzerverhalten mit Visual Studio App Center und Azure-Diensten
description: Erfahren Sie mehr über die Dienste wie App Center, die Ihnen helfen, intelligente Geschäftsentscheidungen zu treffen, indem sie aufbereiten, wie Benutzer Ihre mobile Anwendung nutzen.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 744e41200a5f7e5ff898e7a0786a4284de176b7a
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632592"
---
# <a name="analyze-and-understand-mobile-application-use"></a>Analysieren und Verstehen der Verwendung mobiler Anwendungen

Wie gut erfassen Sie, wie Ihre Benutzer ihre Anwendungen verwenden? Wie viele Benutzer nutzen Ihre Anwendung aktive, und wie ändert sich die Nutzung im Laufe der Zeit? Welche Features nutzen sie, und welche werden am häufigsten verwendet? Wo befinden sich diese Benutzer? Wie viele Benutzer verwenden die neueste Version der Anwendung? Für alle diese Fragen ist die jeweilige Antwort wichtig, damit Ihre App in ein erfolgreiches Unternehmen mündet. Um diese Arten von Fragen zur Nutzungsanalyse zu beantworten, müssen Sie Nutzungsdaten aus Ihren Apps sammeln.

Je tiefergehend Sie die Daten auswerten, desto mehr können Sie Wege finden, Ihre Anwendung zu verbessern und Ihre Benutzer zufriedenzustellen. Es ist unverzichtbar, anhand der entsprechenden Daten aussagekräftige Erkenntnisse zu gewinnen und die Benutzer zu überzeugen.

## <a name="importance-of-analytics"></a>Wichtigkeit der Analyse

- Verstehen Sie Ihre Benutzer, verstehen Sie, wie die Benutzer mit Ihrer Anwendung interagieren und was die Benutzer zur Rückkehr veranlasst. Auf dieser Basis können Sie Ihre Anwendung optimieren und ein hervorragendes Erlebnis bieten, um Ihr Geschäft auszubauen.
- Verfolgen Sie Ihre Nutzungsmetriken, um fundierte Entscheidungen darüber zu treffen, wie Sie Ihre Anwendung vermarkten und Ihre Kunden besser bedienen können.
- Messen Sie die Leistung Ihrer Anwendung.
- Erfahren Sie, welche Teile Ihrer Anwendung zur Wert- und Leistungssteigerung beitragen.
- Erhalten Sie Zugriff auf datengesteuerte Erkenntnisse zu Problemen bezüglich Kundenabwanderung und Kundenbindung.

Verwenden Sie die folgenden Dienste, um die Analyse mobiler Anwendungen zu ermöglichen.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

Mit [App Center Analytics](/appcenter/analytics/) können Sie Ihr Zielpublikum vergrößern, indem Sie sich auf die wichtigen Aspekte konzentrieren. Sie profitieren von tiefgreifenden Berichten und Erkenntnissen über Benutzersitzungen, am häufigsten verwendete Geräte, Betriebssystemversionen und Verhaltensanalyse. Erstellen Sie auf einfache Weise benutzerdefinierte Ereignisse, um alles mit umfassenden Anwendungsanalysen nachzuverfolgen.

### <a name="visual-studio-app-center-features"></a>Features von Visual Studio App Center

- Verfolgen Sie ohne zusätzliche Kosten Nutzungsmuster, Benutzerakzeptanz und andere Metriken zur Kundenbindung.
- Identifizieren Sie Trends, das Benutzerverhalten und die Kundenbindung mithilfe benutzerdefinierter Ereignisse.
- Sie erhalten standardmäßige Metriken und ausführliche Erkenntnisse zur (täglichen, wöchentlichen, monatlichen) Anwendungsnutzung, Sitzungen, Geräteeigenschaften und Benutzerdemografien gesammelt in einem einzigen Dashboard.
- Exportieren Sie all Ihre App Center-Analysedaten zur unbegrenzten Speicherung fortlaufend nach Azure. App Center Analytics unterstützt den Export nach Azure Blob Storage und Azure Application Insights.
- Durch die Integration mit Azure Application Insights erhalten Sie noch tiefere Erkenntnisse, beispielsweise zur Kundenbindung, Trichteranalysen und Kohorten.
- Verwenden Sie die einzeilige SDK-Integration, um innerhalb weniger Minuten zu starten.
- Erhalten Sie Plattformunterstützung für iOS, Android, macOS, tvOS, Xamarin, React Native, Unity und Cordova.

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center-Referenzen

- [Registrieren für App Center](https://appcenter.ms/signup)
- [Erste Schritte mit App Center Analytics](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor

Azure Monitor umfasst [Application Insights](/azure/azure-monitor/app/app-insights-overview). Dieser Dienst stellt Tools für das Erfassen und Analysieren von Telemetriedaten bereit, damit Sie die Leistung Ihrer mobilen Anwendung optimieren und überwachen können. Sie können sich Application Insights zunutze machen, indem Sie App Center Analytics zum Einrichten des Exports nach Application Insights verwenden. Application Insights kann die Telemetriedaten zu benutzerdefinierten Ereignissen aus Ihren Anwendungen abfragen, segmentieren, filtern und analysieren. Diese Funktionen reichen über diejenigen in App Center hinaus.

### <a name="azure-monitor-features"></a>Features von Azure Monitor

- Abfragen der Telemetriedaten zu benutzerdefinierten Ereignissen
- Filtern von ereignisbezogenen Telemetriedaten mit leistungsstarken Segmentierungsfunktionen
- Analysieren von Wechsel-, Bindungs- und Navigationsmustern in Ihrer Anwendung Verwenden Sie Folgendes:
  - Trichter zum Analysieren und Überwachen der Konversionsraten
  - Vermerkdauer zum Analysieren der fortlaufenden Bindung von Benutzern an Ihre Anwendung
  - Arbeitsmappen zum Kombinieren von Visualisierungen und Text in einen teilbaren Bericht
  - Kohorten zum Benennen und Speichern spezifischer Benutzer- oder Ereignisgruppen, damit auf diese ganz leicht von anderen Analysetools verwiesen werden kann

### <a name="azure-monitor-references"></a>Azure Monitor-Referenzen

- [Azure portal](https://portal.azure.com/)
- [Analysieren Ihrer mobilen Apps mit App Center und Application Insights](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Verwenden von App Center Analytics mit Application Insights](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab

[Azure PlayFab](https://playfab.com/) stellt eine vollständige Back-End-Plattform mit Spielediensten, Echtzeitanalysen und LiveOps bereit, die Sie zum Erstellen von erstklassigen über die Cloud verfügbaren Spielen benötigen. Diese Dienste helfen Spieleentwicklern dabei, die Hürden beim Einstieg zu überwinden. Sowohl kleinen als auch großen Studios werden somit kosteneffiziente Entwicklungslösungen geboten, die mit ihren Spielen skalieren. Sie unterstützen Studios bei der Einbindung, Beibehaltung und Monetarisierung ihrer Spieler. PlayFab versetzt Entwickler in die Lage, die intelligente Cloud zu verwenden, um Spiele zu entwickeln und zu betreiben, Spieledaten zu analysieren und Spielerlebnisse insgesamt zu verbessern.

### <a name="azure-playfab-features"></a>Features von Azure PlayFab

- Überwachen von Echtzeitdashboards
- Auswerten der Leistung Ihres Spiels über Hauptmetriken
- Überprüfen von Zusammenfassungen der täglichen und monatlichen Leistung Ihres Spiels mithilfe automatisch generierter Berichte. Diese Berichte können Sie im Game Manager anzeigen und herunterladen oder sogar täglich an Ihren Posteingang senden lassen.
- A/B-Tests zum Durchführen von Experimenten und Ermitteln der optimalen Einstellung für eine bestimmte Variable
- Segmentierung für Spieler zum Definieren automatisierter Spielergruppen

### <a name="azure-playfab-references"></a>Azure PlayFab-Referenzen

- [PlayFab-Portal](https://developer.playfab.com/en-US/sign-up)
- [Analyse](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Schnellstarts](/gaming/playfab/#pivot=documentation&panel=quickstarts)
