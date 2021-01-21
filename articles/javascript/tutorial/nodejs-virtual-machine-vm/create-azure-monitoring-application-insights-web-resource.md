---
title: Erstellen einer Azure Monitor-Ressource
description: Hier erfahren Sie, wie Sie eine Azure-Ressourcengruppe für alle Ihre Azure-Ressourcen sowie eine Monitor-Ressource erstellen, um die Protokolldateien Ihrer Web-App in der Azure-Cloud zu sammeln. Azure Monitor ist der Name des Azure-Diensts. Application Insights ist der Name der im Tutorial verwendeten Clientbibliothek.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0b1b634756a5188cbd9233274205005f62b25026
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561046"
---
# <a name="2-create-application-insights-resource-for-web-pages"></a>2. Erstellen einer Application Insights-Ressource für Webseiten

In diesem Schritt des Tutorials werden eine Azure-Ressourcengruppe für alle Ihre Azure-Ressourcen sowie eine Monitor-Ressource erstellt, um die Protokolldateien Ihrer Web-App in der Azure-Cloud zu sammeln. Azure Monitor ist der Name des Azure-Diensts. Application Insights ist der Name der im Tutorial verwendeten Clientbibliothek. 

## <a name="create-a-resource-group-for-your-virtual-machine-resources"></a>Erstellen einer Ressourcengruppe für Ihre VM-Ressourcen

Dieses Tutorial umfasst mehrere Azure-Ressourcen. Durch die Erstellung einer Ressourcengruppe können Sie Ressourcen ganz einfach finden und wieder löschen, wenn Sie sie nicht mehr benötigen.

1. Geben Sie an einem Terminal oder an einer Bash-Shell den [Azure CLI-Befehl zum Erstellen einer Azure-Ressourcengruppe](/cli/azure/group#az_group_create) mit dem Namen `rg-demo-vm-eastus` ein:

    ```azurecli
    az group create \
        --location eastus \
        --name rg-demo-vm-eastus 
    ```

## <a name="create-azure-monitor-resource-with-azure-cli"></a>Erstellen einer Azure Monitor-Ressource mithilfe der Azure CLI

1. Installieren Sie die Application Insights-Erweiterung für die Azure CLI.

    ```azurecli
    az extension add -n application-insights
    ```

1. Verwenden Sie den folgenden Befehl, um eine [Überwachungsressource zu erstellen](/cli/azure/ext/application-insights/monitor/app-insights/component#ext_application_insights_az_monitor_app_insights_component_create):


    ```azurecli
    az monitor app-insights component create \
      --app demoWebAppMonitor \
      --location eastus \
      --resource-group rg-demo-vm-eastus
    ```

    Suchen Sie in den Ergebnissen nach dem Instrumentierungsschlüssel (`instrumentationKey`), und kopieren Sie ihn. Diese benötigen Sie weiter unten. 

1. Lassen Sie das Terminal für den nächsten Schritt geöffnet.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Linux-Computers](create-linux-virtual-machine-azure-cli.md) 
