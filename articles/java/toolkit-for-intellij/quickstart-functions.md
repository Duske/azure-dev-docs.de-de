---
title: Erstellen Ihrer ersten serverlosen Funktion mit Azure Functions per IntelliJ IDEA
description: Hier wird beschrieben, wie Sie mit dem Azure-Toolkit für IntelliJ eine einfache per HTTP ausgelöste Funktion in Azure erstellen und veröffentlichen.
ms.topic: quickstart
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: d796d6974d9591ae9df79d37c48f47d7e1cb5a72
ms.sourcegitcommit: 5541f993c01ce356e1b0eaa8f95aea9051c3c21e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278465"
---
# <a name="quickstart-create-an-azure-functions-project-using-intellij-idea"></a>Schnellstart: Erstellen eines Azure Functions-Projekts mit IntelliJ IDEA

In diesem Artikel verwenden Sie IntelliJ IDEA zum Erstellen einer Funktion, die auf HTTP-Anforderungen antwortet. Der Code wird lokal getestet und anschließend in der serverlosen Umgebung von Azure Functions bereitgestellt. Im Rahmen dieser Schnellstartanleitung fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

Vergewissern Sie sich zunähst, dass Folgendes vorhanden ist:

+ Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Ein von [Azure unterstütztes Java Development Kit (JDK)](../fundamentals/java-jdk-long-term-support.md) für Java 8
+ Eine installierte Instanz von [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) (Ultimate Edition oder Community Edition)
+ [Maven 3.5.0 oder höher](https://maven.apache.org/download.cgi)
+ Aktuelle Version von [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)

## <a name="installation-and-sign-in"></a>Installation und Anmeldung

1. Wählen Sie im Dialogfeld mit den Einstellungen/Voreinstellungen von IntelliJ IDEA (STRG+ALT+S) die Option **Plugins** (Plug-Ins) aus. Suchen Sie anschließend unter **Marketplace** nach **Azure Toolkit for IntelliJ** (Azure-Toolkit für IntelliJ), und klicken Sie auf **Install** (Installieren). Klicken Sie nach Abschluss der Installation auf **Restart** (Neu starten), um das Plug-In zu aktivieren. 

   ![Plug-In „Azure-Toolkit für IntelliJ“ im Marketplace][marketplace]

2. Öffnen Sie zur Anmeldung beim Azure-Konto Randleiste **Azure Explorer** , und klicken Sie dann auf die **Azure Sign In** in der Leiste oben auf (oder im Menü der IDEE **Tools/Azure/Azure-Anmeldung** ).

   ![IntelliJ-Befehl für Azure-Anmeldung][I01]

3. Wählen Sie im Fenster für **die Azure-Anmeldung** die Option **Device Login** (Geräteanmeldung) aus, und klicken Sie anschließend auf **Sign in** (Anmelden). Weitere Anmeldeoptionen finden Sie [hier](sign-in-instructions.md).

   ![Fenster für die Azure Anmeldung mit ausgewählter Geräteanmeldung][I02]

4. Klicken Sie im Dialogfeld **Azure Device Login** (Azure-Geräteanmeldung) auf **Copy&Open** (Kopieren und öffnen).

   ![Dialogfeld für Azure-Anmeldung][I03]

5. Fügen Sie im Browser Ihren Gerätecode ein, den Sie im vorherigen Schritt durch Klicken auf **Copy&Open** (Kopieren und öffnen) kopiert haben, und klicken Sie anschließend auf **Next** (Weiter).

   ![Der Browser für die Geräteanmeldung][I04]

6. Wenn das Dialogfeld **Select Subscriptions** (Abonnements auswählen) angezeigt wird, wählen Sie die Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **OK**.

   ![Dialogfeld zum Auswählen von Abonnements][I05]

## <a name="create-your-local-project"></a>Erstellen Ihres lokalen Projekts

In diesem Abschnitt wird mithilfe des Azure-Toolkits für IntelliJ ein lokales Azure Functions-Projekt erstellt. Weiter unten in diesem Artikel wird der Funktionscode in Azure veröffentlicht. 

1. Öffnen Sie das Willkommensdialogfeld von IntelliJ, wählen Sie *Neues Projekt erstellen* aus, um den Assistenten zum Erstellen eines neuen Projekts zu öffnen, und wählen Sie dann *Azure Functions* aus.

    ![Erstellen eines Functions-Projekts](media/quickstart-functions/create-functions-project.png)

1. Wählen Sie *HTTP-Trigger* aus, klicken Sie auf *Weiter* , und gehen Sie im Assistenten alle Konfigurationen auf den nachfolgenden Seiten durch. Bestätigen Sie den Projektspeicherort, und klicken Sie dann auf *Fertig stellen*. Ihr neues Projekt wird anschließend in Intellj IDEA geöffnet.

    ![Erstellen eines Functions-Projekts: Fertigstellen](media/quickstart-functions/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Lokales Ausführen der Funktions-App

1. Navigieren Sie zu `src/main/java/org/example/functions/HttpTriggerFunction.java`, um den generierten Code anzuzeigen. Neben Zeile *17* befindet sich die grüne Schaltfläche *Ausführen*. Klicken Sie darauf, und wählen Sie *Run 'azure-function-example'* („azure-function-example“ ausführen) aus. Daraufhin wird Ihre Funktions-App lokal mit einigen Protokollen ausgeführt.

    ![Lokales Ausführen der Funktions-App](media/quickstart-functions/local-run-functions-project.png)

    ![Lokales Ausführen der Funktions-App: Ausgabe](media/quickstart-functions/local-run-functions-output.png)

1. Sie können die Funktion ausprobieren, indem Sie über den Browser (etwa über `http://localhost:7071/api/HttpTrigger-Java?name=Azure`) auf den ausgegebenen Endpunkt zugreifen.

    ![Lokales Ausführen der Funktions-App: Testergebnis](media/quickstart-functions/local-run-functions-test.png)

1. Das Protokoll wird ebenfalls in IDEA ausgegeben. Beenden Sie nun die Funktion, indem Sie auf die Schaltfläche zum *Beenden* klicken.

    ![Lokales Ausführen der Funktions-App: Testprotokoll](media/quickstart-functions/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Lokales Debuggen der Funktions-App

1. Debuggen Sie Ihre Funktions-App nun lokal. Klicken Sie auf der Symbolleiste auf die Schaltfläche *Debuggen*. (Wird die Symbolleiste nicht angezeigt, klicken Sie zum Aktivieren der Symbolleiste auf *Ansicht > Darstellung > Symbolleiste*.)

    ![Schaltfläche zum lokalen Debuggen der Funktions-App](media/quickstart-functions/local-debug-functions-button.png)

1. Klicken Sie in der Datei `src/main/java/org/example/functions/HttpTriggerFunction.java` auf Zeile *20* , um einen Breakpoint hinzuzufügen, und greifen Sie erneut auf den Endpunkt `http://localhost:7071/api/HttpTrigger-Java?name=Azure` zu. Der Breakpoint wird erreicht, und Sie können weitere Debugfunktionen wie *step* , *watch* oder *evaluation* ausprobieren. Beenden Sie die Debugsitzung, indem Sie auf die Schaltfläche zum Beenden klicken.

    ![Lokales Debuggen der Funktions-App: Breakpoint](media/quickstart-functions/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Bereitstellen der Funktions-App in Azure

1. Klicken Sie im Projektexplorer von IntelliJ mit der rechten Maustaste auf Ihr Projekt, und wählen Sie *Azure > Deploy to Azure Functions* (In Azure Functions bereitstellen) aus.

    ![Bereitstellen von Funktionen in Azure](media/quickstart-functions/deploy-functions-to-azure.png)

1. Falls Sie noch keine Funktions-App besitzen, klicken Sie auf *No available function, click to create a new one* (Keine verfügbare Funktion. Zum Erstellen einer neuen Funktion klicken).

    ![Bereitstellen von Funktionen in Azure: Erstellen einer App](media/quickstart-functions/deploy-functions-create-app.png)

1. Geben Sie den Namen der Funktions-App ein, und wählen Sie die entsprechenden Angaben für das Abonnement, die Plattform, die Ressourcengruppe und den App Service-Plan aus. Sie können hier auch eine Ressourcengruppe oder einen App Service-Plan erstellen. Übernehmen Sie die App-Einstellungen, klicken Sie auf *OK* , und warten Sie einige Minuten, bis die neue Funktion erstellt wird. Die Statusleiste *Creating New Function App...* (Neue Funktions-App wird erstellt...) wird nicht mehr angezeigt.

    ![Bereitstellen von Funktionen in Azure: Assistent zum Erstellen von Apps](media/quickstart-functions/deploy-functions-create-app-wizard.png)

1. Wählen Sie die Funktions-App aus, in der die Bereitstellung vorgenommen werden soll. (Die von Ihnen soeben erstellte Funktions-App ist automatisch ausgewählt.) Klicken Sie zum Bereitstellen der Funktionen auf *Ausführen*.

    ![Bereitstellen von Funktionen in Azure: Ausführen](media/quickstart-functions/deploy-functions-run.png)

    ![Bereitstellen von Funktionen in Azure: Protokoll](media/quickstart-functions/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Verwalten von Azure Functions über IDEA

1. Sie können Ihre Funktionen mit *Azure Explorer* in IDEA verwalten. Klicken Sie auf *Funktions-App* , und alle Funktionen werden hier angezeigt.

    ![Anzeigen von Funktionen in Explorer](media/quickstart-functions/explorer-view-functions.png)

1. Klicken Sie zum Auswählen einer Funktion, und klicken Sie dann mit der rechten Maustaste darauf. Wählen Sie *Eigenschaften anzeigen* aus, um die Detailseite zu öffnen. 

    ![Anzeigen von Funktionseigenschaften](media/quickstart-functions/explorer-functions-show-properties.png)

1. Klicken Sie mit der rechten Maustaste auf die Funktion *HttpTrigger-Java* , und wählen Sie *Triggerfunktion* aus. Der Browser wird mit der Trigger-URL geöffnet.

    ![Bereitstellen von Funktionen in Azure: Ausführen](media/quickstart-functions/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Hinzufügen weiterer Funktionen zum Projekt

1. Klicken Sie mit der rechten Maustaste auf das Paket *org.example.functions* , und wählen Sie *Neu > Azure Function Class* (Azure-Funktionsklasse) aus. 

    ![Eintrag zum Hinzufügen von Funktionen zum Projekt](media/quickstart-functions/add-functions-entry.png)

1. Geben Sie im Assistenten zum Erstellen von Funktionsklassen den Klassennamen *HttpTest* ein, und wählen Sie *HttpTrigger* aus. Klicken Sie zum Erstellen auf *OK*. Auf diese Weise können Sie nach Bedarf neue Funktionen erstellen.

    ![Hinzufügen von Funktionen zum Projekt: Auswählen des Triggers](media/quickstart-functions/add-functions-trigger.png)
    
    ![Hinzufügen von Funktionen zum Projekt: Ausgabe](media/quickstart-functions/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Bereinigen von Functions

1. Löschen von Azure Functions in Azure Explorer
      
      ![Hinzufügen von Funktionen zum Projekt: Auswählen des Triggers](media/quickstart-functions/delete-function.png)
      

## <a name="next-steps"></a>Nächste Schritte

Sie haben ein Java-Funktionsprojekt mit einer per HTTP ausgelösten Funktion erstellt, auf Ihrem lokalen Computer ausgeführt und für Azure bereitgestellt. Erweitern Sie nun Ihre Funktion durch...

> [!div class="nextstepaction"]
> [Hinzufügen einer Azure Storage-Warteschlangen-Ausgabebindung](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/create-hello-world-web-app/marketplace.png
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png