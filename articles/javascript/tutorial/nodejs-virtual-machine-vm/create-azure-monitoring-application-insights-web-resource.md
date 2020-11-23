---
title: Erstellen einer Azure Monitor-Ressource
description: Hier erfahren Sie, wie Sie eine Azure-Ressourcengruppe für alle Ihre Azure-Ressourcen sowie eine Monitor-Ressource erstellen, um die Protokolldateien Ihrer Web-App in der Azure-Cloud zu sammeln. Azure Monitor ist der Name des Azure-Diensts. Application Insights ist der Name der im Tutorial verwendeten Clientbibliothek.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: b401953c0e1c972efa0f5d90817f461b858cf04b
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625039"
---
# <a name="2-create-application-insights-resource-for-web-pages"></a>2. Erstellen einer Application Insights-Ressource für Webseiten

In diesem Schritt des Tutorials werden eine Azure-Ressourcengruppe für alle Ihre Azure-Ressourcen sowie eine Monitor-Ressource erstellt, um die Protokolldateien Ihrer Web-App in der Azure-Cloud zu sammeln. Azure Monitor ist der Name des Azure-Diensts. Application Insights ist der Name der im Tutorial verwendeten Clientbibliothek. 

## <a name="create-a-resource-group-for-your-virtual-machine-resources"></a>Erstellen einer Ressourcengruppe für Ihre VM-Ressourcen

Dieses Tutorial umfasst mehrere Azure-Ressourcen. Durch die Erstellung einer Ressourcengruppe können Sie Ressourcen ganz einfach finden und wieder löschen, wenn Sie sie nicht mehr benötigen.

1. Geben Sie an einem Terminal oder an einer Bash-Shell den [Azure CLI-Befehl zum Erstellen einer Azure-Ressourcengruppe](/cli/azure/group?view=azure-cli-latest#az_group_create) mit dem Namen `rg-demo-vm-eastus` ein:

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

1. Verwenden Sie den folgenden Befehl, um eine [Überwachungsressource zu erstellen](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext_application_insights_az_monitor_app_insights_component_create):


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
