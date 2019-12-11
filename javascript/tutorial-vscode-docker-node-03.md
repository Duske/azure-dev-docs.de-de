---
title: Erstellen eines Containerimages für eine Node.js-App in Visual Studio Code
description: 'Teil 3 des Tutorials: Erstellen eines Node.js-Anwendungsimages'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: c3662c7d21359008bdc0cc5c3050fb2fdc7d6241
ms.sourcegitcommit: 9d0a6de18d9b5180c1cb485eff8e2774de48d225
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540517"
---
# <a name="create-your-nodejs-application-image"></a>Erstellen des Node.js-Anwendungsimages

[Vorheriger Schritt: Verwenden einer Containerregistrierung](tutorial-vscode-docker-node-02.md)

In diesem Schritt verwenden Sie die Docker-Erweiterung in Visual Studio Code, um die erforderlichen Dateien zum Erstellen eines Images für Ihre App hinzuzufügen, das Image zu erstellen und es in eine Registrierung zu pushen.

Falls Sie noch keine App für diese exemplarische Vorgehensweise haben, können Sie eine App aus dem [Node.js-Tutorial in Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial) verwenden.

## <a name="add-docker-files"></a>Hinzufügen von Docker-Dateien

1. Öffnen Sie in Visual Studio Code die **Befehlspalette** (**F1**), geben Sie `add docker files to workspace` ein, und wählen Sie dann den Befehl **Docker: Add Docker files to workspace** (Docker: Docker-Dateien dem Arbeitsbereich hinzufügen) aus.

1. Wählen Sie **Node.js** als Anwendungstyp aus, wenn Sie dazu aufgefordert werden, und wählen Sie dann den Port aus, an dem Ihre Anwendung lauscht.

1. Der Befehl erstellt eine `Dockerfile` und einige Konfigurationsdateien für Docker Compose sowie eine `.dockerignore`-Datei.

    > [!TIP]
    > VS Code bietet hervorragende Unterstützung für Docker-Dateien. Weitere Informationen zu den umfassenden Sprachfeatures wie intelligente Vorschläge, Vervollständigung und Fehlererkennung finden Sie in der VS Code-Dokumentation unter [Verwenden von Docker](https://code.visualstudio.com/docs/azure/docker).

## <a name="build-a-docker-image"></a>Erstellen eines Docker-Images

Die `Dockerfile` beschreibt die Umgebung für Ihre App, einschließlich des Speicherorts der Quelldateien und des Befehls zum Starten der App in einem Container.

> [!TIP]
> Container im Vergleich zu Images: Ein Container ist eine Instanz eines Images.

1. Öffnen Sie die **Befehlspalette** (**F1**), und führen Sie **Docker Images: Build Image** (Docker-Images: Image erstellen) aus, um das Image zu erstellen.

1. Wählen Sie bei der entsprechenden Eingabeaufforderung die gerade erstellte `Dockerfile` aus, und geben Sie dann einen Namen für das Image an. Der Name muss die Zielregistrierung oder das Docker Hub-Konto enthalten:

    `[registry or username]/[image name]:[tag]`

    In diesem Tutorial, in dem Azure Container Registry verwendet wird, lautet der Imagename wie folgt:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Wenn Sie Docker Hub verwenden, geben Sie Ihren Docker Hub-Benutzernamen an. Beispiel:

    `fiveisprime/myexpressapp:latest`

1. Danach wird der Bereich **Terminal** von Visual Studio Code geöffnet, um den Befehl `docker build` auszuführen. In der Ausgabe sehen Sie auch alle Schritte bzw. Ebenen, aus denen die App-Umgebung besteht.

1. Nachdem das Image erstellt wurde, wird es im **DOCKER**-Explorer unter **Images** angezeigt.

    ![Liste der Docker-Images in Visual Studio Code](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Pushen des Images in eine Registrierung

1. Öffnen Sie in Visual Studio Code die **Befehlspalette** (**F1**), führen Sie **Docker Images: Push** (Docker-Images: Pushen) aus, und wählen Sie dabei das Image aus, das Sie gerade erstellt haben. Im Bereich **Terminal** werden die für diesen Vorgang verwendeten `docker push`-Befehle angezeigt.

1. Erweitern Sie nach Abschluss des Vorgangs den Knoten **Images** im Docker-Erweiterungsexplorer, um das Image anzuzeigen.

    ![Anzeige des gepushten Images in Azure Container Registry](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Ich habe ein Image für meine Anwendung erstellt.](tutorial-vscode-docker-node-04.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
