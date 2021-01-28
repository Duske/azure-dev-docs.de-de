---
title: Was ist Azure für JavaScript-Entwickler?
description: In diesem Artikel werden Azure-Konzepte für JavaScript-, TypeScript und Node.js-Entwickler behandelt.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: devx-track-js
ms.openlocfilehash: b6d0e54bdaf1b0ea9adba9800de58fe664df8324
ms.sourcegitcommit: 3d906f265b748fbc0a070fce252098675674c8d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699798"
---
# <a name="what-is-azure-for-javascript-developers"></a>Was ist Azure für JavaScript-Entwickler?

Azure ist einer Cloudplattform, die eine umfassende Auswahl von Hostingoptionen und cloudbasierten Diensten bereitstellt. Wenn die Cloudentwicklung neu für Sie ist, erfahren Sie mehr über Azure:

* [Azure Architecture Center](/azure/architecture/) 
* [Azure-Terminologie](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts)
* [Zehn Entwurfsprinzipien für Azure-Anwendungen](/azure/architecture/guide/design-principles/)
* [Cloudentwurfsmuster](/azure/architecture/patterns/)

## <a name="javascript-typescript-and-other-languages"></a>JavaScript, TypeScript und andere Sprachen

Die Azure-Runtimeunterstützung für JavaScript unterstützt auch TypeScript und andere Arten, die auf JavaScript basieren. 

## <a name="run-javascript-with-hosted-runtime"></a>Ausführen von JavaScript mit gehosteter Runtime 

* Azure [App Service](/azure/app-service/) nutzt die Node.js-Runtime-Engine. Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Node.js-Versionen anzuzeigen:

    ```azurecli-interactive
    az webapp list-runtimes | grep node
    ```

* [Von Azure Functions unterstütze Node.js-Versionen](/azure/azure-functions/functions-reference-node?tabs=v2#node-version) basieren auf der von Ihnen verwendeten Version von Azure Functions. 

* Benutzerdefinierte Runtimes werden auf folgende Weisen unterstützt:

    * [Virtuelle Computer](/azure/virtual-machines/)
    * Container: [Einzelinstanzen](/azure/container-instances/), [Web-App](/azure/app-service/) und [Kubernetes](/azure/aks/)
    * (Serverlose) Funktionen verwenden [benutzerdefinierte Handler](/azure/azure-functions/functions-custom-handlers)

## <a name="run-javascript-with-azure-sdks"></a>Ausführen von JavaScript mit Azure SDKs

Alle Azure SDKs werden mit JavaScript und ohne andere Tools ausgeführt. Die meisten modernen SDKs werden in TypeScript geschrieben und stellen die `*.d.ts`-Datei für die Typüberprüfung bereit. TypeScript ist nicht für die Verwendung von Azure SDKs oder Azure-Clouddiensten erforderlich. 

Ihr JavaScript-Code kann Azure-Dienste unabhängig davon verwenden, wo sie gehostet werden (lokal, hybrid oder in der Cloud). Die Azure SDKs stellen die empfohlene Vorgehensweise für die programmgesteuerte Verwendung von Azure-Diensten mit JavaScript dar. Diese SDKs erfordern mindestens Version 8 der Node.js-Runtime. 

## <a name="azure-concepts-and-terminology"></a>Azure-Konzepte und -Terminologie

Für die ersten Schritte mit Azure müssen Sie:
* ein [kostenloses Abonnement](https://azure.microsoft.com/en-us/free/) erstellen.
* eine Ressource erstellen, die Folgendes umfasst:
    * Anmeldung beim [Azure-Portal](https://portal.azure.com/)
    * Erstellen einer Ressourcengruppe, in der eine logische Sammlung Ihrer Azure-Ressourcen gespeichert wird
    * Erstellen einer Ressource in Ihrer Ressourcengruppe
* die Ressource verwenden, die je nach Ressourcenart Folgendes umfassen kann:
    * Mit dem Azure-Portal
    * Mit der Azure-Befehlszeilenschnittstelle
    * Mit PowerShell
    * Mit Azure SDKs

Hier erfahren Sie mehr über [Grundlegende Azure-Konzepte und -Terminologie](/azure/cloud-adoption-framework/ready/considerations/fundamental-concepts). 

Azure bietet Anleitungen für [Namenskonventionen und Ressourcenkennzeichnung](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging), um Sie beim Finden Ihrer Ressourcen zu unterstützen. 

## <a name="select-development-ide-and-set-up-environment"></a>Auswählen und Einrichten der integrierten Entwicklungsumgebung

Sie können Ihre JavaScript-Anwendung mit einer beliebigen integrierten Entwicklungsumgebung (IDE) entwickeln. Für die JavaScript-Entwicklung wird **Visual Studio Code** empfohlen. 

Hier erfahren Sie mehr über die [empfohlenen Tools für JavaScript-Entwickler](../node-azure-tools.md). 

## <a name="select-azure-account-subscription-resource-and-tag"></a>Auswählen eines Azure-Kontos, -Abonnements, einer -Ressource und eines -Tags

* Ihr **Azure-Konto** ist durch Ihre E-Mail-Adresse an ein Abonnement gebunden. Sie können über mehrere Azure-Konten verfügen.

* Ein **Abonnement** ist eine Organisations- und Abrechnungseinheit für Ihre Ressourcen. Sie können über mehrere Abonnements verfügen. Hier erfahren Sie mehr über das [Erstellen Ihrer ersten Produktionsabonnements](/azure/cloud-adoption-framework/ready/azure-best-practices/initial-subscriptions).

* Ihre **Ressourcengruppe** stellt die nächste Organisationsebene in einem Abonnement dar. Erstellen Sie eine neue Ressourcengruppe für jedes Projekt oder jede Gruppe von Azure-Diensten, die Sie zusammen verwenden möchten. 

* Beim Auswählen eines Namens für eine **Ressource** ist es wichtig, dass Sie wissen, wann der Ressourcenname für den URL-Endpunkt der Ressource verwendet wird. Sie können vielen Ressourcen nach der Erstellung auch einen benutzerdefinierten Domänennamen zuweisen. 

* Eine dynamischere Gruppierung von Ressourcen besteht darin, **eine Ressource mit einem Eigenschaftennamen und -wert zu kennzeichnen**. Häufig besteht die Nutzung von Tags darin, Ihren Namen oder Ihre E-Mail-Adresse als Projektbesitzer zu jeder Ressource hinzuzufügen, die Sie erstellen. Ein Abonnementadministrator kann jede Ressource, die Sie besitzen, bzw. den Besitzer mühelos ermitteln, wenn Nutzungs- oder Abrechnungsprobleme auftreten. 

Eine [systematische Namenskonvention](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming) ist ein wichtiger Bestandteil der Nutzung von Azure auf Produktionsebene.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Die gängigste Methode zum Erstellen von Ressourcen für eine Person, die noch nicht mit Azure vertraut ist, ist die Verwendung des Azure-Portals. Die Schritte zur Erstellung zeigen jede Auswahl mitsamt der empfohlenen Option, die Sie tätigen müssen.

### <a name="pricing-tiers"></a>Tarife

Tarife bestimmen, wie Ihre Ressourcen in Rechnung gestellt werden. Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/en-us/pricing/calculator), um die Abrechnung Ihrer Ressource nachzuvollziehen. 

### <a name="free-tier-resources"></a>Ressourcen des Free-Tarifs

Wenn Sie den kostenlosen F0-Tarif auswählen, müssen Sie wissen, welche Einschränkungen dieser Tarif umfasst.

Bei kostenlosen Tarifen gilt Folgendes:

* Abonnements sind möglicherweise auf eine kostenlose Ressource des Diensts beschränkt. Wenn Sie keine kostenlose Ressource erstellen können, ist bereits eine kostenlose Ressource in Ihrem Abonnement vorhanden.
* Wenn Sie das Kontingent des Tarifs entweder in Transaktionen pro Sekunde oder Transaktionen pro Monat überschreiten, empfängt Ihre Anwendung einen HTTP-Fehler mit einer Meldung, die angibt, dass Sie das Kontingent überschreiten. 

### <a name="resource-creation-time"></a>Zeit für die Ressourcenerstellung

Die Erstellung der meisten Ressourcen benötigt nur wenige Minuten, andere benötigen jedoch länger. Das Benachrichtigungsfenster des Azure-Portals informiert Sie mit einem Link zur Ressource, wenn Ihre Ressource verwendet werden kann. 

## <a name="install-azure-sdk-for-resources"></a>Installieren des Azure SDKs für Ressourcen

Die [**Azure SDKs**](../azure-sdk-library-package-index.md) stellen die empfohlene Methode zur programmgesteuerten Verwendung von Ressourcen für JavaScript-Entwickler dar. 

Azure-Ressourcen sind auch über folgende Schnittstellen verfügbar:
* [Azure CLI](/cli/azure/install-azure-cli) oder [Azure Cloud Shell](https://shell.azure.com/)
* [Azure PowerShell](/powershell/azure/)
* [REST-APIs](/rest/api/azure/)

## <a name="deploy-web-apps-to-hosting-options"></a>Bereitstellen von Web-Apps in Hostingoptionen

Mithilfe von Hostingoptionen können Sie Azure schnell für Ihre Anwendung verwenden. Die folgenden Schnellstarts und Tutorials zum Hosting unterstützen Sie bei den gängigsten Azure-Vorgängen für Einsteiger:

* Client/statische Anwendung
    * [Vanilla JS](/azure/static-web-apps/getting-started?tabs=vanilla-javascript)
    * [React](/azure/static-web-apps/getting-started?tabs=react)
    * [Angular](/azure/static-web-apps/getting-started?tabs=angular)
    * [Vue](/azure/static-web-apps/getting-started?tabs=vue)
* Serveranwendung 
    * [Bereitstellen einer Express.js-MongoDB-App in App Service über Visual Studio Code](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)
* Containeranwendung 
    * [Bereitstellen von Express.js-Container-Apps in App Service aus privaten Containerregistrierungen mithilfe von Visual Studio Code](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md?tabs=bash)
* VM-Anwendung
    * [Erstellen und Bereitstellen eines virtuellen Linux-Computers mit Express.js-App mithilfe der Azure CLI und GitHub Actions](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md)

Weitere Informationen zu [Hostingoptionen](../how-to/deploy-web-app.md).

## <a name="next-steps"></a>Nächste Schritte

* [Installieren von Node.js](install-nodejs-develop-azure-sdk-project.md)
* [Empfohlene Azure-Tools für JavaScript-Entwickler](../node-azure-tools.md)
