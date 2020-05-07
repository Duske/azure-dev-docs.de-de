---
title: Übersicht über Jenkins und Azure
description: Verwenden Sie Azure für das Hosten des Jenkins-Builds und das Bereitstellen von Automatisierungsservern. Nutzen Sie darüber hinaus Azure Compute- und Azure Storage-Ressourcen, um die Pipelines für Continuous Integration und Continuous Deployment (CI/CD) zu erweitern.
keywords: Jenkins, Azure, DevOps, Übersicht
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 19bacd6e1b3d4ddee4e6fef27b2183f4a33545d6
ms.sourcegitcommit: 8309822d57f784a9c2ca67428ad7e7330bb5e0d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82861303"
---
# <a name="azure-and-jenkins"></a>Azure und Jenkins

[Jenkins](https://jenkins.io/) ist ein beliebter Open-Source-Automatisierungsserver zum Einrichten von Continuous Integration und Continuous Deployment (CI/CD) für Ihre Softwareprojekte. Sie können Ihre Jenkins-Bereitstellung in Azure hosten oder Ihre bestehende Jenkins-Konfiguration mithilfe von Azure-Ressourcen erweitern. Außerdem sind Jenkins-Plug-Ins verfügbar, die die CI/CD Ihrer Anwendungen in Azure vereinfachen.

Dieser Artikel bietet eine Einführung in die Verwendung von Azure mit Jenkins. Sie erhalten darin Informationen zu den wichtigsten Features von Azure für Jenkins-Benutzer. Weitere Informationen zu den ersten Schritten mit Ihrem eigenen Jenkins-Server in Azure finden Sie unter [Erstellen eines Jenkins-Servers auf einem virtuellen Azure-Linux-Computer über das Azure-Portal](configure-on-linux-vm.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hosten von Jenkins-Servern in Azure

Hosten Sie Jenkins in Azure, um Ihre Buildautomatisierung zu zentralisieren und Ihre Bereitstellung zu skalieren, wenn die Anforderungen Ihrer Softwareprojekte steigen. Sie können Jenkins in Azure wie folgt bereitstellen:
 
- Mit der [Jenkins-Projektmappenvorlage](configure-on-linux-vm.md) in Azure Marketplace
- Mit [Azure Virtual Machines](/azure/virtual-machines/linux/overview). In unserem [Tutorial](pipeline-with-github-and-docker.md) finden Sie Informationen zum Erstellen einer Jenkins-Instanz auf einem virtuellen Computer.
- Informationen zu einem Kubernetes-Cluster im [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) finden Sie in unserer [Anleitung](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Überwachen und verwalten Sie Ihre Azure Jenkins-Bereitstellung mit [Azure Monitor-Protokollen](/azure/log-analytics/log-analytics-overview) und der [Azure-Befehlszeilenschnittstelle](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skalieren der Buildautomatisierung nach Bedarf

Fügen Sie Ihrer vorhandenen Jenkins-Bereitstellung Build-Agents hinzu, um Ihre Jenkins-Buildkapazität zu skalieren, wenn sich die Anzahl von Builds und die Komplexität Ihrer Aufträge und Pipelines erhöhen. Sie können diese Build-Agents mithilfe des [Plug-Ins für Azure-VM-Agents](https://plugins.jenkins.io/azure-vm-agents) auf virtuellen Azure-Computern ausführen. Weitere Einzelheiten finden Sie in unserem [Tutorial](/azure/jenkins/jenkins-azure-vm-agents).

Nach der Konfiguration mit einem [Azure-Dienstprinzipal](/azure/azure-resource-manager/resource-group-overview) können Jenkins-Aufträge und -Pipelines diese Anmeldeinformationen für Folgendes verwenden:

- Sicheres Speichern und Archivieren von Buildartefakten in [Azure Storage](/azure/storage/common/storage-introduction) mithilfe des [Azure Storage-Plug-Ins](https://plugins.jenkins.io/windows-azure-storage). Weitere Informationen finden Sie in der [Jenkins-Speicheranleitung](azure-storage-blobs-as-build-artifact-repository.md).
- Sie verwalten und konfigurieren Azure-Ressourcen mit der [Azure-Befehlszeilenschnittstelle](deploy-to-azure-app-service-using-azure-cli.md).

## <a name="deploy-your-code-into-azure-services"></a>Bereitstellen Ihres Codes in Azure-Diensten

Verwenden Sie Jenkins-Plug-Ins zum Bereitstellen von Anwendungen in Azure als Teil Ihrer Jenkins-CI/CD-Pipelines. Durch das Bereitstellen in [Azure App Service](/azure/app-service/) und [Azure Container Service](/azure/container-service/kubernetes/) können Sie Updates Ihrer Anwendungen ohne Verwalten der zugrunde liegenden Infrastruktur bereitstellen, testen und veröffentlichen.

 Plug-Ins stehen für die Bereitstellung der folgenden Dienste und Umgebungen zur Verfügung:

- [Azure App Service für Linux](/azure/app-service/containers/app-service-linux-intro). Informationen zum Einstieg finden Sie im [Tutorial](deploy-from-github-to-azure-app-service.md).
- [Azure App Service](/azure/app-service/overview). Informationen zum Einstieg finden Sie in der [Anleitung](deploy-to-azure-app-service-using-plugin.md).