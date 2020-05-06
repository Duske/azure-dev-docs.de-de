---
title: Erstellen eines Containerimages für eine Node.js-App in Visual Studio Code
description: 'Teil 4 des Tutorials: Erstellen eines Node.js-Anwendungsimages'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: a8659edb4d0b3664c7704fd0bedde0c274562f3c
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80740688"
---
# <a name="create-your-nodejs-application-image"></a>Erstellen des Node.js-Anwendungsimages

[Vorheriger Schritt: Erstellen und Ausführen einer lokalen Node.js-App](tutorial-vscode-docker-node-03.md)

## <a name="add-docker-files"></a>Hinzufügen von Docker-Dateien

1. Öffnen Sie in Visual Studio Code die **Befehlspalette** (**F1**), geben Sie `add docker files to workspace` ein, und wählen Sie dann den Befehl **Docker: Add Docker files to workspace** (Docker: Docker-Dateien dem Arbeitsbereich hinzufügen) aus.

1. Wählen Sie **Node.js** als Anwendungstyp aus, wenn Sie dazu aufgefordert werden, antworten Sie für Docker Compose-Dateien mit **Nein**, und wählen Sie dann den Port (in der Regel 3000) aus, an dem Ihre Anwendung lauscht.

1. Der Befehl erstellt eine `Dockerfile` und einige Konfigurationsdateien für Docker Compose sowie eine `.dockerignore`-Datei.

    > [!TIP]
    > VS Code bietet hervorragende Unterstützung für Docker-Dateien. Weitere Informationen zu den umfassenden Sprachfeatures wie intelligente Vorschläge, Vervollständigung und Fehlererkennung finden Sie in der VS Code-Dokumentation unter [Verwenden von Docker](https://code.visualstudio.com/docs/azure/docker).

## <a name="build-a-docker-image"></a>Erstellen eines Docker-Images

Die `Dockerfile` beschreibt die Umgebung für Ihre App, einschließlich des Speicherorts der Quelldateien und des Befehls zum Starten der App in einem Container.

> [!TIP]
> Container im Vergleich zu Images: Ein Container ist eine Instanz eines Images.

1. Öffnen Sie die **Befehlspalette** (**F1**), und führen Sie **Docker Images: Build Image** (Docker-Images: Image erstellen) aus, um das Image zu erstellen. VS Code verwendet das Dockerfile im aktuellen Ordner und gibt dem Image den Namen des aktuellen Ordners.

1. Danach wird der Bereich **Terminal** von Visual Studio Code geöffnet, um den Befehl `docker build` auszuführen. In der Ausgabe sehen Sie auch alle Schritte bzw. Ebenen, aus denen die App-Umgebung besteht.

1. Nachdem das Image erstellt wurde, wird es im **DOCKER**-Explorer unter **Images** angezeigt.

    ![Liste der Docker-Images in Visual Studio Code](media/deploy-containers/image-list.png)

## <a name="push-the-image-to-a-registry"></a>Pushen des Images in eine Registrierung

1. Um das Image in eine Registrierung zu pushen, müssen Sie es zuerst mit dem Registrierungsnamen kennzeichnen. Klicken Sie im **DOCKER**-Explorer mit der rechten Maustaste auf das **aktuelle** Image.

    ![Befehl zur Imagekennzeichnung in Visual Studio Code](media/deploy-containers/tag-command.png)

1. Vervollständigen Sie in der folgenden Aufforderung die Tags, und drücken Sie die **EINGABETASTE**.

    Laut Konvention wird bei der Kennzeichnung folgendes Format verwendet:

    `[registry or username]/[image name]:[tag]`

    Wenn Sie Azure Container Registry verwenden, sieht der Imagename in etwa wie folgt aus:

    `msdocsvscodereg.azurecr.io/myexpressapp:latest`

    Wenn Sie Docker Hub verwenden, geben Sie Ihren Docker Hub-Benutzernamen an. Beispiel:

    `fiveisprime/myexpressapp:latest`

1. Das neu gekennzeichnete Image wird jetzt in einem Knoten unter **Images** angezeigt, der den Registrierungsnamen enthält. Erweitern Sie diesen Knoten, klicken Sie mit der rechten Maustaste auf **Neueste**, und wählen Sie **Push** aus.

    ![Befehl zum Pushen des Images in Visual Studio Code](media/deploy-containers/push-command.png)

1. Im Bereich **Terminal** werden die für diesen Vorgang verwendeten `docker push`-Befehle angezeigt. Die Zielregistrierung wird durch die im Imagenamen angegebene Registrierung bestimmt.

   > [!IMPORTANT]
   > Wird in der Ausgabe „Authentifizierung erforderlich“ angezeigt, führen Sie `az acr login --name <your registry name>` im Terminal aus.

1. Erweitern Sie nach Abschluss des Vorgangs den Knoten **Registrierungen** im Docker-Erweiterungsexplorer, um das Image in der Registrierung anzuzeigen.

    ![Anzeige des gepushten Images in Azure Container Registry](media/deploy-containers/image-in-acr.png)

> [!div class="nextstepaction"]
> [Ich habe ein Image für meine Anwendung erstellt.](tutorial-vscode-docker-node-05.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=containerize-app)
