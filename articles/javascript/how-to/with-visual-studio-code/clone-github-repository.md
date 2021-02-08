---
title: Klonen eines GitHub-Repositorys mit VSCode
description: Klonen eines öffentlichen Repositorys von GitHub auf Ihren lokalen Computer mithilfe von Visual Studio Code.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js
ms.openlocfilehash: f3bf194f7520779b92d4cfb5966eedb0f599edea
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224804"
---
# <a name="clone-and-use-a-github-repository-in-visual-studio-code"></a>Klonen und Verwenden eines GitHub-Repositorys in Visual Studio Code

Hier erfahren Sie die Schritte zum Klonen eines öffentlichen Repositorys von GitHub auf Ihren lokalen Computer mithilfe von Visual Studio Code.

Beim Arbeiten in Visual Studio Code mit einem Repository werden separate Tools verwendet:

|Symbol|Information|[Zugriff von](https://code.visualstudio.com/docs/getstarted/userinterface)|
|--|--|--|
|| [Git CLI](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette)|Befehlspalette: F1|
|:::image type="content" source="../../media/how-to-clone-github-repo/git-commit-icon-activity-bar.png" alt-text="Symbol für die Quellcodeverwaltung.":::|Quellcodeverwaltungs-Erweiterung|Aktivitätsleiste|
|:::image type="content" source="../../media/how-to-clone-github-repo/github-icon-activity-bar.png" alt-text="Symbol für GitHub-Probleme":::|GitHub-Erweiterung|Aktivitätsleiste|

Die folgenden Prozeduren verwenden die benannten Teile der [Visual Studio Code-Benutzeroberfläche](https://code.visualstudio.com/docs/getstarted/userinterface). 

## <a name="use-command-palette-to-clone-repository"></a>Verwenden der Befehlspalette zum Klonen des Repositorys

Laden Sie als Erstes das Beispielprojekt herunter, indem Sie die folgenden Schritte ausführen:

1. Drücken Sie **F1**, um die Befehlspalette anzuzeigen.

1. Geben Sie an der Eingabeaufforderung der Befehlspalette `gitcl` ein, wählen Sie den Befehl **Git: Clone** aus, und drücken Sie die **EINGABETASTE**.

    ![Befehl „gitcl“ in der Eingabeaufforderung der Befehlspalette von Visual Studio Code](../../media/how-to-clone-github-repo/visual-studio-code-git-clone.png)

1. Geben Sie ein GitHub-Repository ein, wenn Sie zum Eingeben der **Repository-URL** aufgefordert werden, und drücken Sie anschließend die **EINGABETASTE**.

1. Wählen bzw. erstellen Sie das lokale Verzeichnis, in dem das Projekt geklont werden soll.

    ![Visual Studio Code-Explorer](../../media/how-to-clone-github-repo/visual-studio-code-explorer.png)

## <a name="create-a-branch-for-changes-with-git-cl"></a>Erstellen eines Branch für Änderungen mit Git CL

Verwenden Sie Git in der Befehlspalette, um einen neuen Branch zu erstellen.

1. Drücken Sie **F1**, um die Befehlspalette anzuzeigen.
1. Suchen Sie nach `git branch`, und wählen Sie `Git: Create Branch` aus.

    :::image type="content" source="../../media/how-to-clone-github-repo/git-cli-branch-list.png" alt-text="Suchen nach „git branch“ und Auswählen von „Git: Create Brand“.":::

1. Geben Sie einen neuen Branchnamen ein. Der Branchname wird in der Statusleiste angezeigt. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="Der Branchname wird in der Statusleiste angezeigt.":::

## <a name="create-a-branch-from-status-bar"></a>Erstellen eines Branch aus der Statusleiste

1. Wählen Sie den Branchnamen in der Statusleiste aus. 

    Die Statusleiste finden Sie in der Regel am unteren Rand von Visual Studio Code. 

1. Wählen Sie in der Befehlspalette **++Neuen Branch erstellen** aus.
1. Geben Sie Ihren neuen Branchnamen ein. 

1. Geben Sie einen neuen Branchnamen ein. Der Branchname wird in der Statusleiste angezeigt. 

    :::image type="content" source="../../media/how-to-clone-github-repo/git-branch-status-bar-visual-studio-code.png" alt-text="Der Branchname wird in der Statusleiste angezeigt.":::

## <a name="commit-changes-with-git"></a>Committen von Änderungen mit Git 

Nachdem Sie Änderungen an Ihrem Branch vorgenommen haben, committen Sie die Änderungen.

1. Wechseln Sie zur Aktivitätsleiste, und wählen Sie das Git-Symbol aus.

1. Geben Sie im Feld **Nachricht** eine Commit-Nachricht ein, und drücken Sie **STRG**+**EINGABE**, oder wählen Sie das Häkchen in der Quellcodeverwaltungs-Leiste aus.

    ![Hinzufügen der Datei „yarn.lock“ zu Git](../../media/how-to-clone-github-repo/visual-studio-code-add-yarn-lock.png)

## <a name="push-a-local-branch-to-remote-from-status-bar"></a>Pushen eines lokalen Branch an einen Remoteort aus der Statusleiste

1. Wählen Sie das Push-Symbol rechts vom Branchnamen aus. 
1. Wählen Sie den Remotenamen im Popupfenster aus. Wenn Sie nur einen Remoteort haben, werden Sie nicht aufgefordert, den Remotenamen auszuwählen. 

## <a name="push-a-local-branch-to-remote-from-the-source-control-extension"></a>Pushen eines lokalen Branch an einen Remoteort aus der Quellcodeverwaltungs-Erweiterung
1. Wählen Sie das Quellcodeverwaltungs-Symbol in der Aktivitätsleiste aus. 
1. Wählen Sie die Auslassungspunkte (...) aus, und wählen Sie dann **Pull, Push** und dann **Push zu...** aus. 
1. Wählen Sie den Remotenamen im Popupfenster aus. Wenn Sie nur einen Remoteort haben, werden Sie nicht aufgefordert, den Remotenamen auszuwählen. 

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Web-App](../deploy-web-app.md)
