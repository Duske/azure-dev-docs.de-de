---
title: Bereitstellen von JavaScript-Apps in Azure
description: Die Hostingoptionen und Bereitstellungsszenarien umfassen mehrere Dienste und Tools für Azure. Veröffentlichen Sie Ihre App, und stellen Sie sie in Azure bereit.
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, contperfq2
ms.openlocfilehash: 93c9fa67e7fd928640478650d23207d48dd07531
ms.sourcegitcommit: e1175aa94709b14b283645986a34a385999fb3f7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93192512"
---
# <a name="deploy-and-host-your-nodejs-apps-on-azure"></a>Bereitstellen und Hosten Ihrer Node.js-Apps in Azure

Die Hostingoptionen und Bereitstellungsszenarien umfassen mehrere Dienste und Tools für Azure. Azure verfügt über verschiedene Optionen für das Hosting und über viele Tools, mit denen Sie Ihre App aus einem lokalen oder Cloudrepository nach Azure verschieben können. 

## <a name="choose-a-hosting-provider-from-azure"></a>Auswählen eines Hostinganbieters für Azure

Sie können unter vielen verschiedenen Lösungen wählen, um Ihre Client-, Server- oder Hintergrundaufgabe-App in Azure zu hosten. Verwenden Sie die Informationen in der folgenden Tabelle, um eine Auswahl zu treffen. Die empfohlene Lösung für die meisten Anwendungsfälle ist [Azure App Service](/azure/app-service/overview.md). 

| Dienst | Empfohlen für |
|--|--|
|[App Service](/azure/app-service/overview.md): **Empfohlen**|Hosten Sie Ihre App per Code oder über einen Container. Dies ermöglicht Ihnen die Verwaltung des Webservers, ohne dass Sie die zugrunde liegende Umgebung verwalten müssen.|
|[Statische Web-Apps](/azure/static-web-apps/)|Hosten Sie Ihre statische Client-App (z. B. Angular, Vue, React). Fügen Sie optional Endpunkte mit serverlosen Funktionen hinzu, um eine Full-Stack-App zu hosten. Mit diesem einfachen Dienst wird ein Großteil des Webservers abstrahiert, damit Sie sich auf die Features konzentrieren können, die für eine Clientanwendung wichtig sind. |
|[Funktionen](/azure/azure-functions/)|Hosten Sie Ihre serverlosen Anwendungsendpunkte.|
|[Storage](/azure/storage/blobs/storage-blob-static-website-how-to.md?tabs=azure-portal)|Mit Azure Storage kann auch eine statische Web-App gehostet werden. Dies ist hilfreich, wenn Sie eine enge Integration zwischen stabilem Speicher und Ihrer Clientanwendung benötigen.|
|[Containerinstanzen](/azure/container-instances/)|Richten Sie einen Container ohne viel Zeitaufwand ein.|
|[Kubernetes-Dienst](/azure/aks/)|Orchestrierungen mit mehreren Containern.|
|[Virtual Machines](/azure/virtual-machines) (VMs)|Vollständige Kontrolle über einen virtuellen Windows- oder Linux-Computer. [Wählen Sie eine unterstützte Linux-Distribution aus](/azure/virtual-machines/linux/endorsed-distros?toc=/azure/virtual-machines/linux/toc.json), oder [informieren Sie sich über die Ermittlung von Linux-VM-Images im Azure Marketplace](/azure/virtual-machines/linux/cli-ps-findimage.md).|
|[Content Delivery Network (CDN)](/azure/cdn/)|Speichern Sie über den nächstgelegenen POP-Server statische Objekte zwischen, die über Azure Blob Storage, eine Webanwendung oder einen öffentlich zugänglichen Webserver geladen wurden. Durch die Nutzung verschiedener Netzwerk- und Routingoptimierungen kann Azure CDN außerdem dynamische Inhalte beschleunigen, die nicht zwischengespeichert werden können.|

Eine umfassende Übersicht über die verschiedenen Hostingoptionen finden Sie unter [Entscheidungsstruktur für Azure-Computedienste](/azure/architecture/guide/technology-choices/compute-decision-tree) und im Microsoft Learn-Modul [Wichtige Clouddienste: Computeoptionen in Azure](/learn/modules/intro-to-azure-compute.md).

## <a name="choose-your-deployment-process-for-azure"></a>Auswählen Ihres Bereitstellungsprozesses für Azure

Nachdem Sie einen Dienst zum Hosten Ihrer Anwendung ausgewählt haben, ist der nächste Schritt die Auswahl eines Bereitstellungsprozesses und eines Tools. Die Bereitstellung Ihrer Client- und Server-Apps für Azure-Dienste bedeutet, dass Sie eine oder mehrere Dateien nach Azure verschieben müssen, damit sie über einen HTTP-Endpunkt bereitgestellt werden können. 

Häufig verwendete Methoden zum Verschieben von Dateien in die Azure-Cloud sind in der folgenden Tabelle aufgeführt.

| Methode | Details |
|--|--|
|[GitHub-Aktionen](/azure/app-service/deploy-github-actions.md?tabs=applevel)|Verwenden Sie diese Option für automatisierte oder ausgelöste Continuous Deployment-Vorgänge.|
|[Visual Studio Code-Erweiterungen](https://marketplace.visualstudio.com/search?term=azure&target=VSCode&category=All%20categories&sortBy=Relevance)|Verwenden Sie diese Option für Bereitstellungen, die manuell, zu Testzwecken oder nur selten durchgeführt werden. Hierfür ist es erforderlich, dass Sie die Erweiterung für den Dienst lokal installiert haben.|
|[Azure-Befehlszeilenschnittstelle](../tutorial-vscode-azure-cli-node-04.md)|Verwenden Sie diese Option für Bereitstellungen, die manuell oder nur selten durchgeführt werden. Hierfür ist es erforderlich, dass Sie die Erweiterung für den Dienst lokal installiert haben.|

Je nach Dienst können auch andere Bereitstellungsmethoden vorhanden sein. Für Azure App Service werden beispielsweise viele Bereitstellungsmethoden unterstützt:
* [Per ZIP-Datei](/azure/app-service/deploy-zip.md)
* [Per FTP](/azure/app-service/deploy-ftp.md)
* [Dropbox oder OneDrive](/app-service/deploy-content-sync.md)
* [Git (lokal)](/azure/app-service/deploy-local-git.md)

## <a name="verify-your-deployment-with-your-http-endpoint"></a>Überprüfen Ihrer Bereitstellung mit Ihrem HTTP-Endpunkt

Greifen Sie zum Überprüfen Ihrer Bereitstellung auf Ihren HTTP-Endpunkt zu. Der HTTP-Endpunkt wird für alle Dienste auf der Seite **Übersicht** angezeigt. 

### <a name="view-http-endpoint-in-azure-portal"></a>Anzeigen des HTTP-Endpunkts im Azure-Portal

Zeigen Sie Ihren HTTP-Endpunkt auf der Übersichtsseite des Diensts im Azure-Portal an. 

:::image type="content" source="../media/howto-deploy/azure-portal-hosting-url.png" alt-text="Anzeigen Ihres HTTP-Endpunkts auf der Übersichtsseite des Diensts im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen mit Containern](deploy-containers.md)