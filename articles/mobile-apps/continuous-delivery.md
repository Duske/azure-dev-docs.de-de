---
title: Automatisieren des Bereitstellens und Veröffentlichens Ihrer mobilen Anwendungen mit Visual Studio App Center und Azure-Diensten
description: Erfahren Sie mehr über die Dienste, z. B. App Center, mit denen Sie eine Continuous Delivery-Pipeline für Ihre mobilen Anwendungen einrichten können.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 6cb18fed3db2ad46c96b7ff20684727aa2e35064
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699768"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatisieren des Bereitstellens und Veröffentlichens Ihrer mobilen Anwendungen mit Continuous Delivery-Diensten

Als Entwickler schreiben Sie Code und checken diesen in das Coderepository ein, aber die in das Repository eingecheckten Commits sind eventuell nicht immer konsistent. Wenn mehrere Entwickler am selben Projekt arbeiten, können Probleme mit der Integration auftreten. Teams können in Situationen geraten, in denen manches nicht funktioniert, Fehler akkumulieren und sich die Projektentwicklung verzögert. Entwickler müssen warten, bis der gesamte Softwarecode erstellt und getestet ist, um nach Fehlern zu suchen, was den Prozess langsam und weniger iterativ macht.

Mit Continuous Delivery automatisieren Sie die Bereitstellung und Veröffentlichung Ihrer mobilen Anwendungen. Es ist also egal, ob Sie die jeweilige Anwendung an eine Gruppe von Testern oder Mitarbeitern des Unternehmens (für Betatests) oder in einem App Store (für die Produktion) verteilen. Continuous Delivery macht Bereitstellungen weniger riskant und fördert schnelle Iterationen. Sie können außerdem beständig neue Änderungen für Ihre Kunden freigeben.

## <a name="distribute-application-binaries-to-beta-testers"></a>Verteilen von Anwendungsbinärdateien an Betatester

Das Ausführen des Betatests Ihrer mobilen Anwendung ist einer der entscheidenden Schritte während des Anwendungsentwicklungsprozesses. Es hilft Ihnen, frühzeitig Fehler und Probleme in Ihrer Anwendung zu finden. Das Feedback verbessert Ihre Anwendungsqualität, wenn Sie sie für den Einsatz in der Produktion vorbereiten.

Mit den folgenden Diensten können Sie eine Continuous Delivery-Pipeline in Ihren mobilen Apps ermöglichen.

### <a name="visual-studio-app-center-distribute"></a>Visual Studio App Center Distribute

[App Center Distribute](/appcenter/distribution/) ist ein Tool für Entwickler, um schnell Builds auf Geräten veröffentlichen zu können. Mit einer vollständigen Installationsportalumgebung ist App Center Distribute eine leistungsstarke Lösung für die Verteilung an Tester von Beta-Apps. Es stellt außerdem eine bequeme Alternative zur Verteilung über öffentliche App Stores dar. Entwickler können ihren Verteilungsworkflow mit App Center Build und der Integration in öffentliche App Stores noch weiter automatisieren.

#### <a name="visual-studio-app-center-distribute-features"></a>Features von Visual Studio App Center Distribute

- Verteilen Sie Ihre App an Betatester und Benutzer, und stellen Sie sicher, dass alle Tester die neueste Version Ihrer Anwendung haben.
- Benachrichtigen Sie Tester über neue Releases, ohne dass Tester den Downloadvorgang erneut durchlaufen müssen.
- Verwalten Sie Verteilergruppen für verschiedene Versionen Ihrer Anwendung.
- An Stores verteilen: 
  - [Apple](/appcenter/distribution/stores/apple)
  - [Google Play](/appcenter/distribution/stores/googleplay)
  - [Intune](/appcenter/distribution/stores/intune)
- Erhalten Sie Plattformunterstützung für iOS, Android, macOS, tvOS, Xamarin, React Native, Unity und Cordova.
- Registrieren Sie iOS-Geräte automatisch in Ihrem Bereitstellungsprofil.

#### <a name="visual-studio-app-center-distribute-references"></a>Visual Studio App Center-Referenzen

- [Registrieren bei Visual Studio App Center](https://appcenter.ms/signup)
- [Erste Schritte mit App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) ist ein umfassender Dienst für Continuous Integration (CI) und Continuous Delivery (CD), der mit Ihrem bevorzugten Git-Anbieter funktioniert. Azure Pipelines kann in den meisten wichtigen Clouddiensten wie Azure-Dienste bereitstellen. Sie können mit Ihrem Code auf GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud oder Azure Repos starten. Anschließend können Sie das Erstellen, Testen und Bereitstellen Ihres Codes für Microsoft Azure, Google Cloud Platform oder Amazon Web Services (AWS) automatisieren.

#### <a name="azure-pipelines-features"></a>Features von Azure Pipelines

- **Vereinfachte aufgabenbasierte Umgebung zum Einrichten eines CI-Servers:** Einrichten eines CI-Servers für native (Android, iOS und Windows) als auch plattformübergreifende (Xamarin, Cordova und React Native) mobile Anwendungen.
- **Alle Sprachen, Plattformen und Clouds:** Erstellen, Testen und Bereitstellen von Node.js-, Python-, Java-, PHP-, Ruby-, Go-, C/C++-, C#-, Android- und iOS-Apps. Nutzen Sie die parallele Ausführung unter Linux, macOS und Windows. Stellen Sie in Cloudanbietern wie Azure, AWS und Google Cloud Platform bereit. Verteilen Sie mobile Anwendungen über Betakanäle und App Stores.
- **Native Containerunterstützung:** Erstellen Sie neue Container im Handumdrehen, und stellen Sie diese in einer beliebigen Registrierung bereit. Stellen Sie Container auf unabhängigen Hosts oder Kubernetes bereit.
- **Erweiterte Workflows und Funktionen:** Problemloses Erstellen von Buildketten und mehrstufige Builds. Erhalten von Unterstützung für YAML, Testintegration, Releasegates, Berichterstellung und vieles mehr.
- **Erweiterbar:** Verwenden Sie eine Reihe von Build-, Test-und Bereitstellungsaufgaben, die von der Community erstellt wurden, was Hunderte von Erweiterungen von Slack bis SonarCloud umfasst. Sie haben sogar die Möglichkeit, aus anderen CI-Systemen, etwa Jenkins, bereitzustellen. Webhooks und REST-APIs können Sie bei der Integration unterstützen.
- **Kostenlose, in der Cloud gehostete Builds:** Diese Builds sind für öffentliche und private Repositorys verfügbar.
- **Unterstützung für die Bereitstellung bei anderen Cloudanbietern:** Zu den Anbietern zählen AWS und Google Cloud Platform.

#### <a name="azure-pipelines-references"></a>Azure Pipelines-Referenzen

- [Erste Schritte mit Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started)
- [Erste Schritte mit Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Verteilen Ihrer Anwendung direkt an App Stores

Nachdem Ihre Anwendung für die Verwendung in der Produktionsumgebung bereit ist und öffentlich verwendet werden soll, muss Sie an App Stores übermittelt werden, wo Sie von Kunden heruntergeladen werden kann. Es gibt mehrere Möglichkeiten, Ihre Anwendung direkt an App Stores zu verteilen. 

### <a name="visual-studio-app-center-distribute-stores"></a>Visual Studio App Center Distribute Stores

Mit [App Center Distribute](/appcenter/distribution/stores/) können Sie Ihre mobilen Anwendungen direkt in App Stores veröffentlichen. Nachdem Ihre Anwendung für den Download durch Benutzer bereit ist, können Sie Ihre Anwendungsbinärdateien direkt aus dem Visual Studio App Center-Portal veröffentlichen. 

Sie können direkt an folgende Ziele verteilen:

- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)

### <a name="apple-app-store"></a>Apple App Store

In dem von Apple entwickelten und betreuten App Store können Benutzer nach Anwendungen suchen, die für iOS-, MacOS-, WatchOS- oder tvOS-Geräte entwickelt wurden, und diese herunterladen. Entwickler müssen ihre iOS-Apps, wenn sie öffentlich verwendet werden sollen, an den Apple App Store übermitteln.

### <a name="google-play"></a>Google Play

Google Play ist der offizielle App Store für das Android-Betriebssystem, in dem Benutzer nach Anwendungen suchen können, die für Android-Geräte entwickelt und über Google veröffentlicht wurden, und diese herunterladen.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) ist ein cloudbasierter Dienst im Mobilitätsmanagementbereich für Unternehmen, der es Ihren Mitarbeitern ermöglicht, produktiv zu sein, während sie Ihre Unternehmensdaten geschützt halten. Mit Intune haben Sie folgende Möglichkeiten:

- Verwalten der mobilen Geräte, mit denen Ihre Mitarbeiter auf Unternehmensdaten zugreifen
- Verwalten der mobilen Anwendungen, die Ihre Mitarbeiter verwenden
- Schützen Ihrer Unternehmensdaten, indem die Art und Weise kontrolliert wird, wie Ihre Mitarbeiter auf die Daten zugreifen und diese freigeben
- Sicherstellen, dass Geräte und Anwendungen mit den Sicherheitsanforderungen des Unternehmens kompatibel sind.

## <a name="deploy-updates-directly-to-users-devices"></a>Bereitstellen von Updates direkt auf den Geräten der Benutzer

### <a name="codepush"></a>CodePush

Mit [CodePush](/appcenter/distribution/codepush/) können Entwickler in App Center, Apache Cordova und React Native Updates für mobile Anwendungen direkt auf den Geräten ihrer Benutzer bereitstellen. Es fungiert dazu als zentrales Repository, in dem Entwickler bestimmte Updates wie JavaScript-, HTML-, CSS- und Bildänderungen veröffentlichen können. Anwendungen können das Repository dann über die bereitgestellten Client-SDKs auf Updates abfragen. Auf diese Weise können Sie bei Ihren Benutzern ein stärker deterministisches und direktes Beteiligungsmodell etablieren, während Sie Fehler beheben oder kleine Funktionen hinzufügen. Es ist nicht erforderlich, dass Sie eine Binärdatei neu erstellen oder sie über öffentliche App Stores neu verteilen.

#### <a name="codepush-key-features"></a>Wichtige CodePush-Features

- Cordova- und React Native-Entwickler können Updates für mobile Anwendungen direkt auf den Geräten ihrer Benutzer bereitstellen, ohne eine Veröffentlichung über einen Store vorzunehmen.
- Dies ist nützlich für das Beheben von Fehlern oder Hinzufügen und Entfernen von kleinen Funktionen, für die kein erneutes Erstellen der Binärdateien und eine erneute Veröffentlichung über die entsprechenden Stores erforderlich ist.

#### <a name="codepush-references"></a>CodePush-Referenzen

- [Registrieren bei Visual Studio App Center](https://appcenter.ms/signup)
- [Erste Schritte mit CodePush in App Center](/appcenter/distribution/codepush/)
- [CodePush-CLI](/appcenter/distribution/codepush/cli)
