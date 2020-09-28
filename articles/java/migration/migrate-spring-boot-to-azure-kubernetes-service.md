---
title: Migrieren von Spring Boot-Anwendungen für die Ausführung in Azure Kubernetes Service
description: In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Boot-Anwendung für die Ausführung in einem Azure Kubernetes Service-Container migrieren möchten.
author: mnriem
ms.author: manriem
ms.topic: conceptual
ms.date: 4/10/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4d3da50042074b724f614b718ceb0edc7fb83077
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831706"
---
# <a name="migrate-spring-boot-applications-to-azure-kubernetes-service"></a>Migrieren von Spring Boot-Anwendungen zu Azure Kubernetes Service

In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Boot-Anwendung für die Ausführung in Azure Kubernetes Service (AKS) migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Überprüfen, ob die unterstützte Java-Version richtig funktioniert

Es empfiehlt sich, eine unterstützte Version von Java zu verwenden, wenn Sie eine Spring Boot-Anwendung in AKS ausführen. Vergewissern Sie sich, dass Ihre Anwendung mit dieser unterstützten Version ordnungsgemäß funktioniert.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Ermitteln, ob und wie das Dateisystem verwendet wird

Für jegliche Nutzung des Dateisystems durch Ihre Spring Boot-Anwendung sind Konfigurations- und in selteneren Fällen Architekturänderungen erforderlich. Unter Umständen kann es zu den Szenarien kommen, die in den folgenden Abschnitten beschrieben sind.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Gehen Sie bei Anwendungen, die Spring Boot 1.x verwenden, wie im [Migrationshandbuch für Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) beschrieben vor, um sie auf eine unterstützte Spring Boot-Version zu aktualisieren.

### <a name="review-your-database-properties"></a>Überprüfen Ihrer Datenbankeigenschaften

Falls Ihre Anwendung eine Datenbank verwendet, überprüfen Sie die Datenbankeigenschaften in der Datei *application.properties*, und vergewissern Sie sich, dass Ihre Spring Boot-Anwendung nach der Migration zu AKS weiterhin auf die Datenbank zugreifen kann. Im Falle einer lokalen Datenbank muss entweder eine Cloudmigration durchgeführt oder eine Verbindung mit Ihrer lokalen Datenbank hergestellt werden.

### <a name="identify-log-aggregation-solutions"></a>Ermitteln von Protokollaggregationslösungen

Ermitteln Sie alle Protokollaggregationslösungen, die ggf. von den zu migrierenden Anwendungen verwendet werden.

### <a name="identify-application-performance-management-apm-agents"></a>Ermitteln von APM-Agents (Application Performance Management, Anwendungsleistungsverwaltung)

Ermitteln Sie alle APM-Agents, die ggf. für Ihre Anwendungen verwendet werden (beispielsweise Dynatrace und Datadog). Sie müssen diese APM-Agents neu konfigurieren, um sie in ein Dockerfile oder in eine Jib-Konfiguration einzuschließen, oder den In-Process-Java-Agent von Application Insights verwenden.

### <a name="identify-zipkin-dependencies"></a>Ermitteln von Zipkin-Abhängigkeiten

Ermitteln Sie, ob Ihre Anwendung explizite Zipkin-Abhängigkeiten aufweist. Suchen Sie in Ihren Maven- oder Gradle-Abhängigkeiten nach Abhängigkeiten von der Gruppe `io.zipkin.java`.

### <a name="inventory-external-resources"></a>Bestand: Externe Ressourcen

Identifizieren Sie externe Ressourcen, z. B. Datenquellen, JMS-Nachrichtenbroker und URLs anderer Dienste. Bei Spring Boot-Anwendungen befindet sich die Konfiguration solcher Ressourcen üblicherweise im Ordner *src/main/directory* in einer Datei namens *application.properties* oder *application.yml*. Überprüfen Sie außerdem die Umgebungsvariablen der Produktionsbereitstellung auf relevante Konfigurationseinstellungen.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Nachdem Sie den oder die verwendeten Broker ermittelt haben, können Sie nach den entsprechenden Einstellungen suchen. Bei Spring Boot-Anwendungen befinden sich diese in der Regel im Anwendungsverzeichnis in den Dateien *application.properties* und *application.yml*.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

#### <a name="identity-providers"></a>Identitätsanbieter

Ermitteln Sie alle Identitätsanbieter und alle Spring Boot-Anwendungen, für die eine Authentifizierung und/oder Autorisierung erforderlich ist. Informationen zum Konfigurieren von Identitätsanbietern finden Sie in den folgenden Artikeln:

* Informationen zur OAuth-/OAuth2-Spring Security-Konfiguration finden Sie unter [Spring Security](https://spring.io/projects/spring-security).
* Informationen zur Auth0-Spring Security-Konfiguration finden Sie in der [Auth0-Spring Security-Dokumentation](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Informationen zur PingFederate-Spring Security-Konfiguration finden Sie in der [Auth0-PingFederate-Anleitung](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-vmware-tanzu-application-service-tas-formerly-pivotal-cloud-foundry"></a>Über VMware Tanzu Application Service (TAS) (vormals Pivotal Cloud Foundry) konfigurierte Ressourcen

Bei Anwendungen, die mit TAS verwaltet werden, werden externe Ressourcen (einschließlich der weiter oben beschriebenen) häufig über TAS-Dienstbindungen konfiguriert. Verwenden Sie zum Untersuchen der Konfiguration solcher Ressourcen die [TAS-Befehlszeilenschnittstelle (Cloud Foundry)](https://docs.cloudfoundry.org/cf-cli/), um die Variable `VCAP_SERVICES` für die Anwendung anzuzeigen.

```bash
# Log into TAS, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <Organization Name>
cf target space <Space Name>

# Display variables for the application
cf env <Application Name>
```

Überprüfen Sie die Variable `VCAP_SERVICES` auf Konfigurationseinstellungen von externen Diensten, die an die Anwendung gebunden sind. Weitere Informationen finden Sie in der [Dokumentation zu TAS (Cloud Foundry)](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

### <a name="in-place-testing"></a>Direktes Testen

Migrieren Sie Ihre Anwendung vor der Erstellung von Containerimages zu dem JDK und der Spring Boot-Version, das bzw. die Sie unter AKS nutzen möchten. Führen Sie für Ihre Anwendung gründliche Tests durch, um die Kompatibilität und Leistung sicherzustellen.

## <a name="migration"></a>Migration

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

### <a name="create-a-docker-image-for-spring-boot"></a>Erstellen eines Docker-Images für Spring Boot

Zum Erstellen eines Dockerfile benötigen Sie Folgendes:

* Ein unterstütztes JDK.
* Ihre JVM-Runtimeoptionen.
* Eine Möglichkeit zum Übergeben von Umgebungsvariablen (falls zutreffend).

Bei Bedarf können Sie anschließend die in den folgenden Abschnitten beschriebenen Schritte ausführen. Sie können das [Schnellstartrepository für Spring Boot-Container](https://github.com/Azure/spring-boot-container-quickstart) als Ausgangspunkt für Ihr Dockerfile und Ihre Spring Boot-Anwendung verwenden.

#### <a name="configure-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Konfigurieren des Azure Key Vault-Anbieters für den Secrets Store CSI-Treiber

Erstellen Sie eine Azure Key Vault-Instanz, und fügen Sie alle erforderlichen Geheimnisse ein. Weitere Informationen finden Sie unter [Quickstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI](/azure/key-vault/quick-create-cli). Konfigurieren Sie dann den [Azure Key Vault-Anbieter für den Secrets Store CSI-Treiber](https://github.com/Azure/secrets-store-csi-driver-provider-azure), um diese Geheimnisse für Pods zugänglich zu machen.

Außerdem müssen Sie das Startskript aktualisieren, das für das Bootstrapping für Ihre Spring Boot-Anwendung verwendet wird. Mit diesem Skript müssen die Zertifikate vor dem Start der Anwendung in den von Spring Boot genutzten Keystore importiert werden.

### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Erstellen und Pushen des Docker-Images für Azure Container Registry

Nachdem Sie das Dockerfile erstellt haben, müssen Sie das Docker-Image erstellen und in Ihrer Azure Container Registry-Instanz veröffentlichen.

Wenn Sie unser [GitHub-Schnellstartrepository für Spring Boot-Container](https://github.com/Azure/spring-boot-container-quickstart) verwendet haben, entspricht der Prozess zum Erstellen und Pushen Ihres Images für Ihre Azure Container Registry-Instanz dem Aufrufen der folgenden drei Befehle.

In diesen Beispielen enthält die Umgebungsvariable `MY_ACR` den Namen Ihrer Azure Container Registry-Instanz und die Variable `MY_APP_NAME` den Namen der Webanwendung, die Sie in Ihrer Azure Container Registry-Instanz verwenden möchten.

Erstellen der Bereitstellungsdatei:

```bash
mvn package
```

Anmelden bei Ihrer Azure Container Registry-Instanz:

```azurecli
az acr login -n ${MY_ACR}
```

Erstellen und Pushen des Images:

```azurecli
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} .
```

Alternativ können Sie die Docker CLI verwenden, um das Image zuerst lokal zu erstellen und zu testen. Dies ist in den folgenden Befehlen veranschaulicht. Dieser Ansatz kann das Testen und Optimieren des Images vor der ersten Bereitstellung unter ACR vereinfachen. Hierfür ist es aber erforderlich, die Docker CLI zu installieren und sicherzustellen, dass der Docker-Daemon ausgeführt wird.

Erstellen des Images:

```bash
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Lokales Ausführen des Images:

```bash
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Nun können Sie unter `http://localhost:8080` auf Ihre Anwendung zugreifen.

Anmelden bei Ihrer Azure Container Registry-Instanz:

```azurecli
az acr login -n ${MY_ACR}
```

Pushen des Images an Ihre Azure Container Registry-Instanz:

```bash
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Ausführlichere Informationen zum Erstellen und Speichern von Containerimages in Azure finden Sie im Lernmodul [Erstellen und Speichern von Containerimages mit Azure Container Registry](/learn/modules/build-and-store-container-images/).

Wenn Sie unser [GitHub-Schnellstartrepository für Spring Boot-Container](https://github.com/Azure/spring-boot-container-quickstart) verwendet haben, können Sie auch einen benutzerdefinierten Keystore einschließen, der Ihrer JVM-Instanz beim Start hinzugefügt wird. Dieser Hinzufügevorgang findet statt, wenn Sie die Keystore-Datei unter */opt/spring-boot/mycert.crt* platzieren. Hierzu können Sie die Datei direkt dem Dockerfile hinzufügen oder den Azure Key Vault-Anbieter für den Secrets Store CSI-Treiber verwenden, wie weiter oben bereits erwähnt.

Wenn Sie unser [GitHub-Schnellstartrepository für Spring Boot-Container](https://github.com/Azure/spring-boot-container-quickstart) verwendet haben, können Sie auch Application Insights aktivieren, indem Sie in Ihrer Kubernetes-Bereitstellungsdatei die Umgebungsvariable `APPLICATIONINSIGHTS_CONNECTION_STRING` festlegen. (Der Wert der Umgebungsvariablen sollte wie folgt aussehen: `InstrumentationKey=00000000-0000-0000-0000-000000000000`). Weitere Informationen finden Sie unter [Java-Anwendungsüberwachung ohne Code mit Azure Monitor Application Insights – Public Preview](/azure/azure-monitor/app/java-in-process-agent).

Falls Ihr Docker-Image nicht angepasst werden muss, können Sie alternativ auch die Verwendung des [Maven-Jib-Plug-Ins](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) oder eine Bereitstellung in AKS in Erwägung ziehen. Weitere Informationen finden Sie unter [Bereitstellen von Spring Boot-Anwendungen in Azure Kubernetes Service](../spring-framework/deploy-spring-boot-java-app-on-kubernetes.md).

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

### <a name="deploy-to-aks"></a>Bereitstellen für AKS

Es wird beschrieben, wie Sie Ihre Kubernetes-YAML-Datei(en) erstellen und anwenden. Weitere Informationen finden Sie unter [Quickstart: Bereitstellen eines Azure Kubernetes Service-Clusters über die Azure-Befehlszeilenschnittstelle](/azure/aks/kubernetes-walkthrough#run-the-application). Wenn Sie einen externen Lastenausgleich erstellen (ob für Ihre Anwendung oder einen Eingangscontroller), sollten Sie darauf achten, dass Sie die IP-Adresse aus dem vorherigen Abschnitt als `LoadBalancerIP` angeben.

Binden Sie externalisierte Parameter als Umgebungsvariablen ein. Weitere Informationen finden Sie unter [Definieren von Umgebungsvariablen für einen Container](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/).

Beachten Sie beim Erstellen Ihrer YAML-Bereitstellungsdatei die Einstellungen für Arbeitsspeicher und CPU, damit Ihre Container die richtige Größe haben.

### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Sicherstellen der Konsolenprotokollierung und Konfigurieren von Diagnoseeinstellungen

Konfigurieren Sie Ihre Protokollierung so, dass die Protokollierungsinformationen aller Anwendungen nicht in Dateien, sondern in der Konsole ausgegeben werden.

Nach der Bereitstellung einer Anwendung in Azure Kubernetes Service können die Protokolle mithilfe von `kubectl` angezeigt werden.

#### <a name="logstashelk-stack"></a>LogStash/ELK-Stapel

Wenn Sie LogStash bzw. den ELK-Stapel für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an einen [Azure Event Hub](/azure/event-hubs/) gestreamt wird. Verwenden Sie dann das [Event Hub-Plug-In für LogStash](https://github.com/logstash-plugins/logstash-input-azure_event_hubs), um protokollierte Ereignisse in LogStash zu erfassen.

#### <a name="splunk"></a>Splunk

Wenn Sie Splunk für die Protokollaggregation verwenden, konfigurieren Sie die Diagnoseeinstellung so, dass die Konsolenausgabe an [Azure Blob Storage](/azure/storage/blobs/) gestreamt wird. Verwenden Sie dann das [Splunk-Add-On für Microsoft Cloud Services](https://splunkbase.splunk.com/app/3757/), um protokollierte Ereignisse in Splunk zu erfassen.

### <a name="migrate-and-enable-the-identity-provider"></a>Migrieren und Aktivieren des Identitätsanbieters

Sollte für Spring Boot-Anwendungen eine Authentifizierung oder Autorisierung erforderlich sein, stellen Sie sicher, dass sie für den Zugriff auf den Identitätsanbieter konfiguriert sind:

* Wenn es sich bei dem Identitätsanbieter um Azure Active Directory handelt, sollten keine Änderungen erforderlich sein.
* Handelt es sich bei dem Identitätsanbieter um eine lokale Active Directory-Gesamtstruktur, empfiehlt sich ggf. die Implementierung einer Hybrididentitätslösung mit Azure Active Directory. Entsprechende Informationen finden Sie in der [Dokumentation zur Hybrididentität](/azure/active-directory/hybrid/).
* Wenn es sich bei dem Identitätsanbieter um eine andere lokale Lösung (beispielsweise PingFederate) handelt, erfahren Sie im Thema [Benutzerdefinierte Installation von Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), wie Sie einen Verbund mit Azure Active Directory konfigurieren. Alternativ können Sie Spring Security nutzen, um Ihren Identitätsanbieter über [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) oder [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2) zu verwenden.

### <a name="configure-persistent-storage"></a>Konfigurieren von persistentem Speicher

Konfigurieren Sie ein oder mehrere [persistente Volumes](/azure/aks/azure-disks-dynamic-pv), wenn für Ihre Anwendung nicht flüchtiger Speicher benötigt wird.

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie Ihre Anwendung zu AKS migriert haben, sollten Sie sich vergewissern, dass sie wie erwartet funktioniert. Anschließend können Sie sich dann über unsere Empfehlungen informieren, mit denen Sie Ihre Anwendung cloudnativer gestalten können.

### <a name="recommendations"></a>Empfehlungen

* Erwägen Sie, einen DNS-Namen der IP-Adresse hinzuzufügen, die Ihrem Eingangscontroller oder dem Lastenausgleichsmodul der Anwendung zugeordnet ist. Weitere Informationen finden Sie unter [Erstellen eines Eingangscontrollers mit einer statischen öffentlichen IP-Adresse in AKS](/azure/aks/ingress-static-ip).

* Erwägen Sie, [HELM-Diagramme ](https://helm.sh/docs/topics/charts/) für Ihre Anwendung hinzuzufügen. Mit einem Helm-Diagramm können Sie Ihre Anwendungsbereitstellung parametrisieren, damit diese von unterschiedlichen Kunden genutzt werden kann.

* Entwerfen und implementieren Sie eine DevOps-Strategie. Sie können Bereitstellungen automatisieren und mit Azure Pipelines testen, um die Zuverlässigkeit sicherzustellen, während gleichzeitig die Entwicklungsgeschwindigkeit erhöht wird. Weitere Informationen finden Sie unter [Erstellen und Bereitstellen im AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Aktivieren Sie die [Azure-Überwachung für den Cluster](/azure/azure-monitor/insights/container-insights-enable-existing-clusters), um beispielsweise das Sammeln von Containerprotokollen und das Nachverfolgen der Nutzung zu ermöglichen.

* Erwägen Sie, anwendungsspezifische Metriken über Prometheus verfügbar zu machen. Prometheus ist ein Open-Source-Framework für Metriken, das von der Kubernetes-Community viel genutzt wird. Sie können die [Erfassung von Prometheus-Metriken in Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration) konfigurieren, anstatt Ihren eigenen Prometheus-Server zu hosten. So ermöglichen Sie die Aggregation von Metriken aus Ihren Anwendungen und die automatisierte Reaktion auf anomale Bedingungen bzw. deren Eskalation.

* Entwerfen und implementieren Sie eine Strategie für Geschäftskontinuität und Notfallwiederherstellung. Bei unternehmenskritischen Anwendungen sollten Sie erwägen, eine [Bereitstellungsarchitektur mit mehreren Regionen](/azure/aks/operator-best-practices-multi-region) zu verwenden.

* Informieren Sie sich über die [Richtlinie zur Unterstützung der Kubernetes-Version](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). Wir sind dafür verantwortlich, [Ihren AKS-Cluster zu aktualisieren und auf dem neuesten Stand zu halten](/azure/aks/upgrade-cluster), damit sichergestellt ist, dass immer eine unterstützte Version ausgeführt wird.

* Sorgen Sie dafür, dass alle Teammitglieder, die für die Clusterverwaltung und Anwendungsentwicklung zuständig sind, über die entsprechenden [bewährten Methoden für AKS](/azure/aks/best-practices) informiert sind.

* Stellen Sie sicher, dass in Ihrer Bereitstellungsdatei angegeben ist, wie parallele Updates durchgeführt werden. Weitere Informationen finden Sie in der Kubernetes-Dokumentation unter [Bereitstellung von parallelen Updates](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment).

* Richten Sie die automatische Skalierung ein, damit hohe Auslastungen zu Spitzenzeiten bewältigt werden können. Weitere Informationen hierzu finden Sie unter [Automatisches Skalieren eines Clusters zur Erfüllung von Anwendungsanforderungen in AKS](/azure/aks/cluster-autoscaler).

* Sie haben auch die Möglichkeit, [die Größe des Codecaches zu überwachen](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) und der Variablen `JAVA_OPTS` in der Dockerfile die Parameter `-XX:InitialCodeCacheSize` und `-XX:ReservedCodeCacheSize` hinzuzufügen, um die Leistung weiter zu optimieren.