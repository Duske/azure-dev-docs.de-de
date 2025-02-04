---
title: Migrieren von WebLogic-Anwendungen zu WildFly in Azure Kubernetes Service
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebLogic-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8a55c9e7aaffba5a65f2d195763900aac3f3c336
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738431"
---
# <a name="migrate-weblogic-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrieren von WebLogic-Anwendungen zu WildFly in Azure Kubernetes Service

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebLogic-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

Falls Sie keine Anforderungen in Bezug auf die Migrationsvorbereitung erfüllen können, helfen Ihnen die Informationen im Migrationsleitfaden weiter:

* [Migrieren von WebLogic-Anwendungen zu virtuellen Azure-Computern](migrate-weblogic-to-virtual-machines.md)

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

### <a name="determine-whether-session-replication-is-used"></a>Ermitteln, ob die Sitzungsreplikation verwendet wird

Wenn für Ihre Anwendung die Sitzungsreplikation benötigt wird, haben Sie zwei Optionen (mit oder ohne Oracle Coherence*Web):

* Gestalten Sie Ihre Anwendung so um, dass eine Datenbank für die Sitzungsverwaltung verwendet wird.
* Gestalten Sie Ihre Anwendung so um, dass die Sitzung für den Azure Redis-Dienst externalisiert wird. Weitere Informationen finden Sie unter [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

<!-- AKS-specific extension of the last INCLUDE. -->
> [!NOTE]
> Falls Sie Ihre Webanwendungen einzeln skalieren möchten, um eine bessere Nutzung Ihrer AKS-Ressourcen zu erzielen, sollten Sie die EAR-Datei in separate Webanwendungen unterteilen.
<!-- end extension -->

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

### <a name="determine-whether-weblogic-scripting-tool-wlst-is-used"></a>Ermitteln, ob das WebLogic-Skriptingtool (WLST) verwendet wird

Wenn Sie derzeit das WLST für die Bereitstellung verwenden, müssen Sie sich mit den WLST-Vorgängen auseinandersetzen. Falls vom WLST im Rahmen der Bereitstellung (Laufzeit-)Parameter Ihrer Anwendung geändert werden, müssen Sie sicherstellen, dass diese Parameter eine der folgenden Bedingungen erfüllen:

* Sie werden als App-Einstellungen externalisiert.
* Sie sind in Ihre Anwendung eingebettet.
* Während der Bereitstellung wird die JBoss CLI genutzt.

Falls vom WLST mehr Vorgänge als oben beschrieben durchgeführt werden, müssen Sie während der Migration einige zusätzliche Schritte ausführen.

### <a name="determine-whether-your-application-uses-weblogic-specific-apis"></a>Ermitteln, ob für Ihre Anwendung WebLogic-spezifische APIs verwendet werden

Wenn für Ihre Anwendung WebLogic-spezifische APIs verwendet werden, müssen Sie sie umgestalten, um diese Abhängigkeiten zu beseitigen. Wenn Sie beispielsweise eine Klasse verwendet haben, die in der [Java-API-Referenz für Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlapi/index.html?overview-summary.html) erwähnt wird, haben Sie in Ihrer Anwendung eine WebLogic-spezifische API genutzt.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

### <a name="determine-whether-a-deployment-plan-was-used"></a>Ermitteln, ob ein Bereitstellungsplan verwendet wurde

Wenn Ihre App basierend auf einem Bereitstellungsplan bereitgestellt wurde, müssen Sie bewerten, was dieser Plan bewirkt. Wenn es im Bereitstellungsplan um eine direkte Bereitstellung geht, können Sie Ihre Webanwendung ohne weitere Änderungen bereitstellen. Falls der Bereitstellungsplan komplexer ist, müssen Sie ermitteln, ob Sie die JBoss CLI nutzen können, um Ihre Anwendung im Rahmen der Bereitstellung richtig zu konfigurieren. Falls die Nutzung der JBoss CLI nicht möglich ist, müssen Sie Ihre Anwendung so umgestalten, dass kein Bereitstellungsplan mehr benötigt wird.

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Ermitteln, ob und wie das Dateisystem verwendet wird

Für jegliche Nutzung des Dateisystems auf dem Anwendungsserver sind erneute Konfigurationen oder in selteneren Fällen auch Architekturänderungen erforderlich. Das Dateisystem kann von freigegebenen WebLogic-Modulen oder von Ihrem Anwendungscode genutzt werden. Unter Umständen kann es zu den Szenarien kommen, die in den folgenden Abschnitten beschrieben sind.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

### <a name="determine-whether-jca-connectors-are-used"></a>Ermitteln, ob JCA-Connectors genutzt werden

Wenn für Ihre Anwendung JCA-Connectors genutzt werden, müssen Sie überprüfen, ob der JCA-Connector unter WildFly verwendet werden kann. Wenn die JCA-Implementierung an WebLogic gebunden ist, müssen Sie Ihre Anwendung so umgestalten, dass diese Abhängigkeit beseitigt wird. Falls die Verwendung möglich ist, müssen Sie die JARs dem Serverklassenpfad hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

### <a name="determine-whether-weblogic-clustering-is-used"></a>Ermitteln, ob das WebLogic-Clustering genutzt wird

Höchstwahrscheinlich haben Sie Ihre Anwendung auf mehreren WebLogic-Servern bereitgestellt, um Hochverfügbarkeit zu erzielen. Für Azure Kubernetes Service ist eine Skalierung möglich, aber wenn Sie die WebLogic-Cluster-API verwendet haben, müssen Sie Ihren Code umgestalten, um die Nutzung dieser API zu entfernen.

[!INCLUDE [perform-in-place-testing](includes/perform-in-place-testing.md)]

## <a name="migration"></a>Migration

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Konfigurieren von persistentem Speicher

Konfigurieren Sie ein oder mehrere [persistente Volumes](/azure/aks/azure-disks-dynamic-pv), wenn für Ihre Anwendung nicht flüchtiger Speicher benötigt wird.

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie Ihre Anwendung nun zu Azure Kubernetes Service migriert haben, sollten Sie sich vergewissern, dass sie wie erwartet funktioniert. Lesen Sie anschließend die folgenden Empfehlungen dazu, wie Sie Ihre Anwendung cloudnativer gestalten können.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
