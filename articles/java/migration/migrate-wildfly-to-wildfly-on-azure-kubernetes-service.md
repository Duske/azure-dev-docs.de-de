---
title: Migrieren von WildFly-Anwendungen zu WildFly in Azure Kubernetes Service
description: In diesem Leitfaden erfahren Sie, was Sie beachten sollten, wenn Sie eine vorhandene WildFly-Anwendung für die Ausführung unter WildFly in einem Azure Kubernetes Service-Container migrieren möchten.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 3/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf056d9d961662e20f4fa4112a153150bf05460f
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379694"
---
# <a name="migrate-wildfly-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrieren von WildFly-Anwendungen zu WildFly in Azure Kubernetes Service

In diesem Leitfaden erfahren Sie, was Sie beachten sollten, wenn Sie eine vorhandene WildFly-Anwendung für die Ausführung unter WildFly in einem Azure Kubernetes Service-Container migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Bestand: Alle Geheimnisse

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimnisse und Kennwörter. Überprüfen Sie *jboss-web.xml* in Ihren WAR-Dateien. Unter Umständen finden Sie in Ihrer Anwendung auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen.

Es empfiehlt sich gegebenenfalls, diese Geheimnisse in Azure Key Vault zu speichern. Weitere Informationen finden Sie unter [Grundlegende Konzepte von Azure Key Vault](/azure/key-vault/basic-concepts).

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-wildfly](includes/validate-that-the-supported-java-version-works-correctly-wildfly.md)]

### <a name="inventory-jndi-resources"></a>Bestand: JNDI-Ressourcen

Inventarisieren Sie alle JNDI-Ressourcen. Für andere Ressourcen, z. B. JMS-Nachrichtenbroker, ist ggf. eine Migration oder Neukonfiguration erforderlich.

### <a name="determine-whether-session-replication-is-used"></a>Ermitteln, ob die Sitzungsreplikation verwendet wird

Falls Ihre Anwendung die Sitzungsreplikation verwendet, müssen Sie Ihre Anwendung ändern, um diese Abhängigkeit zu entfernen.

#### <a name="inside-your-application"></a>Innerhalb Ihrer Anwendung

Untersuchen Sie die Datei *WEB-INF/jboss-web.xml* und/oder *WEB-INF/web.xml*.

### <a name="document-datasources"></a>Dokumentdatenquellen

Verwendet Ihre Anwendung Datenbanken, müssen Sie die folgenden Informationen erfassen:

* Wie lautet der Name der Datenquelle?
* Wie ist der Verbindungspool konfiguriert?
* Wo ist die JAR-Datei mit den JDBC-Treibern zu finden?

Weitere Informationen finden Sie in der WildFly-Dokumentation unter [DataSource-Konfiguration](http://docs.wildfly.org/19/Admin_Guide.html#DataSource).

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Ermitteln, ob und wie das Dateisystem verwendet wird

Für jegliche Nutzung des Dateisystems auf dem Anwendungsserver sind erneute Konfigurationen oder in selteneren Fällen auch Architekturänderungen erforderlich. Das Dateisystem kann von WildFly-Modulen oder von Ihrem Anwendungscode genutzt werden. Unter Umständen kann es zu den Szenarien kommen, die in den folgenden Abschnitten beschrieben sind.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="determine-whether-jca-connectors-are-in-use"></a>Ermitteln, ob JCA-Connectors genutzt werden

Wenn für Ihre Anwendung JCA-Connectors genutzt werden, müssen Sie überprüfen, ob der JCA-Connector unter WildFly verwendet werden kann. Wenn die JCA-Implementierung mit WildFly verknüpft ist, müssen Sie Ihre Anwendung umgestalten, um diese Abhängigkeit zu beseitigen. Falls die Verwendung möglich ist, müssen Sie die JARs dem Serverklassenpfad hinzufügen und die erforderlichen Konfigurationsdateien in den WildFly-Serververzeichnissen ablegen, um die Verfügbarkeit sicherzustellen.

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>Ermitteln, ob Ihre Anwendung als EAR-Datei gepackt ist

Ist Ihre Anwendung als EAR-Datei gepackt, untersuchen Sie die Datei *application.xml*, und erfassen Sie die Konfiguration.

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
