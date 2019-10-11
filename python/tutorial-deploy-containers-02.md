---
title: 'Tutorial: Bereitstellen eines Containerimages in Azure App Service mit Visual Studio Code'
description: 'Tutorialschritt 2: Bereitstellen des eigentlichen Docker-Images in Azure App Service aus einer Containerregistrierung'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: a099e25134615fc05508cde3c0f128a3be813402
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172275"
---
# <a name="tutorial-deploy-a-container-image-to-azure-app-service"></a>Tutorial: Bereitstellen eines Containerimages in Azure App Service

[Vorheriger Schritt: Voraussetzungen](tutorial-deploy-containers-01.md)

Mit einem Containerimage in einer Registrierung können Sie die Docker-Erweiterung in VS Code verwenden, um ganz einfach eine Azure App Service-Instanz einzurichten, die in dem Container ausgeführt wird.

1. Erweitern Sie im **Docker**-Explorer die Option **Registrierungen**, den Knoten für Ihre Registrierung (z. B. **Azure**) und dann den Knoten für den Imagenamen, bis das Image mit dem Tag `:latest` angezeigt wird.

    ![Suchen eines Images im Docker-Explorer](media/deploy-containers/deploy-find-image.png)

1. Klicken Sie mit der rechten Maustaste auf das Image, und wählen Sie die Option zum **Bereitstellen des Images in Azure App Service** aus.

    ![Auswählen des Menübefehls „Bereitstellen“](media/deploy-containers/deploy-menu.png)

1. Befolgen Sie die Anweisungen, um ein Azure-Abonnement auszuwählen, wählen Sie eine Ressourcengruppe aus (oder geben Sie sie an), geben Sie eine Region an, konfigurieren Sie einen App Service-Plan (B1 ist am günstigsten), und geben Sie einen Namen für den Standort an. In der folgenden Animation wird der Prozess veranschaulicht.

    ![Animation zur Erstellung und Bereitstellung](media/deploy-containers/deploy-to-app-service.gif)

    Eine **Ressourcengruppe** ist eine benannte Sammlung der unterschiedlichen Ressourcen, aus denen eine App besteht. Indem Sie alle Ressourcen der App einer einzelnen Gruppe zuweisen, können Sie diese Ressourcen ganz einfach als eine Einheit verwalten. (Weitere Informationen finden Sie in der Azure-Dokumentation unter [Übersicht über Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).)

    In einem **App Service-Plan** werden die physischen Ressourcen (ein zugrunde liegender virtueller Computer), auf denen der ausgeführte Container gehostet wird, definiert. Für dieses Tutorial ist B1 der kostengünstigste Plan, der Docker-Container unterstützt. (Weitere Informationen finden Sie in der Azure-Dokumentation in der [Übersicht über App Service-Pläne](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).)

    Der Name der App Service-Instanz muss in Azure global eindeutig sein. Aus diesem Grund wird häufig der Firmen- oder der eigene Name verwendet. Für Produktionswebsites konfigurieren Sie App Service in der Regel mit einem separat registrierten Domänennamen.

1. Das Erstellen der App Service-Instanz dauert einige Minuten. Sie können den Fortschritt im Ausgabebereich von VS Code anzeigen.

1. Nachdem der Vorgang abgeschlossen ist, **müssen** Sie der App Service-Instanz auch eine Einstellung mit dem Namen `WEBSITES_PORT` (beachten Sie den Plural „WEBSITES“) hinzufügen, um den Port anzugeben, an dem der Container lauscht. (Wenn Sie beispielsweise ein Image aus dem Tutorial [Erstellen eines Python-Containers in VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container) verwenden, lautet der Port 5000 für Flask und 8000 für Django.) Wechseln Sie zum Festlegen von `WEBSITES_PORT` zum **Azure App Service**-Explorer, erweitern Sie den Knoten für die neue App Service-Instanz (Sie müssen ggf. eine Aktualisierung vornehmen), klicken Sie mit der rechten Maustaste auf **Anwendungseinstellungen**, und wählen Sie **Neue Einstellung hinzufügen** aus. Geben Sie in den Eingabeaufforderungen als Schlüssel `WEBSITES_PORT` und als Wert die Portnummer ein.

    ![Kontextmenübefehl in App Service für „Neue Einstellung hinzufügen“](media/deploy-containers/add-app-service-setting.png)

1. Die App Service-Instanz wird automatisch neu gestartet, wenn Sie Einstellungen ändern. Sie können auch jederzeit mit der rechten Maustaste auf die App Service-Instanz klicken und dann **Neu starten** auswählen.

1. Nachdem der Dienst neu gestartet wurde, navigieren Sie unter `http://<name>.azurewebsites.net` zu der Website. Sie können auch **STRG** (bzw. **Cmd** unter macOS) gedrückt halten, während Sie im Ausgabebereich auf die URL klicken oder mit der rechten Maustaste auf die App Service-Instanz im **Azure App Service**-Explorer klicken und dann **Website durchsuchen** auswählen.

> [!div class="nextstepaction"]
> [Ich habe das Image bereitgestellt.](tutorial-deploy-containers-03.md)

[Ich bin auf ein Problem gestoßen](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=02-deploy-container)
