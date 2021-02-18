---
title: Azure-Entwicklungsablauf
description: Eine Übersicht über den Cloudentwicklungszyklus in Azure, der die Bereitstellung, Codierung, Tests, Implementierung und Verwaltung umfasst.
ms.date: 02/16/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c2187f43da3159cc623e2ace671561ece2cf1758
ms.sourcegitcommit: b882128a763f81dba83913bfff1e9cd1ec70818f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642286"
---
# <a name="the-azure-development-flow-provision-code-test-deploy-and-manage"></a>Der Azure-Entwicklungsablauf: Bereitstellung, Codierung, Tests, Implementierung und Verwaltung

[Vorheriger Artikel: Bereitstellen und Verwalten von und Zugreifen auf Ressourcen](cloud-development-provisioning.md)

Nachdem Sie nun das Azure-Modell der Dienste und Ressourcen verstanden haben, können Sie den Gesamtablauf der Entwicklung von Cloudanwendungen mit Azure nachvollziehen: **Bereitstellung**, **Codierung**, **Tests**, **Implementierung** und **Verwaltung**.

| Schritt | Primäre Tools | activities |
| --- | --- | --- |
| Bereitstellen | Azure CLI, Azure-Portal, Cloud Shell, Python-Skripts mit Azure-Verwaltungsbibliotheken | Bereitstellen von Ressourcengruppen, Bereitstellen bestimmter Ressourcen in diesen Gruppen, Konfigurieren von Ressourcen für die Verwendung aus App-Code und/oder zum Empfangen von Python-Code in Bereitstellungen. |
| Code | Code-Editor (z. B. Visual Studio Code), Azure-Bibliotheken, Referenzdokumentation | Schreiben von Python-Code mithilfe der Azure-Clientbibliotheken, um mit bereitgestellten Ressourcen zu interagieren. |
| Test | Python-Runtime, Debugger | Lokales Ausführen von Python-Code für aktive Cloudressourcen (in der Regel für Entwicklungs- oder Testressourcen, nicht für Produktionsressourcen). Der Code selbst wird noch nicht in Azure gehostet, was Ihnen hilft, schnell zu debuggen und zu iterieren. |
| Bereitstellen | Azure CLI, GitHub, DevOps | Nachdem der Code lokal getestet wurde, stellen Sie ihn in einem geeigneten Azure-Hostingdienst bereit, in dem der Code selbst in der Cloud ausgeführt werden kann. Bereitgestellter Code wird normalerweise für Staging- oder Produktionsressourcen ausgeführt. |
| Verwalten | Azure CLI, Azure-Portal, Python-Skripts, Azure Monitor | Überwachen der Leistung und Reaktionsfähigkeit von Apps, Vornehmen von Anpassungen in der Produktionsumgebung, Rückmigration von Verbesserungen in die Entwicklungsumgebung für die nächste Bereitstellungs- und Entwicklungsrunde. |

## <a name="step-1-provision-and-configure-resources"></a>Schritt 1: Bereitstellen und Konfigurieren von Ressourcen

Wie im [vorherigen Artikel dieser Reihe](cloud-development-provisioning.md) beschrieben, besteht der erste Schritt bei der Entwicklung einer beliebigen Anwendung darin, die Ressourcen, die die Zielumgebung für Ihre Anwendung bilden, bereitzustellen und zu konfigurieren.

Die Bereitstellung beginnt mit dem Erstellen einer Ressourcengruppe in einer geeigneten Azure-Region. Sie können eine Ressourcengruppe über das Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mit einem benutzerdefinierten Skript erstellen, das die Azure-Bibliotheken (oder die REST-API) verwendet.

Innerhalb dieser Ressourcengruppe können Sie dann die einzelnen benötigten Ressourcen über das Portal, über die Befehlszeilenschnittstelle oder über die Azure-Bibliotheken bereitstellen und konfigurieren. (Eine Übersicht über die Typen verfügbarer Ressourcen finden Sie wieder im [Azure-Entwicklerhandbuch](/azure/guides/developer/azure-developer-guide).)

Die Konfiguration umfasst das Festlegen von Zugriffsrichtlinien, die steuern, welche Identitäten (Dienstprinzipale und/oder Anwendungs-IDs) auf diese Ressourcen zugreifen können. Zugriffsrichtlinien werden im Allgemeinen über die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](/azure/role-based-access-control/overview) verwaltet. Einige Dienste verfügen jedoch auch über spezifischere Zugriffssteuerungen. Als Cloudentwickler, der mit Azure arbeitet, sollten Sie sich mit der rollenbasierten Zugriffssteuerung vertraut machen, da Sie sie mit nahezu allen Ressourcen verwenden, bei denen Sicherheitsrisiken bestehen.

Für die meisten Anwendungsszenarien erstellen Sie in der Regel Bereitstellungsskripts mit der Azure CLI und/oder Python-Code unter Verwendung der Azure-Bibliotheken. Diese Skripts beschreiben die Gesamtheit des Ressourcenbedarfs Ihrer Anwendung (und definieren im Wesentlichen den benutzerdefinierten Cloudcomputer, auf dem Sie die Anwendung bereitstellen). Mit einem Skript können Sie auf einfache Weise denselben Satz von Ressourcen in verschiedenen Entwicklungs-, Test-, Staging- und Produktionsumgebungen erneut erstellen, anstatt viele wiederholte Schritte im Azure-Portal manuell auszuführen. Mit solchen Skripts wird außerdem die Bereitstellung einer Umgebung in einer anderen Region oder die Verwendung anderer Ressourcengruppen vereinfacht. Wenn Sie diese Skripts in Quellcodeverwaltungsrepositorys verwalten, verfügen Sie über einen vollständigen Überwachungs- und Änderungsverlauf.

## <a name="step-2-write-your-app-code-to-use-resources"></a>Schritt 2: Schreiben des App-Codes für die Verwendung von Ressourcen

Nachdem Sie die für Ihre Anwendung benötigten Ressourcen bereitgestellt haben, schreiben Sie den Anwendungscode, um mit den Laufzeitaspekten dieser Ressourcen zu arbeiten.

Beispielsweise haben Sie im Bereitstellungsschritt möglicherweise ein Azure-Speicherkonto erstellt, einen Blobcontainer in diesem Konto angelegt und Zugriffsrichtlinien für die Anwendung für diesen Container festgelegt. Dieser Bereitstellungsprozess wird unter [Beispiel: Bereitstellen von Azure Storage mit den Azure-Bibliotheken für Python](azure-sdk-example-storage.md) erläutert. Aus Ihrem Code können Sie sich dann mit diesem Speicherkonto authentifizieren und Blobs in diesem Container erstellen, aktualisieren oder löschen. Dieser Laufzeitprozess wird unter [Beispiel: Bereitstellen von Azure Storage mit den Azure-Bibliotheken für Python](azure-sdk-example-storage.md) veranschaulicht. Sie haben analog dazu möglicherweise auch eine Datenbank mit einem Schema und entsprechenden Berechtigungen bereitgestellt (wie unter [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Datenbank](azure-sdk-example-database.md) erläutert), damit der Anwendungscode eine Verbindung mit der Datenbank herstellen und die üblichen CRUD-Abfragen (Erstellen, Lesen, Aktualisieren und Löschen) ausführen kann.

App-Code verwendet in der Regel Umgebungsvariablen, um die Namen und URLs der zu verwendenden Ressourcen zu identifizieren. Mit Umgebungsvariablen können Sie problemlos zwischen Cloudumgebungen (Entwicklung, Test, Staging und Produktion) wechseln, ohne dass Änderungen am Code vorgenommen werden müssen. Die verschiedenen Azure-Dienste, die Anwendungscode hosten, bieten die Möglichkeit, die erforderlichen Variablen zu definieren. Beispielsweise definieren Sie in Azure App Service (zum Hosten von Web-Apps) und Azure Functions (serverloser Host von Azure) *Anwendungseinstellungen* über das Azure-Portal oder die Azure CLI, die dann als Umgebungsvariablen für den Code bereitgestellt werden.

Als Python-Entwickler schreiben Sie Ihren Anwendungscode wahrscheinlich in Python unter Verwendung der Azure-Bibliotheken für Python. Allerdings können alle unabhängigen Teile einer Cloudanwendung in einer beliebigen unterstützten Sprache geschrieben werden. Wenn Sie in einem Team mit einer Vielzahl von Sprachkenntnissen arbeiten, ist es beispielsweise durchaus möglich, dass einige Teile der Anwendung in Python geschrieben werden, einige in JavaScript, einige in Java und wieder andere in C#.

Die Azure-Bibliotheken können von Anwendungscode nach Bedarf zum Ausführen von Bereitstellungs- und Verwaltungsvorgängen verwendet werden. Analog dazu können die Bibliotheken von Bereitstellungsskripts verwendet werden, um Ressourcen mit bestimmten Daten zu initialisieren oder Aufgaben für die Verwaltung von Cloudressourcen auszuführen. Das gilt auch, wenn diese Skripts lokal ausgeführt werden.

## <a name="step-3-test-and-debug-your-app-code-locally"></a>Schritt 3: Lokales Testen und Debuggen des App-Codes

Entwickler testen Anwendungscode in der Regel gern auf ihren lokalen Arbeitsstationen, bevor sie diesen Code in der Cloud bereitstellen. Lokal zu testen bedeutet, dass Sie in der Regel auf andere Ressourcen zugreifen, die Sie bereits in der Cloud bereitgestellt haben, z. B. Speicher, Datenbanken usw. Der Unterschied besteht darin, dass Sie den App-Code selbst noch nicht innerhalb eines Clouddiensts ausführen.

Indem Sie den Code lokal ausführen, können Sie auch die Debugfunktionen von Tools wie Visual Studio Code nutzen und Ihren Code in einem Quellcodeverwaltungsrepository verwalten.

Sie müssen Ihren Code für lokale Tests überhaupt nicht ändern: Azure unterstützt die lokale Entwicklung und das Debuggen mit dem gleichen Code, den Sie in der Cloud bereitstellen. Umgebungsvariablen sind erneut der Schlüssel: In der Cloud kann Ihr Code auf die Einstellungen der Hostingressource als Umgebungsvariablen zugreifen. Wenn Sie dieselben Umgebungsvariablen lokal erstellen, wird derselbe Code ohne Änderungen ausgeführt. Dieses Muster funktioniert für Authentifizierungsanmeldeinformationen, Ressourcen-URLs, Verbindungszeichenfolgen und eine beliebige Anzahl anderer Einstellungen, wodurch die Verwendung von Ressourcen in einer Entwicklungsumgebung beim lokalen Ausführen von Code und von Produktionsressourcen nach der Bereitstellung des Codes in der Cloud vereinfacht wird.

## <a name="step-4-deploy-your-app-code-to-azure"></a>Schritt 4: Bereitstellen des App-Codes in Azure

Sobald Sie Ihren Code lokal getestet haben, können Sie den Code in der Azure-Ressource bereitstellen, die Sie für das Hosting bereitgestellt haben. Wenn Sie z. B. eine Django-Webanwendung schreiben, stellen Sie den Code entweder auf einem virtuellen Computer (auf dem Sie Ihren eigenen Webserver bereitstellen) oder in Azure App Service (dort wird der Webserver für Sie bereitgestellt) bereit. Nach der Bereitstellung wird der Code auf dem Server statt auf Ihrem lokalen Computer ausgeführt und kann auf alle Azure-Ressourcen zugreifen, für die er autorisiert ist.

Wie bereits im vorherigen Abschnitt erwähnt, stellen Sie in typischen Entwicklungsprozessen zuerst Ihren Code für die Ressourcen bereit, die Sie in einer Entwicklungsumgebung bereitgestellt haben. Nach einer Reihe von Tests stellen Sie Ihren Code in Ressourcen in einer Stagingumgebung bereit, sodass die Anwendung für das Testteam und möglicherweise für Vorschaukunden verfügbar ist. Wenn Sie mit der Leistung der Anwendung zufrieden sind, können Sie den Code in Ihrer Produktionsumgebung bereitstellen. Alle diese Bereitstellungen können auch über Continuous Integration und Continuous Deployment mithilfe von Azure DevOps automatisiert werden.

Nachdem der Code in der Cloud bereitgestellt wurde, wird er jedoch unabhängig von der gewählten Vorgehensweise tatsächlich zu einer Cloudanwendung, die vollständig auf den Servercomputern in den Rechenzentren von Azure ausgeführt wird.

## <a name="step-5-manage-monitor-and-revise"></a>Schritt 5: Verwalten, überwachen und überarbeiten

Nach der Bereitstellung sollten Sie sicherstellen, dass die Anwendung wie gewünscht funktioniert, auf Kundenanforderungen reagiert und Ressourcen effizient (und zu den niedrigsten Kosten) nutzt. Sie können verwalten, wie Azure Ihre Bereitstellung nach Bedarf automatisch skaliert, und Sie können Leistungsdaten über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder benutzerdefinierte Skripts erfassen und überwachen, die mit den Azure-Bibliotheken geschrieben wurden. Sie können dann Anpassungen an Ihren bereitgestellten Ressourcen in Echtzeit vornehmen, um die Leistung zu optimieren, indem Sie die gleichen Tools verwenden.

Die Überwachung gibt Aufschluss darüber, wie Sie Ihre Cloudanwendung ggf. neu strukturieren können. Beispielsweise können Sie feststellen, dass bestimmte Teile einer Web-App (z. B. eine Gruppe von API-Endpunkten) nur gelegentlich im Vergleich zu den primären Teilen verwendet werden. Sie können diese APIs dann separat als serverlose Azure Functions-Elemente bereitstellen, wobei sie über eigene unterstützende Computeressourcen verfügen, die nicht mit der Hauptanwendung konkurrieren, aber nur Centbeträge pro Monat kosten. Ihre Hauptanwendung kann dann auf eine größere Anzahl von Kunden reagieren, ohne dass Sie sie in einen höheren Kostentarif hochskalieren müssen.

## <a name="next-steps"></a>Nächste Schritte

Sie sind jetzt mit der grundlegenden Struktur von Azure und dem allgemeinen Entwicklungsablauf vertraut: Bereitstellen von Ressourcen, Schreiben und Testen von Code, Bereitstellen des Codes in Azure und Überwachen und Verwalten dieser Ressourcen.

Der nächste Schritt besteht darin, sich mit den Azure-Bibliotheken für Python vertraut zu machen, die Sie in vielen Teilen des Flows verwenden.

> [!div class="nextstepaction"]
> [Einführung in die Verwendung der Azure-Bibliotheken für Python >>>](azure-sdk-overview.md)
