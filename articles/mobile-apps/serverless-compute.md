---
title: Erstellen eines serverlosen Back-Ends für eine mobile Anwendung mit Azure Functions und anderen Diensten
description: Erfahren Sie mehr über die Compute-Dienste, die zum Erstellen eine robusten serverlosen Back-Ends für mobile Anwendungen verwendet werden.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 9de5f65045ab7d947902c18be68abf1f4880eeae
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725154"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Erstellen von mobilen Back-End-Komponenten mit Compute-Diensten

Jede mobile Anwendung benötigt ein Back-End, das für die Datenspeicherung, Geschäftslogik und Sicherheit zuständig ist. Die Verwaltung der Infrastruktur zum Hosten und Ausführen von Back-End-Code erfordert, dass Sie die mehrere Server bereitstellen, skalieren und ihre Größe bestimmen. Außerdem müssen Sie Betriebssystemupdates und die beteiligten Hardwarekomponenten verwalten sowie Sicherheitspatches anwenden. Anschließend müssen Sie alle diese Infrastrukturkomponenten auf Leistung, Verfügbarkeit und Fehlertoleranz überwachen. 

Für diese Art von Szenarien erweist sich die serverlose Architektur als nützlich, da Sie weder Server oder ein Betriebssystem noch zugehörige Software- oder Hardwareupdates verwalten müssen. Eine serverlose Architektur spart Entwicklerzeit und -kosten, was eine schnellere Markteinführungszeit und eine Fokussierung auf das Entwickeln von Anwendungen bedeutet.

## <a name="benefits-of-compute"></a>Vorteile von Compute

- Abstraktion von Servern bedeutet, dass es nicht erforderlich ist, sich Gedanken über Hosting, Patching und Sicherheit zu machen, was es Ihnen ermöglicht, sich ganz auf den Code zu konzentrieren.
- Sofortige und effiziente Skalierung stellt sicher, dass Ressourcen automatisch oder nach Bedarf in jeder benötigten Größenordnung bereitgestellt werden.
- Hochverfügbarkeit und Fehlertoleranz.
- Durch Mikroabrechnung ist sichergestellt, dass für Sie nur dann Kosten anfallen, wenn Ihr Code tatsächlich ausgeführt wird.
- Code wird in der Cloud in der von Ihnen geschriebenen Sprache ausgeführt.

Verwenden Sie die folgenden Dienste, um serverlose Compute-Funktionalität in ihren mobilen Apps zu ermöglichen.

## <a name="azure-functions"></a>Azure-Funktionen

[Azure Functions](https://azure.microsoft.com/services/functions/) ist eine ereignisgesteuerte Computelösung, in der Sie Code ausführen können, der in einer Programmiersprache Ihrer Wahl geschrieben wurde, ohne dass Sie sich Gedanken über Server machen müssen. Sie müssen die Anwendung oder die Infrastruktur nicht verwalten, um sie auf ihr auszuführen. Funktionen werden nach Bedarf skaliert, und Sie bezahlen nur für die Zeit, in der Ihr Code ausgeführt wird. Azure Functions ist eine hervorragend Möglichkeit, um eine API für eine mobile Anwendung zu implementieren. Sie lassen sich einfach implementieren und warten und sind über HTTP zugänglich.

### <a name="azure-functions-key-features"></a>Wichtige Features von Azure Functions

- Ereignisgesteuert und skalierbar, wobei Sie Trigger und Bindungen verwenden können, um zu definieren, wann eine Funktion aufgerufen und mit welchen Daten sie verbunden wird.
- Verwenden Sie eigene Abhängigkeiten (BYOD), weil Functions NuGet und NPM unterstützt, sodass Sie Ihre bevorzugten Bibliotheken verwenden können.
- Integrierte Sicherheit, sodass Sie per HTTP ausgelöste Funktionen mit OAuth-Anbietern wie Azure Active Directory, Facebook, Google, Twitter und Microsoft-Konto schützen können.
- Vereinfachte Integration mit unterschiedlichen [Azure-Diensten](/azure/azure-functions/functions-overview) und SaaS-Angeboten (Software-as-a-Service).
- Flexible Entwicklung, sodass Sie Ihre Funktionen direkt im Portal codieren oder Continuous Integration einrichten können sowie Ihren Code über GitHub, Azure DevOps Services und andere unterstützte Entwicklungstools bereitstellen.
- Die Functions-Runtime ist Open Source-Software und auf [GitHub](https://github.com/azure/azure-webjobs-sdk-script) verfügbar.
- Verbesserte Entwicklungsoberfläche, in der Sie lokal mit ihrem bevorzugten Editor oder einer benutzerfreundlichen Webschnittstelle codieren, testen und debuggen können, samt Überwachung mit integrierten Tools und DevOps-Funktionen.
- Eine Vielzahl von Programmiersprachen und Hostingoptionen zum Entwickeln, z. B. C#, Node.js, Java, Javascript oder Python.
- Preismodell mit nutzungsbasierter Bezahlung bedeutet, dass Sie nur für die Zeit bezahlen, in der Ihr Code ausgeführt wird.

### <a name="azure-functions-references"></a>Azure Functions-Referenzen

- [Azure portal](https://portal.azure.com)
- [Dokumentation zu Azure Functions](/azure/azure-functions/)
- [Azure Functions: Entwicklerhandbuch](/azure/azure-functions/functions-reference)
- [Schnellstarts](/azure/azure-functions/functions-create-first-function-vs-code)
- [Beispiele](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service

Mit [Azure App Service](https://azure.microsoft.com/services/app-service/) können Sie Web-Apps und RESTful-APIs in der Programmiersprache Ihrer Wahl erstellen und hosten, ohne eine Infrastruktur verwalten zu müssen. Der Dienst bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein anderes beliebiges Git-Repository.

### <a name="azure-app-service-key-features"></a>Wichtige Features von Azure App Service

- Unterstützung mehrerer Sprachen und Frameworks für ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP oder Python. Sie können PowerShell- und weitere Skripts oder ausführbare Dateien auch als Hintergrunddienst ausführen.
- DevOps-Optimierung durch Continuous Integration und Continuous Deployment mit Azure DevOps, GitHub, BitBucket, Docker Hub oder Azure Container Registry. Verwalten Sie Ihre Apps in App Service mithilfe von Azure PowerShell oder der plattformübergreifenden Befehlszeilenschnittstelle (Command-Line Interface, CLI).
- Globale Skalierung mit Hochverfügbarkeit, um manuell oder automatisch hochzuskalieren oder zu erweitern.
- Verbindungen mit SaaS-Plattformen und lokalen Daten, um aus über 50 Connectors für Unternehmenssysteme wie SAP, SaaS-Dienste wie Salesforce sowie Internetdienste wie Facebook auszuwählen. Zugriff auf lokale Daten über Hybridverbindungen und Azure Virtual Networks.
- Azure App Service ist ISO-, SOC- und PCI-konform. Authentifizieren von Benutzern mit Azure Active Directory oder mit Anmeldungen für soziale Medien wie Google, Facebook, Twitter und Microsoft. Erstellen Sie IP-Adresseinschränkungen, und verwalten Sie Dienstidentitäten.
- Anwendungsvorlagen, um im Azure Marketplace aus zahlreichen Anwendungsvorlagen wie WordPress, Joomla und Drupal zu wählen.
- Visual Studio-Integration mit dedizierten Tools in Visual Studio optimiert das Erstellen, Bereitstellen und Debuggen.

### <a name="azure-app-service-references"></a>Azure App Service-Referenzen

- [Azure portal](https://portal.azure.com/)
- [Azure App Service-Dokumentation](/azure/app-service/)
- [Schnellstarts](/azure/app-service/app-service-web-get-started-dotnet)
- [Beispiele](/azure/app-service/samples-cli)
- [Tutorials](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) verwaltet Ihre gehostete Kubernetes-Umgebung. Mit AKS können Sie ganz ohne Kenntnisse im Bereich Containerorchestrierung schnell und einfach containerbasierte Anwendungen bereitstellen und verwalten. Darüber hinaus beseitigt es die Notwendigkeit laufender Vorgänge und Wartungsaufgaben. AKS stellt Ressourcen bedarfsgesteuert bereit und aktualisiert und skaliert sie entsprechend, ohne Ihre Anwendungen offline zu schalten.

### <a name="azure-kubernetes-service-key-features"></a>Wichtige Features von Azure Kubernetes Service

- Einfaches Migrieren vorhandener Anwendungen zu Containern und Ausführen in AKS.
- Vereinfachen der Bereitstellung und Verwaltung von auf Microservices basierenden Anwendungen.
- Sichere DevOps für AKS, um ein ausgewogenes Verhältnis zwischen Geschwindigkeit und Sicherheit zu erreichen und Code schneller bedarfsgerecht bereitzustellen.
- Müheloses Skalieren mit AKS und Azure Container Instances, um Pods in Containerinstanzen bereitzustellen, die in Sekunden starten.
- Bereitstellen und Verwalten von IoT-Geräten bei Bedarf.
- Trainieren von Machine Learning-Modellen mit Tools wie TensorFlow und KubeFlow.

### <a name="azure-kubernetes-service-references"></a>Azure Kubernetes Service-Referenzen

- [Azure portal](https://portal.azure.com/)
- [Dokumentation zu Azure Kubernetes Service](/azure/aks/)
- [Schnellstarts](/azure/aks/kubernetes-walkthrough-portal)
- [Tutorials](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances

[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) ist eine großartige Lösung für jedes Szenario, das für die Verwendung isolierter Container geeignet ist, wie einfache Anwendungen, Aufgabenautomatisierung und Erstellungsaufträge. Entwickeln Sie Apps schnell, ohne virtuelle Computer zu verwalten.

### <a name="azure-container-instances-key-features"></a>Wichtige Features von Azure Container Instances

- Schnelle Startzeiten, da Container Instances Container in Azure in Sekundenschnelle starten kann, ohne virtuelle Computer bereitstellen und verwalten zu müssen.
- Öffentliche IP-Konnektivität und benutzerdefinierter DNS-Name.
- Sicherheit auf Hypervisorebene, die gewährleistet, dass Ihre Anwendung in einem Container isoliert ist – genau wie bei einem virtuellen Computer.
- Angepasste Größen für optimale Nutzung, indem exakte Angaben für CPU-Kerne und Arbeitsspeicher möglich sind. Dank sekundengenauer Abrechnung können Sie Ihre Ausgaben auf der Grundlage Ihres tatsächlichen Bedarfs präzise optimieren.
- Dauerhafte Speicherung zum Abrufen und Erhalten des Zustands. Container Instances bietet eine direkte Einbindung Azure Files-Freigaben.
- Linux-und Windows-Container, die mit derselben API geplant werden.

### <a name="azure-container-instances-references"></a>Azure Container Instances-Referenzen

- [Azure portal](https://portal.azure.com/)
- [Dokumentation zu Azure Container Instances](/azure/container-instances/)
- [Schnellstarts](/azure/container-instances/container-instances-quickstart-portal)
- [Beispiele](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Tutorials](/azure/container-instances/container-instances-tutorial-prepare-app)