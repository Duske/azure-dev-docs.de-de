---
title: Bereitstellen von Node.js-Containern in Azure
description: Verwenden von Docker-Containern zum Bereitstellen von Node.js-Apps in Azure
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 6bf8acb66a708433966bdfe90cc358a56d2d3b42
ms.sourcegitcommit: ae2fa266a36958c04625bb0ab212e6f2db98e026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96857808"
---
# <a name="deploy-nodejs-container-to-azure"></a>Bereitstellen eines Node.js-Containers in Azure 

Das Entwickeln von Apps mit Containern ist ein gängiges Muster für Skalierbarkeit. Azure bietet verschiedene Möglichkeiten für die Bereitstellung Ihrer Container.

## <a name="host-your-container-app-on-azure"></a>Hosten Ihrer Container-App in Azure

Mit den folgenden Hostingoptionen können Sie Ihre Containeranwendungen bereitstellen.

| Dienst | Empfohlen für |
|--|--|
|[App Service](/azure/app-service/quickstart-custom-container?pivots=container-linux)|Bereitstellen und Ausführen eines benutzerdefinierten Containers in Azure App Service|
|[Containerinstanzen](/azure/container-instances/)|Richten Sie einen Container ohne viel Zeitaufwand ein.|
|[Container Registry](/azure/container-registry/)|Erstellen, Speichern und Verwalten von benutzerdefinierten oder privaten Containerimages|
|[Kubernetes-Dienst](/azure/aks/)|Orchestrierungen mit mehreren Containern.|
|[Virtual Machines](/azure/virtual-machines) (VMs)|Vollständige Kontrolle über einen virtuellen Windows- oder Linux-Computer. [Wählen Sie eine unterstützte Linux-Distribution aus](/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json), oder [informieren Sie sich über die Ermittlung von Linux-VM-Images im Azure Marketplace](/azure/virtual-machines/linux/cli-ps-findimage).|

## <a name="build-containerize-and-deploy-app-to-azure"></a>Erstellen, Containerisieren und Bereitstellen von Apps in Azure

Arbeiten Sie zum Einstieg [dieses Tutorial](develop-nodejs-on-azure.md) durch, um Folgendes zu lernen:

* Beispielcode herunterladen
* Ausführen der Node.js-App
* Debuggen der App in Visual Studio Code
* Containerisieren der Node.js-MEAN-App
* Bereitstellen der App mithilfe von Azure CLI-Befehlen
* Erstellen eines MongoDB-Servers für eine CosmosDB-Ressource
* Hinzufügen des Containerimages zu Ihrer privaten Containerregistrierung
* Hinzufügen eines benutzerdefinierten Domänennamens zu Ihrer Web-App
* Aufskalieren Ihrer Web-App auf eine größere Größe
* Erstellen und Löschen einer Ressourcengruppe für alle Ressourcen

## <a name="next-steps"></a>Nächste Schritte

Microsoft Learn-Module:

- [Ausführen von Docker-Containern mit Azure Container Instances](/learn/modules/run-docker-with-azure-container-instances/)

- [Erstellen und Speichern von Containerimages mit Azure Container Registry](/learn/modules/build-and-store-container-images/)
