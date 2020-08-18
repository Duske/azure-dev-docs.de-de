---
title: Bereitstellen in Azure mithilfe von GitHub Actions
description: Hier erfahren Sie, wie Sie in Ihrem Repository Workflows zum Erstellen, Testen, Verpacken und Veröffentlichen sowie zum Bereitstellen in Azure erstellen.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: 29d6f40a54cf503d12b8b7b87b908e7d05a8857d
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982562"
---
# <a name="deploy-to-azure-using-github-actions"></a>Bereitstellen in Azure mithilfe von GitHub Actions

[GitHub Actions](https://help.github.com/articles/about-github-actions) ermöglicht Entwicklern, das Erstellen automatisierter Workflows für den Softwareentwicklungslebenszyklus.  

Mit GitHub Actions für Azure können Sie Workflows erstellen, die Sie in Ihrem Repository zum Erstellen, Testen, Verpacken, Veröffentlichen und **Bereitstellen** in Azure einrichten können. [Weitere Informationen zu allen anderen Integrationen mit Azure](https://aka.ms/GitHubonAzure).

Noch heute mit einem [kostenlosen Azure-Konto](https://azure.com/free/open-source) beginnen!

> [!NOTE]   
> Über die Links in diesem Artikel gelangen Sie jeweils zu einem GitHub-Artikel oder -Repository. 

## <a name="key-concepts"></a>Wichtige Begriffe

Mit GitHub Actions können Sie direkt in Ihrem GitHub-Repository benutzerdefinierte SDLC-Workflows (Software Development Life Cycle, Softwareentwicklungs-Lebenszyklus) erstellen. Eine Übersicht über GitHub Actions und wichtige Konzepte finden Sie in den folgenden Artikeln: 

- [Informationen zu GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)
- [Wichtige Konzepte](https://help.github.com/actions/getting-started-with-github-actions/core-concepts-for-github-actions)
- [Informationen zum Verpacken mit GitHub Actions](https://help.github.com/en/actions/publishing-packages-with-github-actions/about-packaging-with-github-actions)

## <a name="get-started"></a>Erste Schritte 

GitHub Actions enthält vorkonfigurierte Vorlagen und Marketplace-Aktionen. 

- [Verwenden vorkonfigurierter Vorlagen](https://help.github.com/actions/getting-started-with-github-actions/starting-with-preconfigured-workflow-templates)  
- [Verwenden von Aktionen aus dem GitHub-Marketplace](https://help.github.com/en/actions/getting-started-with-github-actions/using-actions-from-github-marketplace)  
- [GitHub-Marketplace-Aktionen: Bereitstellen in Azure](https://github.com/marketplace?type=actions&query=Azure)  
  
GitHub Actions für Azure finden Sie auf den folgenden Seiten: 
   
- [Azure-Aktionen](https://github.com/marketplace?query=Azure&type=actions)  
- [Einsteiger-Aktionsworkflows für die Bereitstellung in Azure](https://github.com/Azure/actions-workflow-samples)


## <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Verwenden Sie die folgenden GitHub-Aktionen, damit Beispielworkflows eine Verbindung mit Azure herstellen und Skripts basierend auf der Azure CLI oder Azure PowerShell ausgeführt werden:  

- [Azure-Anmeldung](https://github.com/Azure/login)  
- [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/CLI)
- [Azure PowerShell](https://github.com/Azure/powershell)


## <a name="sample-apps-with-cicd-workflow-samples"></a>Beispiel-Apps mit CI/CD-Workflowbeispielen 

Die folgenden Beispiele bieten End-to-End-Workflows für die Erstellung und Bereitstellung von Web-Apps in einer beliebigen Sprache und in einem beliebigen Ökosystem in Azure. 

- [Bereitstellen einer Web-App mit ASP.NET-Unterstützung](https://github.com/Azure-Samples/dotnet-sample)  
- [Bereitstellen einer ASP.NET Core-App](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Bereitstellen einer Node.js-Web-App](https://github.com/Azure-Samples/node_express_app)  
- [Bereitstellen einer Java-Web-App](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Bereitstellen einer Java Spring-App](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Bereitstellen einer Python-Web-App](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Bereitstellen einer containerbasierten Web-App mithilfe von Docker](https://github.com/Azure-Samples/Node_express_container)


## <a name="deploy-a-web-app"></a>Bereitstellen einer Web-App

Bereitstellen in Azure-Web-Apps und Azure-Web-App für Container:

- [Aktion: Azure/webapps-deploy](https://github.com/Azure/webapps-deploy)

Bereitstellen einer statischen Web-App:
- [Azure/static-web-apps-deploy](https://docs.microsoft.com/azure/static-web-apps/getting-started?tabs=angular)


Konfigurieren Sie App-Einstellungen und Verbindungszeichenfolgen mithilfe der Aktionen:

- [Azure/appservice-settings](https://github.com/Azure/appservice-settings) 
- [Azure App Service-Einstellungen](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>Bereitstellen einer serverlosen App

- [Azure-Funktionen](https://github.com/Azure/functions-action)  
- [Azure Functions für Container](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>Erstellen und Bereitstellen von Container-Apps

- [Docker-Anmeldung](https://github.com/Azure/docker-login)  
- [Bereitstellen in Azure Container Instances](https://github.com/Azure/aci-deploy)
- [Containerscanaktion](https://github.com/Azure/container-scan)

## <a name="deploy-to-kubernetes"></a>Bereitstellen in Kubernetes

- [Installer für Kubectl-Tool](https://github.com/Azure/setup-kubectl)  
- [Kubernetes: Festlegen des Kontexts](https://github.com/Azure/k8s-set-context)  
- [AKS: Festlegen des Kontexts](https://github.com/Azure/aks-set-context)  
- [Kubernetes: Erstellen eines Geheimnisses](https://github.com/Azure/k8s-create-secret)  
- [Kubernetes: Bereitstellen](https://github.com/Azure/k8s-deploy)  
- [Einrichten von Helm](https://github.com/Azure/setup-helm)  
- [Kubernetes: Baking](https://github.com/Azure/k8s-bake)  

## <a name="train-and-deploy-a-machine-learning-model"></a>Trainieren und Bereitstellen eines Machine Learning-Modells 

- [Anmeldung](https://github.com/Azure/aml-workspace) 
- [Trainieren](https://github.com/Azure/aml-run)
- [Bereitstellen des Modells](https://github.com/Azure/aml-deploy)

## <a name="deploy-to-databases"></a>Bereitstellen in Datenbanken

- [Azure SQL-Datenbank](https://github.com/Azure/sql-action)  
- [Azure MySQL-Aktion](https://github.com/Azure/mysql-action)  

## <a name="azure-policy-integrations"></a>Azure Policy-Integrationen

- [Azure Policy-Konformitätsprüfung](https://github.com/Azure/policy-compliance-scan) 

## <a name="trigger-a-run-in-azure-pipelines"></a>Auslösen einer Ausführung in Azure Pipelines

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>Hilfsprogrammaktionen

- [Variablenersetzung](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>Zusätzliche Ressourcen

Die folgenden GitHub-Ressourcen unterstützen Sie dabei, Ihre Apps unter Verwendung von GitHub in Azure bereitzustellen.  

- [Marketplace für GitHub Actions für Azure](https://github.com/marketplace?query=Azure&type=actions)
- [Learning-Lab: Continuous Delivery mit Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Einsteiger-Aktionsworkflows für die Bereitstellung in Azure](https://github.com/Azure/actions-workflow-samples)
