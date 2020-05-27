---
title: Azure-Entwicklungsablauf
description: Eine Übersicht über den Cloudentwicklungszyklus in Azure, der die Bereitstellung, Codierung, Tests, Implementierung und Verwaltung umfasst.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 577b813e2b4ccd8d2cae3d7999d9bb959f88adc2
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404963"
---
# <a name="the-azure-development-flow-provision-code-test-deploy-and-manage"></a>Der Azure-Entwicklungsablauf: Bereitstellung, Codierung, Tests, Implementierung und Verwaltung

[Vorheriger Artikel: Bereitstellen und Verwalten von und Zugreifen auf Ressourcen](cloud-development-provisioning.md)

Nachdem Sie nun das Azure-Modell der Dienste und Ressourcen verstanden haben, können Sie den Gesamtablauf der Entwicklung von Cloudanwendungen mit Azure nachvollziehen: **Bereitstellung**, **Codierung**, **Tests**, **Implementierung** und **Verwaltung**.

## <a name="step-1-provision-and-configure-resources"></a>Schritt 1: Bereitstellen und Konfigurieren von Ressourcen

Wie im [vorherigen Artikel dieser Reihe](cloud-development-provisioning.md) beschrieben, besteht der erste Schritt bei der Entwicklung einer beliebigen Anwendung darin, die Ressourcen, die die Zielumgebung für Ihre Anwendung bilden, bereitzustellen und zu konfigurieren.

Die Bereitstellung beginnt mit dem Erstellen einer Ressourcengruppe in einer geeigneten Azure-Region. Sie können eine Ressourcengruppe über das Azure-Portal, über die Azure CLI oder mit einem benutzerdefinierten Skript erstellen, das das Azure SDK (oder die REST-API) verwendet.

Innerhalb dieser Ressourcengruppe stellen Sie dann die einzelnen benötigten Ressourcen bereit und konfigurieren sie, indem Sie das Portal, die CLI oder das Azure SDK verwenden. Die Konfiguration umfasst das Festlegen von Zugriffsrichtlinien, die steuern, welche Identitäten (Dienstprinzipale und/oder Anwendungs-IDs) auf diese Ressourcen zugreifen können.

Für die meisten Entwicklungsszenarien erstellen Sie wahrscheinlich Bereitstellungsskripts mit der Azure CLI und/oder Python-Code unter Verwendung des Azure SDK. Solche Skripts beschreiben die Gesamtheit des Ressourcenbedarfs Ihrer Anwendung und ermöglichen es Ihnen, diese Ressourcen in verschiedenen Entwicklungs-, Test- und Produktionsumgebungen einfach erneut zu erstellen (im Gegensatz zur manuellen Durchführung vieler wiederholter Schritte im Azure-Portal). Mit solchen Skripts wird außerdem die Bereitstellung einer Umgebung in einer anderen Region oder die Verwendung anderer Ressourcengruppen vereinfacht. Sie können diese Skripts auch in Quellcodeverwaltungsrepositorys verwalten, sodass Sie über einen vollständigen Überwachungs- und Änderungsverlauf verfügen.

## <a name="step-2-write-your-app-code-to-use-resources"></a>Schritt 2: Schreiben des App-Codes für die Verwendung von Ressourcen

Nachdem Sie die Ressourcen bereitgestellt haben, die Sie für Ihre Anwendung benötigen, schreiben Sie den Anwendungscode, um mit diesen Ressourcen zu arbeiten (ausgenommen der Ressourcen, für die Sie den Code selbst bereitstellen).

Beispielsweise haben Sie im Bereitstellungsschritt möglicherweise ein Azure-Speicherkonto erstellt, einen Blobcontainer in diesem Konto angelegt und Zugriffsrichtlinien für die Anwendung für diesen Container festgelegt. Aus Ihrem Code können Sie sich jetzt mit diesem Speicherkonto authentifizieren und dann Blobs in diesem Container erstellen, aktualisieren oder löschen. (Dieser Vorgang wird unter [Beispiel: Verwenden von Azure Storage](azure-sdk-example-storage.md) veranschaulicht.) Sie haben analog dazu möglicherweise auch eine Datenbank mit einem Schema und entsprechenden Berechtigungen bereitgestellt, damit der Anwendungscode eine Verbindung mit der Datenbank herstellen und die üblichen CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren und Löschen) ausführen kann.

Als Python-Entwickler schreiben Sie Ihren Anwendungscode in der Regel in Python mithilfe des Azure SDK für Python. Allerdings können alle unabhängigen Teile einer Cloudanwendung in einer beliebigen unterstützten Sprache geschrieben werden. Wenn Sie in einem Team mit einer Vielzahl von Sprachkenntnissen arbeiten, ist es beispielsweise durchaus möglich, dass einige Teile der Anwendung in Python geschrieben werden, einige in JavaScript, einige in Java und wieder andere in C#.

Beachten Sie, dass der Anwendungscode das Azure SDK bei Bedarf zum Ausführen von Bereitstellungs- und Verwaltungsvorgängen verwenden kann. Die Bereitstellung von Skripts kann ebenfalls das SDK zum Initialisieren von Ressourcen mit bestimmten Daten oder das Ausführen von Aufgaben für die Verwaltung von Cloudressourcen verwenden, auch wenn diese Skripts lokal ausgeführt werden.

## <a name="step-3-test-and-debug-your-app-code-locally"></a>Schritt 3: Lokales Testen und Debuggen des App-Codes

Entwickler testen Anwendungscode in der Regel gern auf ihren lokalen Arbeitsstationen, bevor sie diesen Code in der Cloud bereitstellen. Lokal zu testen bedeutet, dass Sie in der Regel auf andere Ressourcen zugreifen, die Sie bereits in der Cloud bereitgestellt haben, z. B. Speicher, Datenbanken usw. Der Unterschied besteht darin, dass Sie den App-Code selbst noch nicht innerhalb eines Clouddiensts ausführen.

Indem Sie den Code lokal ausführen, können Sie auch die Debugfunktionen von Tools wie Visual Studio Code nutzen und Ihren Code in einem Quellcodeverwaltungsrepository verwalten.

Sie müssen Ihren Code für lokale Tests überhaupt nicht ändern: Azure unterstützt die lokale Entwicklung und das Debuggen mit dem gleichen Code, den Sie in der Cloud bereitstellen. Umgebungsvariablen sind der Schlüssel: In der Cloud kann Ihr Code auf die Einstellungen der Hostingressource als Umgebungsvariablen zugreifen. Wenn Sie dieselben Umgebungsvariablen lokal erstellen, wird derselbe Code ohne Änderungen ausgeführt. Dieses Muster funktioniert für Authentifizierungsanmeldeinformationen, Ressourcen-URLs, Verbindungszeichenfolgen und eine beliebige Anzahl anderer Einstellungen, wodurch die Verwendung von Ressourcen in einer Entwicklungsumgebung beim lokalen Ausführen von Code und von Produktionsressourcen nach der Bereitstellung des Codes in der Cloud vereinfacht wird.

## <a name="step-4-deploy-your-app-code-to-azure"></a>Schritt 4: Bereitstellen des App-Codes in Azure

Sobald Sie Ihren Code lokal getestet haben, können Sie den Code in der Azure-Ressource bereitstellen, die Sie für das Hosting bereitgestellt haben. Wenn Sie z. B. eine Django-Webanwendung schreiben, stellen Sie den Code entweder auf einem virtuellen Computer (auf dem Sie Ihren eigenen Webserver bereitstellen) oder in Azure App Service (dort wird der Webserver für Sie bereitgestellt) bereit. Nach der Bereitstellung wird der Code auf dem Server statt auf Ihrem lokalen Computer ausgeführt und kann auf alle Azure-Ressourcen zugreifen, für die er autorisiert ist.

Wie bereits im vorherigen Abschnitt erwähnt, stellen Sie in typischen Entwicklungsprozessen zuerst Ihren Code für die Ressourcen bereit, die Sie in einer Entwicklungsumgebung bereitgestellt haben. Nach einer Reihe von Tests stellen Sie Ihren Code in Ressourcen in einer Stagingumgebung bereit, sodass die Anwendung für das Testteam und möglicherweise für Vorschaukunden verfügbar ist. Wenn Sie mit der Leistung der Anwendung zufrieden sind, können Sie den Code in Ihrer Produktionsumgebung bereitstellen. Alle diese Bereitstellungen können auch über Continuous Integration und Continuous Deployment mithilfe von Azure DevOps automatisiert werden.

Nachdem der Code in der Cloud bereitgestellt wurde, wird er jedoch unabhängig von der gewählten Vorgehensweise tatsächlich zu einer Cloudanwendung, die vollständig auf den Servercomputern in den Rechenzentren von Azure ausgeführt wird.

## <a name="step-5-manage-monitor-and-revise"></a>Schritt 5: Verwalten, überwachen und überarbeiten

Nach der Bereitstellung sollten Sie sicherstellen, dass die Anwendung wie gewünscht funktioniert, auf Kundenanforderungen reagiert und Ressourcen effizient (und zu den niedrigsten Kosten) nutzt. Sie können verwalten, wie Azure Ihre Bereitstellung nach Bedarf automatisch skaliert, und Sie können Leistungsdaten über das Azure-Portal, die Azure CLI oder benutzerdefinierte Skripts erfassen und überwachen, die mit dem Azure SDK geschrieben wurden. Sie können dann Anpassungen an Ihren bereitgestellten Ressourcen in Echtzeit vornehmen, um die Leistung zu optimieren, indem Sie die gleichen Tools verwenden.

Die Überwachung gibt Aufschluss darüber, wie Sie Ihre Cloudanwendung ggf. neu strukturieren können. Beispielsweise können Sie feststellen, dass bestimmte Teile einer Web-App (z. B. eine Gruppe von API-Endpunkten) nur gelegentlich im Vergleich zu den primären Teilen verwendet werden. Sie können diese APIs dann separat als serverlose Azure Functions-Elemente bereitstellen, wobei sie über eigene unterstützende Computeressourcen verfügen, die nicht mit der Hauptanwendung konkurrieren, aber nur Centbeträge pro Monat kosten. Ihre Hauptanwendung kann dann auf eine größere Anzahl von Kunden reagieren, ohne dass Sie sie in einen höheren Kostentarif hochskalieren müssen.

## <a name="next-steps"></a>Nächste Schritte

Sie sind jetzt mit der grundlegenden Struktur von Azure und dem allgemeinen Entwicklungsablauf vertraut: Bereitstellen von Ressourcen, Schreiben und Testen von Code, Bereitstellen des Codes in Azure und Überwachen und Verwalten dieser Ressourcen.

Der nächste Schritt besteht darin, die Arbeitsstation vollständig so zu konfigurieren, dass Sie mit diesem Ablauf funktioniert. Danach können Sie mit dem Azure SDK loslegen!

> [!div class="nextstepaction"]
> [Konfigurieren der lokalen Entwicklungsumgebung >>>](configure-local-development-environment.md)
