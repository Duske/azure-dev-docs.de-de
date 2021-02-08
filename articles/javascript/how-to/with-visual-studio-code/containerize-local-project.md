---
title: Erstellen eines Containers auf der Grundlage eines lokalen Projekts
description: Erstellen eines Dockerfile Ihres lokalen Entwicklungsprojekts mit Visual Studio Code
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: a299e4647e3257627b62242a4ab63b16fc5590a3
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230147"
---
# <a name="create-a-container-image-from-your-local-javascript-project"></a>Erstellen eines Containerimages aus Ihrem lokalen JavaScript-Projekt

Wenn Sie eine App in einem Container ausführen, können Sie Ihren Web-App-Benutzern eine konsistente Erfahrung bereitstellen. Da die Nutzung von Docker nicht immer einfach ist, bietet Visual Studio Code eine Erweiterung, die gängige Docker-Aufgaben vereinfacht.

## <a name="prepare-your-environment"></a>Vorbereiten der Umgebung 

[Docker](https://www.docker.com/) muss installiert sein und ausgeführt werden. Überprüfen Sie dies mit folgendem Befehl:

```bash
docker system info
```

Dieser Befehl gibt einen Fehler zurück, wenn Docker nicht installiert ist und ausgeführt wird. 

## <a name="create-a-container"></a>Erstellen eines Containers

1. Öffnen Sie Visual Studio mit einem vorhandenen JavaScript-Projekt. 
1. Wählen Sie in der Aktivitätsleiste das Symbol **Erweiterungen** aus, suchen Sie nach `docker`, und wählen Sie die [Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) **Docker** aus.
1. Installieren Sie die Docker-Erweiterung, und laden Sie Visual Studio Code dann erneut.

    ![Installieren der Docker-Erweiterung für Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-docker-extension.png)

    Die Docker-Erweiterung für Visual Studio Code enthält einen Befehl zum Generieren eines *Dockerfile* und die Datei *docker-compose.yml* für ein vorhandenes Projekt.

1. Wählen Sie das Docker-Symbol in der Aktivitätsleiste aus, und zeigen Sie dann die Docker-Container in der Seitenleiste an.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-activity-bar-side-bar.png" alt-text="Suchen nach „git branch“ und Auswählen von „Git: Create Brand“.":::

## <a name="view-available-docker-commands"></a>Anzeigen verfügbarer Docker-Befehle

Zeigen Sie zum Auflisten der verfügbaren Docker-Befehle die Befehlspalette (**F1**) an, und geben Sie `docker` ein.

![Von der Docker-Erweiterung für Visual Studio Code unterstützte Befehle ](../../media/node-howto-e2e/visual-studio-code-available-docker-codes.png)

## <a name="create-a-dockerfile-in-your-project"></a>Erstellen eines Dockerfile in Ihrem Projekt

1. Wenn Sie Quellcodeverwaltung verwenden, z. B. **git**, stellen Sie sicher, dass keine weiteren Änderungen vorhanden sind. Auf diese Weise können Sie sehen, welche Dateien für Sie erstellt werden.

1. Wählen Sie **Docker: Hinzufügen von Docker-Dateien zum Arbeitsbereich** mit den Einstellungen für Ihr Projekt aus. 

    Diese Einstellungen sind für Node.js-Projekt gängig:

    |Einstellung|Wert|
    |--|--|
    |Anwendungsplattform|Node.js|
    |Package.json|package.json|
    |Verfügbar zu machender Port|8080 – oder der automatisch erkannte Wert|
    |Optionale Docker-Dateien einschließen (.dockerignore) |ja|

    ![Erstelltes Dockerfile in Visual Studio Code](../../media/node-howto-e2e/visual-studio-code-complete-dockerfile.png)

    Mit dem Docker-Befehl werden ein vollständiges *Dockerfile* und Docker-Compose-Dateien generiert, die Sie sofort nutzen können.

## <a name="build-image-from-your-project"></a>Erstellen eines Images aus Ihrem Projekt

1. Drücken Sie **F1**, geben Sie in der Befehlspalette `dockerb` ein, und wählen Sie den Befehl **Docker: Build Image** (Docker: Image erstellen). 
1. Wählen Sie das *Dockerfile* aus, das Sie gerade generiert haben. 
1. Wenn Ihre Datei „package.json“ eine name-Eigenschaft besitzt, wird diese als Imagename Ihres Containers verwendet. 
    Wenn Sie keine Datei „package.json“ besitzen, geben Sie ein Tag mit dem Format `ALIAS/IMAGE-NAME` an, wobei ALILAS Ihr Docker-Alias und IMAGE-NAME der Name für das Image Ihres Projekts ist. Ein Beispieltag wäre `diberry/express-web-app`. 
1. Drücken Sie die **EINGABETASTE**, um das integrierte Terminalfenster zu starten, in dem die Ausgabe Ihres zu erstellenden Docker-Image angezeigt wird.

    ![Buildausgabe des Docker-Image](../../media/node-howto-e2e/docker-build-image-output.png)

    Der Befehl hat den Prozess der Ausführung von `docker build` für Sie automatisiert.

1. Wählen Sie das Docker-Symbol in der Aktivitätsleiste aus, und wählen Sie dann **Images** aus, damit das neue Image in der Liste der Images angezeigt wird. 
    
    Möglicherweise bemerken Sie auch andere neue Images in der Liste. Docker ruft das Image ab, auf dem Ihr Container basiert.  

## <a name="run-local-container-project"></a>Ausführen eines lokalen Containerprojekts

1. Wählen Sie das Docker-Symbol in der Aktivitätsleiste aus.
1. Klicken Sie mit der rechten Maustaste auf den Imagenamen in der Liste mit den **Images**, und wählen Sie **Ausführen** aus.

    :::image type="content" source="../../media/howto-containerize-local-project/docker-extension-running-container-visual-studio-code.png" alt-text="Mit der rechten Maustaste auf den Imagenamen in der Liste mit den Images klicken und „Ausführen“ auswählen.":::

## <a name="push-local-container-image-to-dockerhub"></a>Pushübertragung des lokalen Containerimages in DockerHub

Das Image muss über eine Registrierung verfügbar sein, um eine Azure-Web-App aus dem Image erstellen zu können. Das Image kann öffentlich in einer Communityregistrierung oder in einer privaten Registrierung verfügbar sein, worauf mit einer Authentifizierung wie [Azure Container Registry](/azure/container-registry/) zugegriffen wird. 

Stellen Sie beim Übertragen des Images per Push sicher, dass Sie die Authentifizierung mit DockerHub bereits durchgeführt haben, indem Sie `docker login` über die CLI ausführen und Ihre Anmeldeinformationen für Ihr Konto eingeben.

1. Rufen Sie in Visual Studio Code die Befehlspalette mit F1 auf.
1. Geben Sie `dockerpush` ein, und wählen Sie den Befehl `Docker: Push` aus. 
1. Wählen Sie das Imagetag aus, das Sie gerade erstellt haben (z. B. `diberry/express-web-app`), und drücken Sie die **EINGABETASTE**. 
1. Mit dem Befehl wird der Aufruf von `docker push` automatisiert und die Ausgabe im integrierten Terminal angezeigt.

## <a name="push-local-container-image-to-azure-container-registry"></a>Pushübertragung des lokalen Containerimages in Azure Container Registry

Lesen Sie die Schritte zum [Authentifizieren und Pushen in Ihre eigene Azure Container Registry](../with-azure-cli/create-container-registry-resource.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Azure Container Registry-Ressource](../with-azure-cli/create-container-registry-resource.md)