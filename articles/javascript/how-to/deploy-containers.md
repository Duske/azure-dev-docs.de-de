---
title: Bereitstellen von Node.js-Containern in Azure
description: Verwenden von Docker-Containern zum Bereitstellen von Node.js-Apps in Azure
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 50c26c26fffc0c9377b661d79858dd97318bd3c2
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224784"
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

Wählen Sie zum Einstieg Folgendes in der Liste aus:
* Tutorial: [Express.js](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md)
* Tutorial: [Deno](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md)

## <a name="next-steps"></a>Nächste Schritte

Microsoft Learn-Module:

- [Ausführen von Docker-Containern mit Azure Container Instances](/learn/modules/run-docker-with-azure-container-instances/)

- [Erstellen und Speichern von Containerimages mit Azure Container Registry](/learn/modules/build-and-store-container-images/)
