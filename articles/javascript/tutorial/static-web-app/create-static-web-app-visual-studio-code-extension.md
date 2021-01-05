---
title: Erstellen der statischen Web-App-Ressource
description: Hier erfahren Sie, wie Sie die statische Web-App-Ressource mit einer Visual Studio Code-Erweiterung für diesen Dienst erstellen.
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: dac91157531b3d26dddebe411d2c79883f77d4b9
ms.sourcegitcommit: 1c508f5ba73a12e4baeacc88ad9a8359301acb50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97687467"
---
# <a name="5-create-azure-static-web-app-resource"></a>5. Erstellen der statischen Azure-Web-App-Ressource

In diesem Abschnitt des Tutorials wird die statische Web-App-Ressource mit einer Visual Studio Code-Erweiterung für diesen Dienst erstellt und lokaler Code zur Erstellung an Ihr Remoterepository gepusht. Anschließend wird App in Azure bereitgestellt.

## <a name="create-a-new-branch-dedicated-to-deployment"></a>Erstellen eines neuen, für die Bereitstellung dedizierten Branch

Die statische Azure-Web-App empfängt einen Build aus einem bestimmten Branch Ihres GitHub-Repositorys. Bis jetzt wurde im Tutorial der Branch `main` verwendet. Erstellen Sie in einem neuen Terminalfenster in Visual Studio Code einen Branch vom Typ `live`, der ausschließlich zum Erstellen und Bereitstellen der App genutzt wird.

```bash
git checkout -b live
```

## <a name="push-the-live-branch-to-github"></a>Pushen des Live-Branch an GitHub

Pushen Sie im Visual Studio Code-Terminal den lokalen Branch `live` an Ihr Remoterepository.

```bash
git push origin live
```

## <a name="create-a-static-web-app-resource"></a>Erstellen einer statischen Web-App-Ressource

1. Wählen Sie das Symbol **Azure** aus. Klicken Sie anschließend mit der rechten Maustaste auf den Dienst **Static Web Apps**, und wählen Sie **Statische Web-App erstellen (Vorschau)** aus. 

    :::image type="content" source="../../media/static-web-app/visualstudiocode-storage-extension-create-static-web-resource.png" alt-text="Screenshot: Visual Studio Code mit Visual Studio-Erweiterung":::

1. Geben Sie die folgenden Informationen in die nachfolgenden Felder ein, die jeweils nacheinander angezeigt werden: 

    |Feldname| value|
    |--|--|
    |Geben Sie einen Namen für Ihre statische Web-App ein.|`Demo-ComputerVisionAnalyzer`|
    |Wählen Sie einen Branch für das Repository aus.|`live`| 
    |Wählen Sie den Speicherort Ihres Anwendungscodes aus.|`/`|
    |Wählen Sie den Speicherort Ihres Azure Functions-Codes aus.|Wählen Sie **Vorerst überspringen** aus.|
    |Geben Sie den Pfad Ihrer Buildausgabe in Relation zum Speicherort Ihrer App ein.|`build`|
    |Auswählen eines Speicherorts für neue Ressourcen|Wählen Sie einen Azure-Standort in Ihrer Nähe aus.|

## <a name="update-the-github-action-with-secret-environment-variables"></a>Aktualisieren der GitHub-Aktion mit Geheimnisumgebungsvariablen

Schlüssel und Endpunkt für maschinelles Sehen befinden sich in der Geheimnissammlung des Repositorys, aber noch nicht in der GitHub-Aktion. In diesem Schritt werden der Schlüssel und der Endpunkt der Aktion hinzugefügt.

1. Rufen Sie die Änderungen ab, die im Zuge der Azure-Ressourcenerstellung vorgenommen wurden, um die GitHub-Aktionsdatei zu erhalten.

    ```bash
    git pull origin live
    ```

1. Bearbeiten Sie im Visual Studio Code-Editor unter `./.github/workflows/` die GitHub-Aktionsdatei, um die Geheimnisse hinzuzufügen. 

    :::code language="yml" source="~/../js-e2e-client-cognitive-services/.github/workflows/sample-github-workflow.yml" highlight="34-36" :::

    
1. Fügen Sie die Änderung dem lokalen Branch `live` hinzu, und committen Sie sie.

    ```bash
    git add . && git commit -m "add secrets to action"
    ```

1. Pushen Sie die Änderung an das Remoterepository. Dadurch wird eine neue Erstellungs- und Bereitstellungsaktion für Ihre statische Azure-Web-App gestartet.

    ```bash
    git push origin live
    ```

## <a name="view-the-github-action-build-process"></a>Anzeigen des Buildprozesses der GitHub-Aktion

1. Öffnen Sie in einem Webbrowser Ihr GitHub-Repository für dieses Tutorial, und wählen Sie **Actions** (Aktionen) aus. 

1. Wählen Sie in der Liste den obersten Build und anschließend im Menü auf der linken Seite die Option **Build and Deploy Job** (Auftrag zum Erstellen und Bereitstellen) aus, um den Buildprozess zu verfolgen. Warten Sie, bis der Auftrag **Build And Deploy** (Erstellen und Bereitstellen) erfolgreich abgeschlossen wurde.

    :::image type="content" source="../../media/static-web-app/browser-screenshot-github-action-build-react-computer-vision-app.png" alt-text=" Wählen Sie in der Liste den obersten Build und anschließend im Menü auf der linken Seite die Option „Build and Deploy Job“ (Auftrag zum Erstellen und Bereitstellen) aus, um den Buildprozess zu verfolgen. Warten Sie, bis der Buildvorgang erfolgreich abgeschlossen wurde.":::

## <a name="view-azure-static-web-site-in-browser"></a>Anzeigen der statischen Azure-Website im Browser

1. Wählen Sie in Visual Studio Code im rechten Menü das Symbol **Azure** aus. Wählen Sie dann Ihre statische Web-App aus, klicken Sie mit der rechten Maustaste auf **Browse Site** (Website durchsuchen), und wählen Sie **Open** (Öffnen) aus, um die öffentliche statische Website anzuzeigen. 

:::image type="content" source="../../media/static-web-app/visualstudiocode-browse-static-web-app.png" alt-text="Wählen Sie „Browse Site“ (Website durchsuchen) und anschließend „Open“ aus, um die öffentliche statische Website anzuzeigen. ":::

Die URL für die Website finden Sie auch an folgenden Orten:
* Im Azure-Portal für Ihre Ressource auf der Seite **Übersicht**
* In der Ausgabe des Erstellungs- und Bereitstellungsvorgangs der GitHub-Aktion (ganz am Ende des Skripts) 

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Hinzufügen von maschinellem Sehen zur React-App](add-computer-vision-react-app.md)