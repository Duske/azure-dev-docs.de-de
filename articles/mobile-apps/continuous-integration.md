---
title: Automatisieren des Lebenszyklus Ihrer Apps mit Visual Studio App Center und Azure-Diensten
description: Erfahren Sie mehr über die Dienste wie App Center, mit denen Sie kontinuierliche Erstellung und Continuous Integration für Ihre mobilen Anwendungen einrichten können.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 82ce8246b916549d2207a10d96de89ad26cde49c
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699888"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisieren des Lebenszyklus Ihrer Apps mit kontinuierlicher Erstellung und Continuous Integration

Als Entwickler schreiben Sie Code und checken diesen in das Coderepository ein, aber die in das Repository eingecheckten Commits sind eventuell nicht immer konsistent. Wenn mehrere Entwickler am selben Projekt arbeiten, können Probleme mit der Integration auftreten. Teams können in Situationen geraten, in denen manches nicht funktioniert, Fehler akkumulieren und sich die Projektentwicklung verzögert. Entwickler müssen warten, bis der gesamte Softwarecode erstellt und getestet ist, um nach Fehlern zu suchen, was den Prozess langsam und weniger iterativ macht. 

Mit kontinuierlicher Erstellung und Continuous Integration können Entwickler die Builderstellung und das Testen ihres Codes vereinfachen, indem sie die Änderungen im Quellcoderepository committen und Tests und Überprüfungen in die Buildumgebung einbringen. Auf diese Weise führen sie Tests immer für ihren Code aus. Alle am Quellcode vorgenommenen Änderungen werden kontinuierlich in den Build übernommen, sobald sie im Repository committet wurden. Bei jedem Einchecken überprüft der CI-Server (Continuous Integration) alle vom Entwickler erstellten Tests und führt sie aus. Werden die Tests nicht bestanden, wird der Code für weitere Änderungen zurückgesendet. Auf diese Weise werden die erstellten Builds nicht von den Entwicklern unterbrochen. Außerdem müssen die Entwickler nicht alle Tests lokal auf ihren Computern ausführen, sodass ihre Produktivität gesteigert wird. 

## <a name="key-benefits"></a>Hauptvorteile

- Automatisieren von Builds, Tests und Bereitstellungen für Pipelines
- Frühzeitiges Erkennen von Fehlern und Beheben von Problemen, um kürzere Veröffentlichungsintervalle sicherzustellen
- Häufigeres Committen von Code und schnelles Erstellen von Anwendungen
- Flexibilität, Code schnell ohne Probleme zu ändern
- Kürzere Markteinführungszeit, sodass nur Code mit guter Qualität den gesamten Prozess durchläuft
- Höhere Effizienz kleiner Codeänderungen, da kleine Codeelemente gleichzeitig integriert werden
- Verbesserte Teamtransparenz und -verantwortlichkeit, sodass Sie kontinuierliches Feedback von Ihren Kunden und Ihrem Team erhalten

Mit den folgenden Diensten können Sie eine Continuous Integration-Pipeline in Ihren mobilen Apps aktivieren.

## <a name="visual-studio-app-center"></a>Visual Studio App Center

[App Center Build](/appcenter/build/) unterstützt Sie mithilfe einer sicheren Cloudinfrastruktur bei der Erstellung nativer und plattformübergreifender Anwendungen, an denen Ihr Team arbeitet. Sie können Ihr Repository problemlos in Visual Studio App Center verbinden und mit dem Aufbau Ihrer App in der Cloud bei jedem Commit beginnen. Sie müssen keine Buildserver lokal konfigurieren oder komplizierte Konfigurationen vornehmen, und es entsteht kein Code, der auf dem Computer eines Kollegen funktioniert, aber nicht auf Ihrem.

Dank der zusätzlichen Leistungsfähigkeit der Visual Studio App Center-Dienste können Sie Ihren Workflow weiter automatisieren. Mit der App Center-Verteilung können Sie Builds automatisch für Tester und öffentliche App-Stores freigeben. Mit App Center-Tests können Sie auch automatisierte Tests der Benutzeroberfläche unter Tausenden echten Geräte- und Betriebssystemkonfigurationen in der Cloud ausführen.

### <a name="visual-studio-app-center-features"></a>Features von Visual Studio App Center

- Einrichten von Continuous Integration in Minuten und häufigeres und schnelleres Erstellen von Anwendungen.
- Integration mit GitHub, Bitbucket, Azure DevOps und GitLab.
- Erstellen von schnellen und sicheren Builds auf verwalteten, in der Cloud gehosteten Computern.
- Ermöglichen des Startens von Tests für Ihre Builds und Überprüfen, ob die App auf echten iOS- und Android-Geräten erstellt werden kann.
- Native und plattformübergreifende Unterstützung für iOS, Android, macOS, Windows, Xamarin und React Native.
- Anpassen Ihrer Builds durch Hinzufügen von Skripts für nach dem Klonen, vor dem Buildvorgang und nach dem Buildvorgang.

### <a name="visual-studio-app-center-references"></a>Visual Studio App Center-Referenzen

- [Registrieren bei Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Erste Schritte mit App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines

 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), ein Dienst in Azure DevOps, ist ein umfassender Dienst für Continuous Integration (CI) und Continuous Delivery (CD), der mit Ihrem bevorzugten Git-Anbieter funktioniert. Er kann für die meisten wichtigen Clouddienste bereitgestellt werden, einschließlich Azure. Sie können mit Ihrem Code auf GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud oder Azure Repos starten. Anschließend können Sie das Erstellen, Testen und Bereitstellen Ihres Codes für Microsoft Azure, Google Cloud Platform oder Amazon Web Services (AWS) automatisieren.

### <a name="azure-pipelines-features"></a>Features von Azure Pipelines

- **Vereinfachte aufgabenbasierte Umgebung zum Einrichten eines CI-Servers:** Einrichten eines CI-Servers für native (Android, iOS und Windows) und plattformübergreifende (Xamarin, Cordova und React Native) mobile Anwendungen zusätzlich zu Servertechnologien von Microsoft und anderen Anbietern (Node.js, Java).
- **Alle Sprachen, Plattformen und Clouds:** Erstellen, Testen und Bereitstellen von Node.js-, Python-, Java-, PHP-, Ruby-, Go-, C/C++-, C#-, Android- und iOS-Anwendungen. Nutzen Sie die parallele Ausführung unter Linux, macOS und Windows. Stellen Sie in Cloudanbietern wie Azure, AWS und Google Cloud Platform bereit. Verteilen Sie mobile Anwendungen über Betakanäle und App Stores.
- **Native Containerunterstützung:** Erstellen Sie neue Container im Handumdrehen, und stellen Sie diese in einer beliebigen Registrierung bereit. Stellen Sie Container auf unabhängigen Hosts oder Kubernetes bereit.
- **Erweiterte Workflows:** Problemloses Erstellen von Buildketten und mehrstufige Builds. Erhalten von Unterstützung für YAML, Testintegration, Releasegates, Berichterstellung und vieles mehr.
- **Erweiterbar:** Verwenden Sie eine Reihe von Build-, Test-und Bereitstellungsaufgaben, die von der Community erstellt wurden, was Hunderte von Erweiterungen von Slack bis SonarCloud umfasst. Sie haben sogar die Möglichkeit, aus anderen CI-Systemen, etwa Jenkins, bereitzustellen. Webhooks und REST-APIs können Sie bei der Integration unterstützen.
- **Kostenlose, in der Cloud gehostete Builds:** Diese Builds sind für öffentliche und private Repositorys verfügbar.
- **Unterstützung für die Bereitstellung bei anderen Cloudanbietern:** Zu den Anbietern zählen AWS und Google Cloud Platform.

### <a name="azure-pipelines-references"></a>Azure Pipelines-Referenzen

- [Erste Schritte mit Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started)
- [Erste Schritte mit Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
- [Schnellstarts](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)

Zur Hilfestellung beim Auswählen des richtigen Diensts für Ihre Anwendungsbuilds lesen Sie den Artikel, in dem [App Center Build und Azure Pipelines](/appcenter/build/choose-between-services) verglichen werden.
