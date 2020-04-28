---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 1ffc12fa2a99e2abdbbc43e0024afd8e5e98c4b9
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673256"
---
### <a name="recommendations"></a>Empfehlungen

* Erwägen Sie, einen DNS-Namen der IP-Adresse hinzuzufügen, die Ihrem Eingangscontroller oder dem Lastenausgleichsmodul der Anwendung zugeordnet ist. Weitere Informationen finden Sie unter [Erstellen eines Eingangscontrollers mit einer statischen öffentlichen IP-Adresse in AKS](/azure/aks/ingress-static-ip).

* Erwägen Sie, [HELM-Diagramme ](https://helm.sh/docs/topics/charts/) für Ihre Anwendung hinzuzufügen. Mit einem Helm-Diagramm können Sie Ihre Anwendungsbereitstellung parametrisieren, damit diese von unterschiedlichen Kunden genutzt werden kann.

* Entwerfen und implementieren Sie eine DevOps-Strategie. Sie können Bereitstellungen automatisieren und mit Azure Pipelines testen, um die Zuverlässigkeit sicherzustellen, während gleichzeitig die Entwicklungsgeschwindigkeit erhöht wird. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen im AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Aktivieren Sie die Azure-Überwachung für den Cluster. Weitere Informationen finden Sie unter [Aktivieren der Überwachung eines bereits bereitgestellten AKS-Clusters](/azure/azure-monitor/insights/container-insights-enable-existing-clusters). Azure Monitor kann dann Containerprotokolle erfassen, die Nutzung nachverfolgen usw.

* Erwägen Sie, anwendungsspezifische Metriken über Prometheus verfügbar zu machen. Prometheus ist ein Open-Source-Framework für Metriken, das von der Kubernetes-Community viel genutzt wird. Sie können die Erfassung von Prometheus-Metriken in Azure Monitor konfigurieren, anstatt Ihren eigenen Prometheus-Server zu hosten. So ermöglichen Sie die Aggregation von Metriken aus Ihren Anwendungen und die automatisierte Reaktion auf anomale Bedingungen bzw. deren Eskalation. Weitere Informationen finden Sie unter [Konfigurieren des Abrufs von Prometheus-Metriken mit Azure Monitor für Container](/azure/azure-monitor/insights/container-insights-prometheus-integration).

* Entwerfen und implementieren Sie eine Strategie für Geschäftskontinuität und Notfallwiederherstellung. Bei unternehmenskritischen Anwendungen sollten Sie erwägen, eine Bereitstellungsarchitektur mit mehreren Regionen zu verwenden. Weitere Informationen finden Sie unter [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)](/azure/aks/operator-best-practices-multi-region).

* Informieren Sie sich über die [Richtlinie zur Unterstützung der Kubernetes-Version](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). Wir sind dafür verantwortlich, Ihren AKS-Cluster zu aktualisieren und auf dem neuesten Stand zu halten, damit sichergestellt ist, dass immer eine unterstützte Version ausgeführt wird. Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters](/azure/aks/upgrade-cluster).

* Sorgen Sie dafür, dass alle Teammitglieder, die für die Clusterverwaltung und Anwendungsentwicklung zuständig sind, über die entsprechenden bewährten Methoden für AKS informiert sind. Weitere Informationen finden Sie unter [Best Practices für Clusterbetreiber und -entwickler zum Erstellen und Verwalten von Anwendungen in AKS](/azure/aks/best-practices).

* Stellen Sie sicher, dass in Ihrer Bereitstellungsdatei angegeben ist, wie parallele Updates durchgeführt werden. Weitere Informationen finden Sie in der Kubernetes-Dokumentation unter [Bereitstellung von parallelen Updates](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment).

* Richten Sie die automatische Skalierung ein, damit hohe Auslastungen zu Spitzenzeiten bewältigt werden können. Weitere Informationen hierzu finden Sie unter [Automatisches Skalieren eines Clusters zur Erfüllung von Anwendungsanforderungen in AKS](/azure/aks/cluster-autoscaler).

* Sie haben auch die Möglichkeit, die Größe des Codecaches zu überwachen und die JVM-Parameter `-XX:InitialCodeCacheSize` und `-XX:ReservedCodeCacheSize` im Dockerfile hinzuzufügen, um die Leistung weiter zu optimieren. Weitere Informationen finden Sie in der Oracle-Dokumentation unter [Codecache-Optimierung](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm).
