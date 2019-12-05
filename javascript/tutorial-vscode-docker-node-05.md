---
title: Erneutes Bereitstellen eines Containers in Azure App Service, nachdem Änderungen in Visual Studio Code vorgenommen wurden
description: 'Schritt 5 des Tutorials: Einfache Schritte zum erneuten Erstellen und Bereitstellen eines Containerimages.'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 6ca29318b7dd5f1256d1b4503cf1ae9fc37ab111
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467116"
---
# <a name="make-changes-and-redeploy"></a>Vornehmen von Änderungen und erneutes Bereitstellen

[Vorheriger Schritt: Bereitstellen des App-Images](tutorial-vscode-docker-node-04.md)

Da Sie unweigerlich Änderungen an Ihrer App vornehmen, müssen Sie den Container mehrmals neu erstellen und neu bereitstellen. Glücklicherweise ist dieser Prozess ganz einfach:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor, und testen Sie sie.

1. Öffnen Sie in Visual Studio Code die **Befehlspalette** (**F1**), und führen Sie **Docker Images: Build Image** (Docker-Images: Image erstellen) aus, um das Image neu zu erstellen. Wenn Sie nur App-Code ändern, sollte der Build nur einige Sekunden dauern.

1. Um das Image in die Registrierung zu pushen, öffnen Sie erneut die **Befehlspalette** (**F1**), führen Sie **Docker Images: Push** (Docker-Images: Pushen) aus, und wählen Sie dabei das Image aus, das Sie gerade erstellt haben. Da eine Änderung des App-Codes klein ist, muss wieder nur diese Ebene gepusht werden. Der Prozess dauert daher in der Regel nur wenige Sekunden.

1. Wählen Sie im Bereich **Azure: App Service**-Explorer die entsprechende App Service-Instanz aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Neu starten** aus. Beim Neustarten einer App Service-Instanz wird automatisch das neueste Containerimage aus der Registrierung gepullt.

1. Navigieren Sie nach ungefähr 15–20 Sekunden erneut zu der App Service-URL, um die Änderungen zu überprüfen.

> [Ich sehe die Änderungen.](tutorial-vscode-docker-node-06.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=deploy-changes)
