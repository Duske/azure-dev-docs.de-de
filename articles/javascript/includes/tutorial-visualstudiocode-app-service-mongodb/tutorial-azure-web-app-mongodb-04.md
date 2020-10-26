---
title: include file tutorial-azure-web-app-mongodb-04.md
description: include file tutorial-azure-web-app-mongodb-04.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 73e629427359a625ea3ec4796c2ec7000a21536a
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183869"
---
In diesem Abschnitt des Tutorials fügen Sie Ihrer Anwendung mithilfe einer Visual Studio Code-Erweiterung und Docker-Containern eine lokale MongoDB-Datenbank hinzu.

## <a name="configure-visual-studio-code-to-run-containers"></a>Konfigurieren von Visual Studio Code für die Ausführung von Containern

In diesem Abschnitt konfigurieren Sie Ihre Entwicklungsumgebung für die Ausführung von zwei Containern: ein Container für Ihr Node.js-Projekt und ein Container für Ihren MongoDB-Container. Da in diesem Abschnitt Visual Studio Code Dev-Container verwendet werden, wird die Containerkonfiguration im Ordner **.devcontainer** gespeichert. Sie können dies an Ihre Quellcodeverwaltung übergeben, damit auch andere Mitglieder Ihres Teams Zugriff auf eine lokale MongoDB-Datenbank haben.  

1. Verwenden Sie in Visual Studio Code die Befehlspalette (STRG+UMSCHALT+P), um Folgendes auszuwählen: **Remotecontainer: Konfigurationsdateien für Entwicklungscontainer hinzufügen**. 

1. Wählen Sie **Node.js und Mongo DB** aus der Liste aus.

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-configure-development-container.png" alt-text="Partieller Screenshot der Befehlspalette von Visual Studio Code."::: 

1. Suchen Sie in der Datei **\.devcontainer\devcontainer.json** nach der **forwardPorts**-Eigenschaft, heben Sie die Kommentierung auf, und fügen Sie dem Array `8080` hinzu. Wenn Sie auch über die Shell auf den MongoDB-Container zugreifen möchten, fügen Sie dem Array den Port `27017` hinzu.  

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-dev-container-configuration-forward-ports.png" alt-text="Partieller Screenshot der Befehlspalette von Visual Studio Code."::: 

## <a name="run-web-app-locally-with-database"></a>Lokales Ausführen der Web-App mit der Datenbank

In diesem Abschnitt führen Sie Ihre Entwicklungsumgebung mit beiden Containern aus und zeigen die Website an. 

1. Wählen Sie in der unteren linken Ecke von Visual Studio Code das grüne Symbol **Remotecontainer** aus. Die Befehlspalette wird geöffnet. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/vscode-remote-container-icon.png" alt-text="Partieller Screenshot der Befehlspalette von Visual Studio Code."::: 

1. Wählen Sie in der Befehlspalette Folgendes aus: **Remotecontainer: In Container erneut öffnen**. Wenn Sie das Projekt zum ersten Mal mit Containern öffnen, werden die Node.js- und MongoDB-Images heruntergeladen und gestartet. Dies kann einige Minuten dauern. 

    Wenn die Container ausgeführt werden, zeigt das Visual Studio Code-Terminal das Terminal des Node.js-Containers an. 

    Optional können Sie den Befehl `ls` verwenden, um Ihre Dateien anzuzeigen. Beachten Sie, dass Ihre Dateien ein freigegebenes Volume auf dem lokalen Computer verwenden. Änderungen, die Sie im Node.js-Container an den Codedateien vornehmen, werden in Ihren lokalen Dateien gespeichert.

1. Starten Sie das Projekt im Terminal mit dem folgenden Befehl:

    ```console
    npm start
    ```

1. Öffnen Sie einen Browser mit Ihrer lokalen Web-App-URL:

    ```
    http://localhost:8080/
    ```

1. Geben Sie Daten in die Felder ein, und übermitteln Sie das Formular. Die Daten werden mit serverseitigem React-Rendering angezeigt. 

    :::image type="content" source="../../media/tutorial-end-to-end-app-cosmos/nodejs-app-connected-mongodb-form.png" alt-text="Partieller Screenshot der Befehlspalette von Visual Studio Code.":::

1. Wenn Sie mit der Untersuchung der App fertig sind, beenden Sie die Container mithilfe der Befehlspalette, indem Sie Folgendes auswählen: **Remotecontainer: Lokal erneut öffnen**. Dadurch werden die Container beendet, aber auf dem lokalen Computer belassen. 

## <a name="want-to-know-more"></a>Möchten Sie mehr erfahren? 

Stellen Sie mit einer Visual Studio Code-Erweiterung eine Verbindung mit dem MongoDB-Container her: **[MongoDB für VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode)** , um Ihre Daten anzuzeigen.

Wenn Sie lieber die **mongo**-Shell verwenden möchten, stellen Sie über ein Visual Studio Code-Terminal eine Verbindung mit dem MongoDB-Container her, indem Sie ein neues Visual Studio Code-Fenster öffnen und dann Folgendes verwenden: **Remotecontainer: An ausgeführten Container anfügen**. Wählen Sie dann den Container aus, der mit `-db` endet. Sobald das Fenster an den Container angefügt ist, öffnen Sie ein Visual Studio Code-Terminal. Mit dem folgenden Befehl können Sie sofort auf die Mongo-Shell zugreifen:

```console
mongo
```

Wenn Sie Ihre Container bereinigen möchten, verwenden Sie die Visual Studio Code-Erweiterung **[Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker)** .