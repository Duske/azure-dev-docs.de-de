---
title: Migrieren von WebSphere-Anwendungen zu WildFly in Azure Kubernetes Service
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebSphere-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.openlocfilehash: 279051f626c09e63637fdf99b323857f2751b813
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673316"
---
# <a name="migrate-websphere-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrieren von WebSphere-Anwendungen zu WildFly in Azure Kubernetes Service

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebSphere-Anwendung für die Ausführung unter Wildfly in einem Azure Kubernetes Service-Container migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

### <a name="inventory-all-secrets"></a>Bestand: Alle Geheimnisse

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien auf den Produktionsservern auf Geheimnisse und Kennwörter. Überprüfen Sie *ibm-web-bnd.xml* in Ihren WAR-Dateien. Unter Umständen finden Sie in Ihrer Anwendung auch Konfigurationsdateien mit Kennwörtern oder Anmeldeinformationen.

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Überprüfen, ob die unterstützte Java-Version richtig funktioniert

Für die Verwendung von WildFly in Azure Kubernetes Service ist eine spezifische Version von Java erforderlich. Sie müssen daher überprüfen, ob Ihre Anwendung mit dieser unterstützten Version richtig ausgeführt werden kann. Diese Überprüfung ist besonders wichtig, wenn für Ihren aktuellen Server ein nicht unterstütztes JDK (z. B. Oracle JDK oder IBM OpenJ9) verwendet wird.

Melden Sie sich an Ihrem Produktionsserver an, und führen Sie Folgendes aus, um Ihre aktuelle Version zu ermitteln:

```bash
java -version
```

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

#### <a name="read-only-static-content"></a>Schreibgeschützter statischer Inhalt

Falls mit Ihrer Anwendung derzeit statischer Inhalt bereitgestellt wird, benötigen Sie dafür einen anderen Speicherort. Sie können beispielsweise erwägen, statischen Inhalt in Azure Blob Storage zu verschieben und Azure CDN hinzuzufügen, um global eine sehr hohe Downloadgeschwindigkeit zu erzielen. Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website)und[Schnellstart: Integrieren eines Azure-Speicherkontos in Azure CDN](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Dynamisch veröffentlichter statischer Inhalt

Wenn Ihre Anwendung statischen Inhalt zulässt, der von Ihrer Anwendung hochgeladen bzw. produziert wird, nach der Erstellung aber unveränderlich ist, können Sie Azure Blob Storage und Azure CDN wie oben beschrieben nutzen. Hierbei können Sie auch eine Azure-Funktion zum Verarbeiten von Uploads und der CDN-Aktualisierung verwenden. Eine entsprechende Beispielimplementierung finden Sie unter [Hochladen und CDN-Vorabladen von statischem Inhalt mit Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="dynamic-or-internal-content"></a>Dynamischer oder interner Inhalt

Für Dateien, für die von Ihrer Anwendung häufige Schreib- und Lesevorgänge durchgeführt werden (z. B. temporäre Datendateien), oder für statische Dateien, die nur für Ihre Anwendung sichtbar sind, können Sie Azure Storage-Freigaben als persistente Volumes bereitstellen. Weitere Informationen finden Sie unter [Dynamisches Erstellen und Verwenden eines persistenten Volumes mit Azure Files in Azure Kubernetes Service (AKS)](/azure/aks/azure-files-dynamic-pv).

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
