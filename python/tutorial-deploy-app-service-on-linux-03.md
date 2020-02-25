---
title: 'Schritt 3: Erstellen der App Service-Instanz über Visual Studio Code'
description: 'Tutorialschritt 3: Erstellen der App Service-Instanz über die VS Code-Erweiterung'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: dea4ace359c8d072af8ab9ed33451bc8077249b7
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422465"
---
# <a name="3-create-the-app-service-from-visual-studio-code"></a>3: Erstellen der App Service-Instanz über Visual Studio Code

[Vorheriger Schritt: Vorbereiten der App](tutorial-deploy-app-service-on-linux-02.md)

In diesem Schritt erstellen Sie die Instanz von Azure App Service, in der Sie die App bereitstellen.

Führen Sie diesen Schritt aus, bevor Sie Ihren Code bereitstellen, damit Sie im nächsten Schritt ggf. eine benutzerdefinierte Startdatei konfigurieren können.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer den Befehl **+** aus, um eine neue App Service-Instanz zu erstellen, oder öffnen Sie die Befehlspalette (**F1**), und wählen Sie **Azure App Service: Neue Web-App erstellen** aus. (In der App Service-Terminologie ist eine „Web-App“ ein **Host** für Web-App-Code – nicht der App-Code selbst.)

    ![Erstellen einer neuen App Service-Instanz im App Service-Explorer](media/deploy-azure/create-new-app-service-in-app-service-explorer.png)

1. In den folgenden Eingabeaufforderungen:

    - Geben Sie einen Namen für Ihre App ein, der in App Service global eindeutig sein muss. Normalerweise verwenden Sie Ihren Namen oder den Firmennamen, gefolgt vom App-Namen.
    - Wählen Sie als Runtime **Python 3.7** aus.

1. Wenn in einer Meldung angezeigt wird, dass die neue App Service-Instanz erstellt wurde, wählen Sie **Ausgabe anzeigen** aus, um zum **Ausgabefenster** in VS Code zu wechseln. Die Ausgabe zeigt die Namen der erstellten Azure-Ressourcengruppe und des erstellten App Service-Plans sowie die URL für die App Service-Instanz an.

    ![URL, Ressourcengruppe und App Service Plan für Ihre App Service-Instanz](media/deploy-azure/url-for-your-new-app-service-and-resource-group-and-plan.png)

1. Um zu bestätigen, dass die App Service-Instanz ordnungsgemäß ausgeführt wird, erweitern Sie Ihr Abonnement im **Azure App Service**-Explorer, klicken mit der rechten Maustaste auf den App Service-Namen und wählen **Website durchsuchen** aus:

    ![Befehl „Website durchsuchen“ für eine App Service-Instanz im App Service-Explorer](media/deploy-azure/select-command-to-browse-website-in-app-service.png)

1. Da Sie noch keinen eigenen Code für die App Service-Instanz bereitgestellt haben (erfolgt im nächsten Schritt), wird nur eine Standard-App angezeigt:

    ![Standard-Python-App in App Service unter Linux](media/deploy-azure/default-python-app-on-app-service-on-linux.png)

## <a name="optional-upload-an-environment-variable-definitions-file"></a>(Optional:) Hochladen einer Umgebungsvariablen-Definitionsdatei

Wenn Sie über eine Umgebungsvariablen-Definitionsdatei verfügen, können Sie diese Datei auch verwenden, um die App Service-Umgebung zu konfigurieren. (Weitere Informationen zu solchen Dateien, die in der Regel die Erweiterung *.env* aufweisen, finden Sie unter [Visual Studio Code – Python-Umgebungen](https://code.visualstudio.com/docs/python/environments#environment-variable-definitions-file).)

1. Wählen Sie im Bereich **Azure: App Service** -Explorer den Knoten für die gewünschte App Service-Instanz aus, erweitern Sie ihn, klicken Sie anschließend mit der rechten Maustaste auf den Knoten **Anwendungseinstellungen**, und wählen Sie **Lokale Einstellungen von diesem Computer hochladen** aus.

1. VS Code fordert Sie auf, den Speicherort für die *ENV*-Datei anzugeben, und lädt sie dann in die App Service-Instanz hoch.

1. Nachdem der Upload abgeschlossen wurde, können Sie den Knoten **Anwendungseinstellungen** erweitern, um die einzelnen Werte anzuzeigen. Sie können sie auch im Azure-Portal anzeigen, indem Sie zur App Service-Instanz navigieren und **Konfiguration** auswählen.

1. Wenn Sie Einstellungen direkt im Azure-Portal festlegen, können Sie diese in einer Definitionsdatei speichern, indem Sie mit der rechten Maustaste auf den Knoten **Anwendungseinstellungen** klicken und **Download Remote Settings** (Remoteeinstellungen herunterladen) auswählen. Dadurch wird sichergestellt, dass diese Einstellungen auch in Ihrem Repository und nicht nur im Portal verfügbar sind.

> [!div class="nextstepaction"]
> [Ich habe die App Service-Instanz erstellt: Fahren Sie mit Schritt 4 fort. >>>](tutorial-deploy-app-service-on-linux-04.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=03-create-app-service)
