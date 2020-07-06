---
title: Wählen der Front-End-Entwicklungsplattform zum Erstellen von Clientanwendungen mit Visual Studio und Azure-Diensten
description: Erfahren Sie mehr über die unterstützten nativen und plattformübergreifenden Sprachen zum Erstellen von Clientanwendungen.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 8972c2b24334c964e00f5a3ccc27fa0c99e6f597
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632482"
---
# <a name="choose-a-mobile-development-framework"></a>Auswählen eines Frameworks für die mobile Entwicklung

Entwickler können clientseitige Technologien verwenden, um mobile Anwendungen selbst zu erstellen, wozu sie bestimmte Frameworks und Muster für einen plattformübergreifenden Ansatz verwenden. Basierend auf ihren Entscheidungsfaktoren können Entwickler Folgendes erstellen:

- Native Einzelplattformanwendungen unter Verwendung von Sprachen wie Objective-C und Java.
- Plattformübergreifende Anwendungen unter Verwendung von Xamarin, .NET und C#.
- Hybridanwendungen unter Verwendung von Cordova und seinen Varianten.

## <a name="native-platforms"></a>Native Plattformen

Das Entwickeln einer nativen Anwendung erfordert plattformspezifische Programmiersprachen, SDKs und Entwicklungsumgebungen sowie weitere Tools, die von Betriebssystemanbietern bereitgestellt werden.

### <a name="ios"></a>iOS

Von Apple erstellt und entwickelt, wird iOS verwendet, um Apps auf Apple-Geräten zu erstellen, insbesondere auf dem iPhone und iPad.

- **Programmiersprachen**: Objective-C, Swift
- **IDE:** Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android

Als von Google entwickeltes und weltweit beliebtestes Betriebssystem wird Android verwendet, um Anwendungen zu erstellen, die auf einer Vielzahl verschiedener Smartphones und Tablets ausgeführt werden können.

- **Programmiersprache**: Java, Kotlin 
- **IDE:** Android Studio und Android Developer Tools 
- **SDK:** Android-SDK

### <a name="windows"></a>Windows

- **Programmiersprache**: C#
- **IDE:** Visual Studio, Visual Studio Code
- **SDK:** Windows SDK

#### <a name="native-platform-pros"></a>Vorteile der nativen Plattform

- Hohe Benutzerfreundlichkeit
- Reaktionsschnelle Anwendungen mit hoher Leistung und der Möglichkeit, Schnittstellen zu nativen Bibliotheken zu nutzen
- Hochgradig sichere Anwendungen

#### <a name="native-platform-cons"></a>Nachteile der nativen Plattform

- Anwendung wird nur auf einer Plattform ausgeführt.
- Erstellen einer Anwendung erfordert mehr Entwicklerressourcen und ist teurer.
- Geringere Wiederverwendung von Code

## <a name="cross-platforms-and-hybrid-applications"></a>Plattformübergreifende und Hybridanwendungen

Plattformübergreifende Anwendungen bieten Ihnen die Möglichkeit, native mobile Anwendungen einmal zu schreiben, den Code für alle zu verwenden und sie unter iOS, Android und Windows auszuführen.

### <a name="xamarin"></a>Xamarin

Im Besitz von Microsoft, wird [Xamarin](https://visualstudio.microsoft.com/xamarin/) verwendet, um robuste, plattformübergreifende mobile Anwendungen in C# zu erstellen. Xamarin verfügt über eine Klassenbibliothek und Laufzeit, die auf vielen Plattformen wie iOS, Android und Windows funktionieren. Außerdem werden native (nicht interpretierte) Anwendungen kompiliert, die eine hohe Leistung bieten. Xamarin kombiniert alle Möglichkeiten der nativen Plattformen und fügt eine Reihe von eigenen leistungsstarken Features hinzu.

- **Programmiersprache**: C#
- **IDE:** Visual Studio unter Windows oder Mac

### <a name="react-native"></a>React Native

[React Native](https://facebook.github.io/react-native/), das 2015 von Facebook veröffentlicht wurde, ist ein [Open Source](https://github.com/facebook/react-native)-JavaScript-Framework zum Schreiben von echten, mit nativem Rendern arbeitenden mobilen Anwendungen für iOS und Android. Es basiert auf React, der JavaScript-Bibliothek von Facebook zum Erstellen von Benutzeroberflächen. Anstelle des Browsers werden in diesem Fall mobile Plattformen als Ziel verwendet. In React Native werden native Komponenten anstelle von Webkomponenten als Bausteine verwendet.

- **Programmiersprache**: JavaScript
- **IDE:** Visual Studio Code

### <a name="unity"></a>Unity

 Unity ist eine Engine, die zum Erstellen von Spielen optimiert ist. Sie können sie verwenden, um hochwertige 2D- oder 3D-Anwendungen mit C# für Plattformen wie Windows, iOS, Android und Xbox zu erstellen.

### <a name="cordova"></a>Cordova

Mit Cordova können Sie Hybridanwendungen erstellen, wozu Sie Visual Studio-Tools für Apache Cordova oder Visual Studio Code mit Erweiterungen für Cordova verwenden. Mit dem Hybridansatz können Sie Komponenten mit Websites teilen und webserverbasierte Anwendungen mit gehosteten Webanwendungen wiederverwenden, die auf Cordova basieren.

#### <a name="cross-platform-pros"></a>Vorteile einer plattformübergreifenden Lösung

- Verbesserte Codenutzbarkeit durch Erstellen einer Codebasis für mehrere Plattformen
- Für eine größere Zielgruppe über viele Plattformen
- Erheblich kürzere Entwicklungszeit
- Einfach zu starten und zu aktualisieren

#### <a name="cross-platform-cons"></a>Nachteile einer plattformübergreifenden Lösung

- Geringere Leistung
- Fehlende Flexibilität
- Jede Plattform hat einen einzigartigen Satz von Features und Funktionen, um die native Anwendung kreativer zu gestalten.
- Längere Entwicklungszeit für die Benutzeroberfläche
- Tooleinschränkung
