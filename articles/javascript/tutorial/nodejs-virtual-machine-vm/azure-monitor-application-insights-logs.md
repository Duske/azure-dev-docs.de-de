---
title: Anzeigen von Protokollen im Azure-Portal
description: Hier erfahren Sie, wie Sie Ihre Protokollierung mit Azure Monitor und Application Insights anzeigen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd0741cd58e8d2e882a352035cd3c4f9bca956d1
ms.sourcegitcommit: a2a51e0c6530eb5794a2fe667cf4c9a60b2a7470
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625069"
---
# <a name="6-view-logs-in-azure-portal"></a>6. Anzeigen von Protokollen im Azure-Portal

In diesem Abschnitt des Tutorials erfahren Sie, wie Sie Ihre Protokollierung mit Azure Monitor und Application Insights anzeigen. 

## <a name="count-of-traces-in-logs-with-azure-cli"></a>Anzahl von Ablaufverfolgungen in Protokollen mithilfe der Azure CLI

Mithilfe der Azure CLI können Sie schnell wichtige Informationen Ihrer Protokolle anzeigen. Verwenden Sie beispielsweise den folgenden Befehl, um die Anzahl von Ablaufverfolgungen in den Protokollen anzuzeigen. 

Beachten Sie, dass die Ablaufverfolgung nur in der Route `/trace` hinzugefügt wurde. Bei Aufrufen für den Stamm Ihrer Web-App werden keine Ablaufverfolgungsprotokolle generiert. 

```azurecli
az monitor app-insights metrics show \
    --resource-group rg-demo-vm-eastus \
    --app demoWebAppMonitor \
    --metric traces/count
```

Die Antwort sieht in etwa wie folgt aus. In diesem Beispiel sind insgesamt zwei Ablaufverfolgungen vorhanden: 

```console
{
  "value": {
    "end": "2020-11-11T21:33:40.311000+00:00",
    "interval": null,
    "segments": null,
    "start": "2020-11-11T20:33:40.311000+00:00",
    "traces/count": {
      "sum": 2
    }
  }
}
```

## <a name="view-application-traces-in-azure-portal"></a>Anzeigen von Anwendungsablaufverfolgungen im Azure-Portal

Wenn Sie Ihre Ablaufverfolgungen als Liste anzeigen möchten, lässt sich das am einfachsten über das Azure-Portal bewerkstelligen. 

1. Öffnen Sie das [Azure-Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll) in einem Webbrowser.
1. Filtern Sie die Ressourcenliste nach der Ressourcengruppe `rg-demo-vm-eastus`. 
1. Wählen Sie die Ressource `demoWebAppMonitor` aus. 
1. Wählen Sie im Abschnitt **Überwachung** das Element **Protokolle** aus. Sollte ein Popupelement mit auswählbaren Abfragen angezeigt werden, wählen Sie das **X** in der Ecke aus, um das Popupelement zu schließen.
1. Doppelklicken Sie unter **Application Insights** auf das Element **traces**, um es auszuwählen. Dadurch wird der Name dem Abfragefenster hinzugefügt. 
1. Führen Sie die Abfrage aus, indem Sie die Schaltfläche **Ausführen** auswählen.
1. Die benutzerdefinierten Ablaufverfolgungen von Azure Monitor Application Insights (aus der Web-App) werden in einer Liste angezeigt.

    :::image type="content" source="../../media/tutorial-vm/azure-portal-application-insights-custom-trace.png" alt-text="Die benutzerdefinierten Ablaufverfolgungen von Azure Monitor Application Insights (aus der Web-App) werden in einer Liste angezeigt.":::

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Bereinigen Ihrer Azure-Ressourcen](clean-up-resources.md) 