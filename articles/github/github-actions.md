---
title: Worum handelt es sich bei GitHub Actions für Azure?
description: Hier erfahren Sie, wie Sie in Ihrem Repository Workflows zum Erstellen, Testen, Verpacken und Veröffentlichen sowie zum Bereitstellen in Azure erstellen.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 10/30/2020
ms.custom: github-actions-azure
ms.openlocfilehash: c952410231ae57b0d127e1b6fb05e03f88403640
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754066"
---
# <a name="what-is-github-actions-for-azure"></a>Worum handelt es sich bei GitHub Actions für Azure?

[GitHub Actions](https://help.github.com/articles/about-github-actions) unterstützt Sie beim Automatisieren Ihrer Softwareentwicklungsworkflows innerhalb von GitHub. Sie können Workflows an demselben Ort bereitstellen, an dem Sie Code speichern und an Pull Requests und Problemen zusammenarbeiten.

In GitHub Actions ist ein [Workflow](https://help.github.com/articles/about-github-actions#workflow) ein automatisierter Prozess, den Sie in Ihrem GitHub-Repository einrichten. Sie können jedes Projekt auf GitHub mit einem Workflow erstellen, testen, verpacken, freigeben oder bereitstellen.

Jeder Workflow besteht aus einzelnen [Aktionen](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions), die nach einem bestimmten Ereignis (etwa einem Pull Request) ausgeführt werden.  Die einzelnen Aktionen sind verpackte Skripts, mit denen Softwareentwicklungsaufgaben automatisiert werden.

Mit GitHub Actions für Azure können Sie Workflows erstellen, die Sie in Ihrem Repository zum Erstellen, Testen, Verpacken, Veröffentlichen und Bereitstellen in Azure einrichten können. GitHub Actions für Azure unterstützt Azure-Dienste, u. a. Azure App Service, Azure Functions und Azure Key Vault.

GitHub Actions beinhaltet darüber hinaus Unterstützung für Hilfsprogramme wie Azure Resource Manager-Vorlagen, die Azure CLI und Azure Policy.

Sehen Sie sich dieses Video aus GitHub Universe 2020 an, um mehr über Continuous Delivery mit GitHub Actions zu erfahren.  

> [!VIDEO https://www.youtube.com/embed/36hY0-O4STg]

## <a name="why-should-i-use-github-actions-for-azure"></a>Gründe für die Verwendung von GitHub Actions für Azure

GitHub Actions für Azure wird von Microsoft für die Verwendung mit Azure entwickelt. Sie finden alle Aktionen von GitHub Actions für Azure im [GitHub-Marketplace](https://github.com/marketplace?query=Azure&type=actions). Weitere Informationen zum Integrieren von Aktionen in Ihre Workflows finden Sie unter [Suchen und Anpassen von Aktionen](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/finding-and-customizing-actions).

## <a name="what-is-the-difference-between-github-actions-and-azure-pipelines"></a>Worin besteht der Unterschied zwischen GitHub Actions und Azure Pipelines?

Sowohl Azure Pipelines als auch GitHub Actions unterstützt Sie bei der Automatisierung von Softwareentwicklungsworkflows. [Hier](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/migrating-from-azure-pipelines-to-github-actions) finden Sie weitere Informationen zu den Unterschieden zwischen den Diensten sowie zur Migration von Azure Pipelines zu GitHub Actions.

## <a name="what-do-i-need-to-use-github-actions-for-azure"></a>Was wird für die Verwendung von GitHub Actions für Azure benötigt?

Sie benötigen Azure- und GitHub-Konten:

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein GitHub-Konto. Falls Sie noch nicht über ein Konto verfügen, können Sie sich [kostenlos](https://github.com/join) registrieren.  

## <a name="how-do-i-connect-github-actions-and-azure"></a>Wie stelle ich eine Verbindung zwischen GitHub Actions und Azure her?

Abhängig von der Aktion verwenden Sie einen Dienstprinzipal oder ein Veröffentlichungsprofil, um über GitHub eine Verbindung mit Azure herzustellen. Sie nutzen bei jeder Verwendung der Aktion [Azure-Anmeldung](https://github.com/marketplace/actions/azure-login) einen Dienstprinzipal. Die [Azure App Service-Aktion](https://github.com/marketplace/actions/azure-webapp) unterstützt die Verwendung eines Veröffentlichungsprofils oder Dienstprinzipals. Weitere Informationen zu Dienstprinzipalen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).  

Sie können die Aktion „Azure-Anmeldung“ in Kombination mit den [Azure CLI](https://github.com/marketplace/actions/azure-cli-action)- und [Azure PowerShell](https://github.com/marketplace/actions/azure-powershell-action)-Aktionen verwenden. Die Aktion „Azure-Anmeldung“ funktioniert auch mit den meisten anderen GitHub-Aktionen für Azure, u. a. mit der Aktion für die [Bereitstellung in Web-Apps](https://github.com/marketplace/actions/azure-webapp) und der Aktion für den [Zugriff auf Key Vault-Geheimnisse](https://github.com/marketplace/actions/azure-key-vault-get-secrets).

## <a name="what-is-included-in-a-github-actions-workflow"></a>Was ist in einem GitHub Actions-Workflow enthalten?

Workflows bestehen aus mindestens einem Auftrag. Ein Auftrag enthält Schritte, die aus einzelnen Aktionen bestehen. Weitere Informationen zu GitHub Actions-Konzepten finden Sie unter [Einführung in GitHub Actions](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions).  

## <a name="where-can-i-see-complete-workflow-examples"></a>Wo finde ich umfassende Workflowbeispiele?

Das [Azure-Repository mit Einsteiger-Aktionsworkflows](https://github.com/Azure/actions-workflow-samples) enthält End-to-End-Workflows für die Erstellung und Bereitstellung von Web-Apps in einer beliebigen Sprache und in einem beliebigen Ökosystem in Azure.

## <a name="where-can-i-see-all-the-available-actions"></a>Wo kann ich alle verfügbaren Aktionen anzeigen?

Sie können alle verfügbaren Aktionen von GitHub Actions für Azure im [Marketplace für GitHub Actions für Azure](https://github.com/marketplace?query=Azure&type=actions) anzeigen.

* [Bereitstellen in einer statischen Web-App](/azure/static-web-apps/getting-started?tabs=angular)
* [Azure App Service-Einstellungen](https://github.com/Azure/appservice-settings)  
* [Bereitstellen in Azure Functions](https://github.com/Azure/functions-action)  
* [Bereitstellen in Azure Functions für Container](https://github.com/Azure/webapps-container-deploy)  
* [Docker-Anmeldung](https://github.com/Azure/docker-login)  
* [Bereitstellen in Azure Container Instances](https://github.com/Azure/aci-deploy)
* [Containerscanaktion](https://github.com/Azure/container-scan)
* [Installer für Kubectl-Tool](https://github.com/Azure/setup-kubectl)  
* [Kubernetes: Festlegen des Kontexts](https://github.com/Azure/k8s-set-context)  
* [AKS: Festlegen des Kontexts](https://github.com/Azure/aks-set-context)  
* [Kubernetes: Erstellen eines Geheimnisses](https://github.com/Azure/k8s-create-secret)  
* [Kubernetes: Bereitstellen](https://github.com/Azure/k8s-deploy)  
* [Einrichten von Helm](https://github.com/Azure/setup-helm)  
* [Kubernetes: Baking](https://github.com/Azure/k8s-bake)  
* [Erstellen von Images virtueller Azure-Computer](https://github.com/Azure/build-vm-image)
* [Machine Learning-Anmeldung](https://github.com/Azure/aml-workspace)
* [Machine Learning-Training](https://github.com/Azure/aml-run)
* [Machine Learning: Bereitstellen von Modellen](https://github.com/Azure/aml-deploy)
* [Bereitstellen in Azure SQL-Datenbank](https://github.com/Azure/sql-action)  
* [Aktion zum Bereitstellen in Azure MySQL](https://github.com/Azure/mysql-action)  
* [Azure Policy-Konformitätsprüfung](https://github.com/Azure/policy-compliance-scan)
* [Verwalten von Azure Policy](https://github.com/Azure/manage-azure-policy)
* [Auslösen einer Azure Pipelines-Ausführung](https://github.com/Azure/pipelines)  
* [Variablenersetzung](https://github.com/Microsoft/variable-substitution)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Lernpfad: Automatisieren des Workflows mit GitHub Actions](https://docs.microsoft.com/learn/modules/github-actions-automate-tasks/)

> [!div class="nextstepaction"]
> [Learning-Lab: Continuous Delivery mit Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
