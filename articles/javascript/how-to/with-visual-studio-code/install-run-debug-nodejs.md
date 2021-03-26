---
title: Entwickeln von Node.js mit Visual Studio Code
description: Hier erfahren Sie die Schritte zum Entwickeln und Debuggen Ihres JavaScript Node.js-Projekts mit Visual Studio.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: b61bfaf9cc6b57a8a481e56841b584550e9a9b99
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118228"
---
# <a name="how-to-develop-and-debug-nodejs-with-visual-studio-code"></a>Entwickeln und Debuggen von Node.js mit Visual Studio Code

Hier erfahren Sie die Schritte zum Entwickeln und Debuggen Ihres JavaScript Node.js-Projekts mit Visual Studio. 

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

1. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/). 
1. Installieren Sie [git](https://git-scm.com/). Visual Studio Code integriert sich in git, um *Quellcodeverwaltung* in der [Seitenleiste](https://code.visualstudio.com/docs/getstarted/userinterface) bereitzustellen.

1. Rufen Sie eine mongoDB-Datenbank-Verbindungszeichenfolge ab.

    Wenn Sie nicht über eine mongoDB-Datenbank verfügen, können Sie folgende Aktionen ausführen:
    * Auswählen diese lokale Projekt in einer Konfiguration mit mehreren Containern auszuführen, von denen einer der Container eine mongoDB-Datenbank ist. Installieren der [Docker](https://www.docker.com/)- und [Remotecontainer](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)-Erweiterung, um eine Konfiguration mit mehreren Containern abzurufen, wobei in einem der Container eine lokale mongoDB-Datenbank ausgeführt wird. 
    * Wählen Sie die Erstellung einer [Azure Cosmos DB](/azure/cosmos-db/)-Ressource für eine MongoDB-Datenbank aus. Weitere Informationen finden Sie in diesem [Tutorial](../../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#create-a-cosmos-db-database-resource-for-mongodb).

## <a name="clone-sample-project-to-local-computer"></a>Klonen eines Beispielprojekts auf den lokalen Computer

Laden Sie als Erstes das Beispielprojekt herunter, indem Sie die folgenden Schritte ausführen:

1. Öffnen Sie Visual Studio Code.

1. Drücken Sie **F1**, um die Befehlspalette anzuzeigen.

1. Geben Sie an der Eingabeaufforderung der Befehlspalette `gitcl` ein, wählen Sie den Befehl **Git: Clone** aus, und drücken Sie die **EINGABETASTE**.

    ![Befehl „gitcl“ in der Eingabeaufforderung der Befehlspalette von Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-git-clone.png)

1. Geben Sie `https://github.com/scotch-io/node-todo` ein, wenn Sie zum Eingeben der **Repository-URL** aufgefordert werden, und drücken Sie anschließend die **EINGABETASTE**.

1. Wählen bzw. erstellen Sie das lokale Verzeichnis, in dem das Projekt geklont werden soll.

    ![Visual Studio Code-Explorer](../../media/node-howto-e2e/visual-studio-code-explorer.png)

## <a name="use-the-integrated-bash-terminal-to-install-dependencies"></a>Verwenden des integrierten bash-Terminals zum Installieren von Abhängigkeiten

Bei diesem Node.js-Projekt müssen Sie zunächst sicherstellen, dass alle Abhängigkeiten des Projekts über npm installiert werden.  

1. Drücken Sie **STRG**+ **`** (bzw. die entsprechende Tastenkombination für das deutsche Tastaturlayout), um das integrierte Terminal von Visual Studio Code anzuzeigen. 

1. Geben Sie `yarn` ein, und drücken Sie die **EINGABETASTE**.  

     ![Ausführen des Befehls „yarn“ in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-install-yarn.png)

## <a name="navigate-the-project-files-and-code"></a>Navigieren in den Projektdateien und dem Code

Zur besseren Orientierung in der Codebase probieren wir nun einige Beispiele für die Navigationsfunktionen von Visual Studio Code aus.

1. Drücken Sie **STRG**+**P**.

1. Geben Sie `.js` ein, um alle JavaScript/JSON-Dateien des Projekts mit dem jeweiligen übergeordneten Verzeichnis anzuzeigen. 

1. Wählen Sie *server.js* aus. Dies ist das Startskript für die App.

1. Bewegen Sie den Mauszeiger auf die Variable **database** (in Zeile 6 importiert), um ihren Typ anzuzeigen. Diese Möglichkeit zum schnellen Prüfen von Variablen, Modulen und Typen in einer Datei ist bei der Entwicklung Ihrer Projekte hilfreich. 

    ![Ermitteln des Typs in Visual Studio Code mit QuickInfo](../../media/node-howto-e2e/visual-studio-code-hover-help.png)

1. Wenn Sie mit der Maus in den Bereich einer Variablen – z.B. **database** – klicken, können Sie alle Verweise auf diese Variable anzeigen, die in der Datei enthalten sind. Um alle Verweise auf eine Variable für das gesamte Projekt anzuzeigen, klicken Sie mit der rechten Maustaste auf die Variable und wählen im Kontextmenü die Option **Alle Verweise suchen**.

    ![Ermitteln aller Verweise mit Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-find-all-references.png)

1. Zusätzlich zum Zeigen auf eine Variable mit der Maus, um ihren Typ zu ermitteln, können Sie auch die Definition einer Variablen untersuchen. Dies ist sogar möglich, wenn sie sich in einer anderen Datei befindet. Klicken Sie beispielsweise mit der rechten Maustaste auf **database.localUrl** (Zeile 12), und wählen Sie im Kontextmenü die Option **Definition einsehen** aus.

    ![Vorschau der Variablendefinition in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-peek-definition.png)

## <a name="use-visual-studio-code-autocompletion-with-mongodb"></a>Verwenden der AutoVervollständigen-Funktion von Visual Studio Code mit mongoDB

Die MongoDB-Verbindungszeichenfolge ist in der Deklaration der Eigenschaft `database.localUrl` hartcodiert. In diesem Abschnitt ändern Sie den Code, um die Verbindungszeichenfolge aus einer Umgebungsvariablen abzurufen, und machen sich mit dem AutoVervollständigen-Feature von Visual Studio Code vertraut.  

1. Öffnen Sie die Datei *server.js*.

1. Ersetzen Sie den folgenden Code:

    ```javascript
    mongoose.connect(database.localUrl);
    ```

    durch diesen Code:

    ```javascript
    mongoose.connect(process.env.MONGODB_URL || database.localUrl);
    ```

Bei der manuellen Eingabe des Codes (anstelle von Kopieren und Einfügen) werden in Visual Studio Code die verfügbaren Elemente für die globale API des Node.js-Prozesses angezeigt, wenn Sie den Punkt nach `process` eingeben.

![VS Code-Umgebungsvariablen mit „process“ und „env“](../../media/node-howto-e2e/visual-studio-code-process-env.png)

AutoVervollständigen funktioniert, weil von Visual Studio Code im Hintergrund TypeScript verwendet wird (auch für JavaScript). So werden Typinformationen angegeben, die dann während der Eingabe für die Vervollständigungsliste genutzt werden können. Visual Studio Code erkennt, dass es sich um ein Node.js-Projekt handelt, und die TypeScript-Typisierungsdatei für [Node.js wird automatisch von NPM heruntergeladen](https://www.npmjs.com/package/@types/node). Mit der Typisierungsdatei können Sie die automatische Vervollständigung auch für andere globale Node.js-Elemente, z. B. `Buffer` und `setTimeout`, sowie alle integrierten Module, etwa `fs` und `http`, nutzen.

Diese automatische Beschaffung von Typisierungen funktioniert nicht nur für die integrierten Node.js-APIs, sondern auch für mehr als 2.000 Drittanbietermodule, z. B. React, Underscore und Express. Um beispielsweise zu verhindern, dass die Beispiel-App für Mongoose abstürzt, wenn keine Verbindung mit der konfigurierten MongoDB-Datenbankinstanz hergestellt werden kann, können Sie in Zeile 13 die folgende Codezeile einfügen:

```javascript
mongoose.connection.on("error", () => { console.log("DB connection error"); });
```

Wie beim vorherigen Code auch, erhalten Sie die automatische Vervollständigung ohne jegliches Zutun.

![Automatische Anzeige der API-Elemente bei AutoVervollständigen](../../media/node-howto-e2e/visual-studio-code-autocomplete-mongoose.png)

Sie können anzeigen, welche Module die automatische Vervollständigung unterstützen, indem Sie das Projekt [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) durchsuchen. Dies ist die von der Community gestützte Quelle aller TypeScript-Typdefinitionen.

## <a name="running-the-local-nodejs-app"></a>Ausführen der lokalen Node.js-App

Nachdem Sie den Code untersucht haben, können Sie die App ausführen. Drücken Sie **F5**, um die App über Visual Studio Code auszuführen. Beim Ausführen des Codes mit **F5** (Debugmodus) startet Visual Studio Code die App und öffnet das Fenster **Debugging-Konsole**, in dem die StdOut-Daten für die App angezeigt werden.

![Überwachen der StdOut-Daten einer App über die Debugging-Konsole](../../media/node-howto-e2e/visual-studio-code-debug-console.png)

Die **Debugging-Konsole** wird auch an die neu ausgeführte App angefügt, sodass Sie JavaScript-Ausdrücke eingeben können, die in der App ausgewertet werden. Außerdem ist die automatische Vervollständigung verfügbar. Geben Sie in der Konsole `process.env` ein, um dieses Verhalten zu sehen:

![Eingeben von Code in der Debugging-Konsole](../../media/node-howto-e2e/visual-studio-code-debug-console-autocomplete.png)

Das Drücken von **F5** zum Ausführen der App war möglich, da die derzeit geöffnete Datei eine JavaScript-Datei ist (*server.js*). Visual Studio Code nimmt daher an, dass es sich bei dem Projekt um eine Node.js-App handelt. Wenn Sie alle JavaScript-Dateien in Visual Studio Code schließen und dann **F5** drücken, fragt Visual Studio Code die Umgebung ab:

![Angeben der Laufzeitumgebung](../../media/node-howto-e2e/visual-studio-code-select-environment.png)

Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:8080`, um die ausgeführte App anzuzeigen. Geben Sie eine Nachricht in das Textfeld ein, und fügen Sie einige Aufgaben hinzu (bzw. entfernen Sie welche), um ein Gefühl für die Funktionsweise der App zu erhalten.

![Hinzufügen oder Entfernen von Aufgaben mit der App](../../media/node-howto-e2e/add-remove-todos-app.png)

## <a name="debugging-the-local-nodejs-app"></a>Debuggen der lokalen Node.js-App

Sie können nicht nur die App ausführen und über die integrierte Konsole damit interagieren, sondern auch direkt im Code Breakpoints festlegen. Drücken Sie beispielsweise **STRG**+**P**, um die Dateiauswahl anzuzeigen. Geben Sie in der Dateiauswahl `route` ein, und wählen Sie die Datei *route.js* aus.

Legen Sie in Zeile 28 einen Breakpoint fest. Dieser Breakpoint steht für die Express-Route, die aufgerufen wird, wenn die App versucht, einen To-Do-Eintrag hinzuzufügen. Klicken Sie zum Festlegen eines Breakpoints im Editor einfach auf den Bereich links von der Zeilennummer. Dies ist in der folgenden Abbildung dargestellt.

![Festlegen eines Breakpoints in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-set-breakpoint.png)

> [!NOTE]
> Neben Standard-Breakpoints unterstützt Visual Studio Code auch bedingte Breakpoints, mit denen Sie anpassen können, wann die Ausführung der App angehalten werden soll. Klicken Sie zum Festlegen eines bedingten Breakpoints mit der rechten Maustaste auf den Bereich links von der Zeile, in der Sie die Ausführung anhalten möchten. Wählen Sie dann **Bedingten Haltepunkt hinzufügen...** aus, und geben Sie entweder einen JavaScript-Ausdruck (z. B. `foo = "bar"`) oder eine Ausführungsanzahl an, um die Bedingung zu definieren, unter der die Ausführung angehalten werden soll.

Wechseln Sie nach dem Festlegen des Breakpoints zurück zur ausgeführten App, und fügen Sie einen To-Do-Eintrag hinzu. Das Hinzufügen eines To-Do-Eintrags bewirkt sofort, dass die Ausführung der App in Zeile 28 angehalten wird, in der Sie den Breakpoint festgelegt haben:

![Unterbrechung der Visual Studio Code-Ausführung an einem Breakpoint](../../media/node-howto-e2e/visual-studio-code-pause-breakpoint-execution.png)

Nachdem die Anwendung angehalten wurde, können Sie den Mauszeiger auf die Ausdrücke des Codes bewegen, um ihren aktuellen Wert anzuzeigen, die lokalen Elemente bzw. Überwachungselemente und die Aufrufliste untersuchen und die Debug-Symbolleiste für den Schritt-für-Schritt-Durchlauf durch die Codeausführung verwenden. Drücken Sie **F5**, um die Ausführung der App fortzusetzen.

## <a name="local-full-stack-debugging-in-visual-studio-code"></a>Lokales Debuggen des vollständigen Stapels in Visual Studio Code

Wie in diesem Thema bereits erwähnt wurde, ist die To-Do-App eine MEAN-App. Dies bedeutet, dass sowohl ihr Front-End als auch ihr Back-End mit JavaScript geschrieben wurden. Derzeit debuggen Sie den Back-End-Code (Node/Express), aber es kann sein, dass Sie später auch den Front-End-Code (Angular) debuggen müssen. Hierfür verfügt Visual Studio Code über ein umfangreiches Ökosystem mit Erweiterungen, z.B. integriertes Chrome-Debugging.

Wechseln Sie zur Registerkarte **Erweiterungen**, und geben Sie im Suchfeld `chrome` ein:

![Erweiterung zum Debuggen von Chrome in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-chrome-extension.png)

Wählen Sie die Erweiterung mit dem Namen **Debugger for Chrome** aus, und wählen Sie anschließend die Option **Installieren**. Wählen Sie nach der Installation der Erweiterung für das Chrome-Debugging die Option **Erneut laden**, um Visual Studio Code zu schließen und wieder zu öffnen und die Erweiterung so zu aktivieren.

![Erneutes Laden von Visual Studio Code nach der Erweiterung für das Chrome-Debugging](../../media/node-howto-e2e/visual-studio-code-reload-extension.png)

Sie konnten den Node.js-Code ohne jegliche Visual Studio Code-spezifische Konfiguration ausführen und debuggen. Zum Debuggen einer Front-End-Web-App müssen Sie die Datei *launch.json* generieren, in der Visual Studio Code angewiesen wird, wie die App ausgeführt werden soll.

## <a name="create-a-full-stack-launchjson-file-for-visual-studio-code"></a>Erstellen einer Datei „launch.json“ für den vollständigen Stapel für Visual Studio Code

Wechseln Sie zum Generieren der Datei *launch.json* zur Registerkarte **Debuggen**, wählen Sie das Zahnradsymbol (das mit einem kleinen roten Punkt versehen sein sollte) aus, und wählen Sie die Umgebung **node.js** aus.

![Visual Studio Code-Option zum Konfigurieren der Datei „launch.json“](../../media/node-howto-e2e/visual-studio-code-debug-gear.png)

Nach der Erstellung sieht die Datei *launch.json* etwa wie unten angegeben aus. Hiermit wird Visual Studio Code mitgeteilt, wie die App gestartet bzw. wie das Anfügen für das Debuggen durchgeführt werden soll.

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch Program",
            "program": "${workspaceRoot}/server.js"
        },
        {
            "type": "node",
            "request": "attach",
            "name": "Attach to Port",
            "address": "localhost",
            "port": 5858
        }
    ]
}
```

Visual Studio Code hat erkannt, dass das Startskript der App *server.js* ist.

Wählen Sie bei geöffneter Datei *launch.json* die Option **Konfiguration hinzufügen** (unten rechts) und anschließend die Option **Chrome: Launch with userDataDir** (Chrome: Mit userDataDir starten).

![Hinzufügen einer Chrome-Konfiguration zu Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-add-chrome-config.png)

Wenn Sie eine neue Laufzeitkonfiguration für Chrome hinzufügen, können Sie den JavaScript-Code des Front-Ends debuggen. 

Sie können den Mauszeiger auf alle angegebenen Einstellungen bewegen, um Dokumentation zum Zweck der Einstellung anzuzeigen. Beachten Sie auch, dass Visual Studio Code die URL der App automatisch erkennt. Aktualisieren Sie die **webRoot**-Eigenschaft in `${workspaceRoot}/public`, damit der Chrome-Debugger weiß, wo sich die Front-End-Objekte der App befinden:

```json
{
   "type": "chrome",
   "request": "launch",
   "name": "Launch Chrome",
   "url": "http://localhost:8080",
   "webRoot": "${workspaceRoot}/public",
   "userDataDir": "${workspaceRoot}/.vscode/chrome"
}
```

Um das Front-End und Back-End gleichzeitig zu starten und zu debuggen, müssen Sie eine zusammengesetzte Laufzeitkonfiguration (*compound*) erstellen, damit Visual Studio Code weiß, welche Konfigurationssätze parallel ausgeführt werden sollen.

Fügen Sie den folgenden Codeausschnitt als Eigenschaft der obersten Ebene in der Datei *launch.json* hinzu (als gleichgeordnetes Element der vorhandenen **configurations**-Eigenschaft).

```json
"compounds": [
   {
      "name": "Full-Stack",
      "configurations": ["Launch Program", "Launch Chrome"]
   }
]
```

Die Zeichenfolgenwerte, die im Array **compounds.configurations** angegeben sind, verweisen auf den Namen (**name**) der einzelnen Einträge in der Liste mit den Konfigurationen (**configurations**). Wenn Sie diese Namen geändert haben, müssen Sie die entsprechenden Änderungen im Array vornehmen. Wechseln Sie hierfür zur Registerkarte „Debuggen“, und ändern Sie die ausgewählte Konfiguration in **Full-Stack** (Name der zusammengesetzten Konfiguration). Drücken Sie anschließend **F5**, um sie auszuführen.

![Ausführen einer Konfiguration in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-full-stack-configuration.png)

Durch die Ausführung der Konfiguration werden die Node.js-App (in der Ausgabe der Debugging-Konsole zu sehen) und Chrome (für die Navigation zur Node.js-App unter `http://localhost:8080`) gestartet.

Drücken Sie **STRG**+**P**, und geben Sie *todos.js* ein (bzw. wählen Sie die Datei aus). Dies ist der Angular-Hauptcontroller für das Front-End der App.

Legen Sie in Zeile 11 einen Breakpoint fest, der als Einstiegspunkt für einen neuen zu erstellenden To-Do-Eintrag dient.

Wechseln Sie zurück zur ausgeführten App, und fügen Sie einen neuen To-Do-Eintrag hinzu. Sie sehen, dass Visual Studio Code nun die Ausführung im Angular-Code angehalten hat.

![Debuggen des Front-End-Codes in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-chrome-pause.png)

Wie beim Debuggen von Node.js auch, können Sie den Mauszeiger auf Ausdrücke bewegen, lokale Elemente bzw. Überwachungselemente anzeigen, Ausdrücke in der Konsole auswerten usw. 

Beachten Sie zwei interessante Punkte:

1. Im Bereich **Aufrufliste** werden zwei unterschiedliche Stapel angezeigt: **Node** und **Chrome**, und es wird angezeigt, welcher aktuell angehalten ist.

1. Sie können zwischen dem Front-End- und Back-End-Code wechseln: Drücken Sie **F5**, um die Ausführung zu starten und den Breakpoint zu erreichen, den Sie zuvor in der Express-Route festgelegt haben.

Mit diesem Setup können Sie JavaScript-Code vom Typ Front, Back oder Full-Stack direkt in Visual Studio Code debuggen.

Darüber hinaus ist das Konzept des zusammengesetzten Debuggers nicht auf zwei Zielprozesse und auch nicht allein auf JavaScript beschränkt. Bei der Arbeit an einer Microservice-App (ggf. eine Polyglot-App) können Sie genau den gleichen Workflow verwenden (nachdem Sie die richtigen Erweiterungen für die Sprache bzw. das Framework installiert haben).

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Containern](../deploy-containers.md)
