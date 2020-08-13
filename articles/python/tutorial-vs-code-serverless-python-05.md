---
title: 'Schritt 5: Bereitstellen von Azure Functions in Python mit VS Code'
description: 'Tutorialschritt 5: Bereitstellen von Python-Funktionscode in Azure, Streamen von Protokollen und Synchronisieren von Einstellungen zwischen einem lokalen Projekt und Azure'
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 950dda297f131f9ae9e2029f79cecae7455e0c07
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982852"
---
# <a name="5-deploy-azure-functions-in-python"></a>5: Bereitstellen von Azure Functions in Python

[Vorheriger Schritt: Lokales Debuggen](tutorial-vs-code-serverless-python-04.md)

In diesem Artikel verwenden Sie die Azure Functions-Erweiterung, um eine Funktions-App in Azure zusammen mit anderen erforderlichen Azure-Ressourcen zu erstellen. Sie können mit einer Funktionen-App Funktionen zu logischen Einheiten gruppieren. Dies erleichtert die Verwaltung, Bereitstellung und Freigabe von Ressourcen.

Außerdem erfordert eine Funktions-App ein Azure Storage-Konto für Daten und einen [Hostingplan](/azure/azure-functions/functions-scale#hosting-plan-support). Alle diese Ressourcen sind in einer einzelnen Ressourcengruppe angeordnet.

1. Wählen Sie im Bereich **Azure: Functions**-Explorer den Befehl **Für Funktions-App bereitstellen** aus, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie den Befehl **Azure Functions: Für Funktions-App** bereitstellen aus. Auch hier befindet sich die Funktions-App dort, wo Ihr Python-Projekt in Azure ausgeführt wird.

    ![Bereitstellen der Python-Funktion in einer Azure-Funktions-App](media/tutorial-vs-code-serverless-python/deploy-a-python-fuction-to-azure-function-app.png)

1. Wenn Sie dazu aufgefordert werden, wählen Sie **Neue Funktions-App in Azure erstellen** aus, und geben Sie einen Namen an, der in Azure eindeutig ist (in der Regel verwenden Sie Ihren persönlichen Namen oder Firmennamen zusammen mit anderen eindeutigen Bezeichnern; Sie können Buchstaben, Ziffern und Bindestriche verwenden). Wenn Sie zuvor eine Funktions-App erstellt haben, wird der zugehörige Name in dieser Liste von Optionen angezeigt.

1. Wählen Sie bei entsprechender Aufforderung eine Python-Version und einen Azure-Standort aus.

1. Die Erweiterung führt die folgenden Aktionen aus, die Sie in Visual Studio Code-Popupmeldungen und im Fenster **Ausgabe** beobachten können (der Vorgang dauert einige Minuten):

    - Erstellen Sie eine Ressourcengruppe mit dem von ihnen an dem von Ihnen ausgewählten Ort vergebenen Namen (ohne Bindestriche).
    - Erstellen Sie in dieser Ressourcengruppe das Speicherkonto, den Hostingplan und die Funktions-App. Standardmäßig wird ein [Verbrauchstarif](/azure/azure-functions/functions-scale#consumption-plan) erstellt. Um Ihre Funktionen in einem dedizierten Plan ausführen zu können, müssen Sie die [Veröffentlichung mit erweiterten Erstellungsoptionen aktivieren](/azure/azure-functions/functions-develop-vs-code).
    - Stellen Sie Ihren Code für die Funktions-App bereit.

    Der **Azure: Functions**-Explorer zeigt außerdem den Status an:

    ![Bereitstellungsstatusanzeige im Azure: Functions-Explorer](media/tutorial-vs-code-serverless-python/deployment-progress-indicator-in-azure-function-explorer.png)

1. Nach Abschluss der Bereitstellung zeigt die Azure Functions-Erweiterung eine Meldung mit Schaltflächen für drei weitere Aktionen an:

    ![Meldung über eine erfolgreiche Bereitstellung mit zusätzlichen Aktionen](media/tutorial-vs-code-serverless-python/azure-functions-deployment-success-with-additional-actions.png)

    Informationen zum **Streamen von Protokollen** und zu **Uploadeinstellungen** finden Sie in den nächsten Abschnitten.

1. Wählen Sie **Ausgaben anzeigen** aus, um zum Fenster **Ausgabe** zu wechseln. In der Ausgabe wird der öffentliche Endpunkt in Azure angezeigt (die URL Ihres spezifischen Endpunkts stimmt mit dem Namen überein, den Sie für die Funktions-App angegeben haben):

    <pre>
    HTTP Trigger Urls:

          HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    </pre>

    Verwenden Sie diesen Endpunkt, um die gleichen Tests auszuführen, die Sie lokal durchgeführt haben, und verwenden Sie dabei URL-Parameter und/oder Anforderungen mit JSON-Daten im Anforderungstext. Die Ergebnisse des öffentlichen Endpunkts sollten mit den Ergebnissen des lokalen Endpunkts, den Sie zuvor in [Teil 4](tutorial-vs-code-serverless-python-04.md) getestet haben, identisch sein.

## <a name="stream-logs"></a>Streaming von Protokollen

Die Unterstützung für das Streamen von Protokollen befindet sich zurzeit in der Entwicklung, wie in [Issue 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) (Problem 589) für die Azure Functions-Erweiterung beschrieben. Die Schaltfläche **Protokolle streamen** in der Bereitstellungspopupmeldung stellt schließlich die Verbindung der Protokollausgabe in Azure mit Visual Studio Code her. Sie können den Protokollstream auch im **Azure Functions**-Explorer starten und stoppen, indem Sie mit der rechten Maustaste auf das Functions-Projekt klicken und **Protokollstreaming starten** oder **Protokollstreaming stoppen** auswählen.

Derzeit sind diese Befehle jedoch noch nicht funktionstüchtig. Das Protokollstreaming ist stattdessen in einem Browser verfügbar, indem Sie den folgenden Befehl ausführen und dabei `<app_name>` durch den Namen Ihrer Functions-App in Azure ersetzen:

```
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

## <a name="sync-local-settings-to-azure"></a>Synchronisieren lokaler Einstellungen mit Azure

Die Schaltfläche **Uploadeinstellungen** in der Bereitstellungspopupmeldung wendet alle Änderungen, die Sie an Ihrer Datei *local.settings.json* vorgenommen haben, in Azure an. Sie können den Befehl auch im **Azure Functions**-Explorer aufrufen, indem Sie den Functions-Projektknoten erweitern, mit der rechten Maustaste auf **Anwendungseinstellungen** klicken und **Lokale Einstellungen hochladen** auswählen. Sie können auch die Befehlspalette verwenden, um den Befehl **Azure Functions: Lokale Einstellungen hochladen** auszuwählen.

Beim Hochladen von Einstellungen werden alle vorhandenen Einstellungen aktualisiert und neue, in *local.settings.json* definierte Einstellungen hinzugefügt. Beim Hochladen werden keine Einstellungen aus Azure entfernt, die nicht in der lokalen Datei aufgeführt sind. Um diese Einstellungen zu entfernen, erweitern Sie den Knoten **Anwendungseinstellungen** im **Azure Functions**-Explorer, klicken mit der rechten Maustaste auf die Einstellung und wählen **Einstellung löschen** aus. Sie können Einstellungen auch direkt im Azure-Portal bearbeiten.

Um alle Änderungen zu übernehmen, die Sie über das Portal oder den **Azure-Explorer** in der Datei *local.settings.json* vornehmen, klicken Sie mit der rechten Maustaste auf den Knoten **Anwendungseinstellungen** und wählen den Befehl **Download Remote Settings** (Remoteeinstellungen herunterladen) aus. Sie können auch die Befehlspalette verwenden, um den Befehl **Azure Functions: Remoteeinstellungen herunterladen** auszuwählen.

> [!div class="nextstepaction"]
> [Ich habe die Funktionen bereitgestellt: Fahren Sie mit Schritt 6 fort. >>>](tutorial-vs-code-serverless-python-06.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=05-deploy)
