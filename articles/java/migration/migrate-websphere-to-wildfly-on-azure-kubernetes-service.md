---
title: Migrieren von WebSphere-Anwendungen zu WildFly in Azure Kubernetes Service
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebSphere-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.custom: devx-track-java
ms.openlocfilehash: 5f31107a192cd36d79d3fb664c49f019bad7e972
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738515"
---
# <a name="migrate-websphere-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrieren von WebSphere-Anwendungen zu WildFly in Azure Kubernetes Service

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebSphere-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Bestand: Alle Geheimnisse

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimnisse und Kennwörter. Überprüfen Sie *ibm-web-bnd.xml* in Ihren WAR-Dateien. Unter Umständen finden Sie in Ihrer Anwendung auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen.

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>Bestand: JNDI-Ressourcen

Inventarisieren Sie alle JNDI-Ressourcen. Für andere Ressourcen, z. B. JMS-Nachrichtenbroker, ist ggf. eine Migration oder Neukonfiguration erforderlich.

#### <a name="inside-your-application"></a>Innerhalb Ihrer Anwendung

Untersuchen Sie die Datei *WEB-INF/ibm-web-bnd.xml* bzw. *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Dokumentdatenquellen

Verwendet Ihre Anwendung Datenbanken, müssen Sie die folgenden Informationen erfassen:

* Wie lautet der Name der Datenquelle?
* Wie ist der Verbindungspool konfiguriert?
* Wo ist die JAR-Datei mit den JDBC-Treibern zu finden?

Weitere Informationen finden Sie in der WebSphere-Dokumentation unter [Konfigurieren der Datenbankkonnektivität](https://www.ibm.com/support/knowledgecenter/SSQP76_8.10.x/com.ibm.odm.distrib.config.was/config_dc_websphere/tpc_was_create_datasrc_cpl.html).

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Ermitteln, ob und wie das Dateisystem verwendet wird

Für jegliche Nutzung des Dateisystems auf dem Anwendungsserver sind erneute Konfigurationen oder in selteneren Fällen auch Architekturänderungen erforderlich. Das Dateisystem kann von WebSphere-Modulen oder von Ihrem Anwendungscode genutzt werden. Unter Umständen kann es zu den Szenarien kommen, die in den folgenden Abschnitten beschrieben sind.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

### <a name="determine-whether-your-application-uses-websphere-specific-apis"></a>Ermitteln, ob für Ihre Anwendung WebSphere-spezifische APIs verwendet werden

Wenn für Ihre Anwendung WebSphere-spezifische APIs verwendet werden, müssen Sie sie umgestalten, um diese Abhängigkeiten zu beseitigen. Wenn Sie beispielsweise eine Klasse verwendet haben, die in der [Spezifikation zum IBM WebSphere-Anwendungsserver (Release 9.0)](https://www.ibm.com/support/knowledgecenter/en/SSEQTJ_9.0.5/com.ibm.websphere.javadoc.doc/web/apidocs/overview-summary.html?view=embed) erwähnt wird, haben Sie in Ihrer Anwendung eine WebSphere-spezifische API genutzt.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Ermitteln, ob JCA-Connectors genutzt werden

Wenn für Ihre Anwendung JCA-Connectors genutzt werden, müssen Sie überprüfen, ob der JCA-Connector unter WildFly verwendet werden kann. Wenn die JCA-Implementierung an WebSphere gebunden ist, müssen Sie Ihre Anwendung so umgestalten, dass diese Abhängigkeit beseitigt wird. Falls die Verwendung möglich ist, müssen Sie die JARs dem Serverklassenpfad hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Ermitteln, ob Ihre Anwendung als EAR-Datei gepackt ist

Ist Ihre Anwendung als EAR-Datei gepackt, sollten Sie sich unbedingt die Dateien *application.xml* und *application-bnd.xml* ansehen und deren Konfiguration erfassen.

> [!NOTE]
> Falls Sie Ihre Webanwendungen einzeln skalieren möchten, um eine bessere Nutzung Ihrer AKS-Ressourcen zu erzielen, sollten Sie die EAR-Datei in separate Webanwendungen unterteilen.

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

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

Nachdem Sie Ihre Anwendung zu Azure Kubernetes Service migriert haben, sollten Sie sich vergewissern, dass sie wie erwartet funktioniert. Anschließend können Sie sich dann über unsere Empfehlungen informieren, mit denen Sie Ihre Anwendung cloudnativer gestalten können.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]
