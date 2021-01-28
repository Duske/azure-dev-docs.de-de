---
title: Hosten des Quellcodes Ihrer mobilen Anwendung in der Cloud mit GitHub und Azure DevOps
description: Erfahren Sie mehr über die Dienste zum Hosten des Codes Ihrer mobilen Anwendungen mit Microsoft-Diensten.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 7a97aa687c80b4b49a566df2f5552bf919a0ec9e
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699748"
---
# <a name="cloud-hosted-source-code-management-services"></a>In der Cloud gehostete Quellcode-Verwaltungsdienste

Wenn Sie Entwicklungsteams haben, in denen mehrere Teammitglieder an derselben Codebasis arbeiten, müssen Sie einen geeigneten Speicherort zum Hosten Ihres Codes finden. Das Speichern der Daten in der Cloud und die Verwendung eines zentralisierten Repositorys ermöglicht allen Benutzern das Hochladen, Bearbeiten und Verwalten der Codedateien. Sie können auch mit anderen Entwicklern bei Projekten interagieren. Der Zugriff auf den Code ist unabhängig davon, wo Sie sich befinden, jederzeit möglich, solange Sie über eine Internetverbindung verfügen.

Cloudhosting gestaltet sich wesentlich einfacher als lokale Optionen. Es erfordert weniger Hardwarekonfiguration und versetzt Organisationen in die Lage, den Implementierungsprozess auf flexiblere Weise vorzunehmen.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Vorteile des Hostens von Quellcode in der Cloud

- **Zentralisierter Cloudspeicher:** Anzeigen und Verwalten Ihrer Daten von überall aus.
- **Bessere Zusammenarbeit und konsistenterer Code:** Nachverfolgen von Code innerhalb von Teams und Verwalten von Projekten, damit Continuous Delivery von exzellenter Software sichergestellt ist.
- **Einfachere Beteiligung:** Einfache Mitwirkung an Ihren Projekten.
- **Kürzerer Freigabezyklus:** Schnelleres Arbeiten in Ihren Teams und einfacheres Mitwirken an Ihren Projekten.
- **Verringern der Kosten:** Keine Gedanken mehr über die Wartung Ihrer eigenen Hardware, Server, VPN usw. machen.

Verwenden Sie die folgenden Dienste, um Ihre Anwendungsdaten in der Cloud zu hosten.

## <a name="github"></a>GitHub

[GitHub](https://github.com/) ist ein Open-Source-Repository-Hostingdienst, der Quellcodeprojekte in unterschiedlichen Programmiersprachen hostet. GitHub verfolgt die verschiedenen Änderungen nach, die bei jeder Iteration vorgenommenen werden.

### <a name="github-key-features"></a>Wichtige Features von GitHub

- Verwenden Sie Codehosting, um Ihren gesamten Code an einem Ort zu halten. Private, öffentliche und Open-Source-Repositorys sind alle mit Tools ausgestattet, mit denen Sie Code hosten, versionieren und freigeben können.
- Nutzen Sie Code Review und integrierte Reviewtools, um Code Review zu einem wesentlichen Bestandteil aller Prozesse in jedem Team zu machen:
  - Schützen Sie Branches, schlagen Sie Änderungen vor, und fordern Sie Überprüfungen an.
  - Entdecken Sie Unterschiede, Kommentare im Kontext, und erhalten Sie klares Feedback.
- Koordinieren Sie frühzeitig, halten Sie Ihre Planung ein, und schaffen Sie mehr mit den Projektverwaltungstools von GitHub:
  - Sehen Sie das große Bild des Projekts.
  - Verwenden Sie Task Boards, die sich unmittelbar neben Ihrem Code in GitHub befinden.
  - Ziehen Sie Karten, um Teammitgliedern Probleme oder Pull Requests zuzuweisen.
  - Legen Sie Meilensteine fest, um den Fortschritt zu organisieren und nachzuverfolgen.
  - Schreiben Sie Notizen, um Ideen zu erfassen, die nützlich sein könnten, aber nicht zu einem bestimmten Problem oder Pull Request gehören.
- Einfaches Erkennen und Auswählen der richtigen Tools für eine bessere Kommunikation und Automatisierung der Arbeit, indem Sie Anwendungen im [GitHub Marketplace](https://github.com/marketplace) kaufen.
- Verwalten und Erweitern Sie Teams durch: 
  - Benutzerrollen zum Organisieren von Teams und Zugriffsberechtigungen.
  - Diskussionsthreadtools, mit denen Konversationen zu Themen und in Teams fokussiert bleiben.
  - Communityrichtlinien zum schnellen Einrichten neuer Teammitglieder mit einem Konto.
- Durchsuchen und Favorisieren (Sternchen) beliebter Projekte, um diesen zu folgen.
- Netzwerken Sie, und lernen Sie von anderen in der Branche.

### <a name="github-references"></a>GitHub-Referenzen

- [GitHub](https://github.com/)
- [GitHub-Anleitungen](https://guides.github.com/)
- [GitHub Community Forum](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps

[Azure DevOps](https://azure.microsoft.com/services/devops/) unterstützt [Azure Repos](https://azure.microsoft.com/services/devops/repos/) und [Team Foundation-Versionskontrolle (TFVC)](/azure/devops/repos/tfvc/index) als Quellcodeverwaltungsoptionen. Azure DevOps hat unbegrenzt viele kostenlose private Repositorys mit kollaborativen Codeüberprüfungen (Code Reviews), erweiterte Dateiverwaltung, Codesuche und Branchrichtlinien, um qualitativ hochwertigen Code sicherzustellen. Azure Repos eignet sich hervorragend für kleine Projekte sowie für große Organisationen, die native Azure Active Directory-Unterstützung und erweiterte Richtlinien benötigen.

### <a name="azure-devops-features"></a>Features von Azure DevOps

- Verwenden Sie ein uneingeschränktes, in der Cloud gehostetes Git-Quellcoderepository für Ihre öffentlichen und privaten Repositorys:
  - Erhalten von Unterstützung für alle Git-Clients.
  - Verwenden von Webhooks und API-Integration.
- Starten Ihres nächsten Builds aus einem Repository-Pull Request:
  - Zusammenarbeit, um besseren Code zu erstellen, indem threadgeführte Diskussionen und Continuous Integration verwendet werden
  - Einrichten von Continuous Integration/Continuous Delivery (CI/CD), um automatisch Builds, Tests und Bereitstellungen mit jedem abgeschlossenen Pull Request auszulösen. Sie können Azure Pipelines oder ihre Tools verwenden.
  - Schutz der Codequalität durch Branchrichtlinien
- Behalten Sie eine zentrale Versionskontrolle mit Team Foundation-Versionskontrolle, die Code Review umfasst.
- Verbinden Sie sich mit Ihrem Code, indem Sie Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code und mehr verwenden.
- Verwenden Sie leistungsstarke semantische Codesuche.
- Profitieren Sie von den Vorteilen der für Unternehmen geeigneten Funktionen. Azure DevOps besitzt eine native Integration in Azure Active Directory, wodurch das Verwalten des Zugriffs auf Ihre Coderepositorys vereinfacht wird.
- Stellen Sie die Codequalität sicher mit Branchrichtlinien, wie etwa Mindestanzahl von Code Reviews, Anforderungen an erfolgreiche Builds und Erzwingen von Git-Zusammenführungsstrategien.
- Verbinden Sie Ihre bevorzugte Entwicklungsumgebung, um auf Repositorys zuzugreifen und Arbeit zu verwalten.

### <a name="azure-devops-references"></a>Azure DevOps-Referenzen

- [Erste Schritte mit Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentation zu Azure Repos](/azure/devops/repos)