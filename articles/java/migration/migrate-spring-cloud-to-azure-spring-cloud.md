---
title: Migrieren von Spring Cloud-Anwendungen zu Azure Spring Cloud
description: In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Cloud-Anwendung für die Ausführung in Azure Spring Cloud migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 2/12/2020
ms.custom: devx-track-java
ms.openlocfilehash: e4be32594940d2c207610e7d709ccb328c38ecf6
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831696"
---
# <a name="migrate-spring-cloud-applications-to-azure-spring-cloud"></a>Migrieren von Spring Cloud-Anwendungen zu Azure Spring Cloud

In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Cloud-Anwendung für die Ausführung in Azure Spring Cloud migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

Falls Sie keine dieser Voraussetzungen für die Migration erfüllen können, sehen Sie sich die folgenden Begleithandbücher an:

* Migrieren ausführbarer JAR-Anwendungen zu Containern in Azure Kubernetes Service (Leitfaden geplant)
* Migrieren ausführbarer JAR-Anwendungen zu Azure Virtual Machines (Leitfaden geplant)

### <a name="inspect-application-components"></a>Untersuchen der Anwendungskomponenten

[!INCLUDE [determine-whether-and-how-the-file-system-is-used-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Ermitteln, ob Dienste betriebssystemspezifischen Code enthalten

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Gehen Sie bei Anwendungen, die Spring Boot 1.x verwenden, wie im [Migrationshandbuch für Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) beschrieben vor, um sie auf eine unterstützte Spring Boot-Version zu aktualisieren. Informationen zu unterstützten Versionen finden Sie unter [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

#### <a name="identify-spring-cloud-versions"></a>Ermitteln von Spring Cloud-Versionen

Überprüfen Sie die Abhängigkeiten der einzelnen Anwendungen, die Sie migrieren möchten, um jeweils die Version der verwendeten Spring Cloud-Komponenten zu ermitteln.

##### <a name="maven"></a>Maven

In Maven-Projekten wird die Spring Cloud-Version üblicherweise in der Eigenschaft `spring-cloud.version` festgelegt:

```xml
  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
  </properties>
```

##### <a name="gradle"></a>Gradle

In Gradle-Projekten wird die Spring Cloud-Version üblicherweise im Block mit den zusätzlichen Eigenschaften festgelegt:

```gradle
ext {
  set('springCloudVersion', "Hoxton.SR3")
}
```

Alle Anwendungen müssen so aktualisiert werden, dass sie unterstützte Versionen von Spring Cloud verwenden. Eine Liste mit unterstützten Versionen finden Sie unter [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

#### <a name="identify-zipkin-dependencies"></a>Ermitteln von Zipkin-Abhängigkeiten

Ermitteln Sie, ob Ihre Anwendung explizite Zipkin-Abhängigkeiten aufweist. Suchen Sie in Ihren Maven- oder Gradle-Abhängigkeiten nach Abhängigkeiten von der Gruppe `io.zipkin.java`.

### <a name="inventory-external-resources"></a>Bestand: Externe Ressourcen

Identifizieren Sie externe Ressourcen, z. B. Datenquellen, JMS-Nachrichtenbroker und URLs anderer Dienste. Bei Spring Cloud-Anwendungen befindet sich die Konfiguration für solche Ressourcen in der Regel an einem der folgenden Orte:

* Im Ordner *src/main/directory* (üblicherweise in einer Datei namens *application.properties* oder *application.yml*)
* In dem Spring Cloud Config-Repository, das im vorherigen Schritt ermittelt wurde

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Nachdem Sie den oder die verwendeten Broker ermittelt haben, können Sie nach den entsprechenden Einstellungen suchen. Bei Spring Cloud-Anwendungen befinden sich diese in der Regel im Anwendungsverzeichnis in den Dateien *application.properties* und *application.yml* oder im Spring Cloud Config-Serverrepository.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

#### <a name="identity-providers"></a>Identitätsanbieter

Ermitteln Sie alle Identitätsanbieter und alle Spring Cloud-Anwendungen, für die eine Authentifizierung und/oder Autorisierung erforderlich ist. Informationen zum Konfigurieren von Identitätsanbietern finden Sie in den folgenden Artikeln:

* Informationen zur OAuth2-Konfiguration finden Sie in der [Schnellstartanleitung für Spring Cloud Security](https://cloud.spring.io/spring-cloud-security/2.1.x/multi/multi__quickstart.html#_quickstart).
* Informationen zur Auth0-Spring Security-Konfiguration finden Sie in der [Auth0-Spring Security-Dokumentation](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Informationen zur PingFederate-Spring Security-Konfiguration finden Sie in der [Auth0-PingFederate-Anleitung](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-vmware-tanzu-application-service-tas-formerly-pivotal-cloud-foundry"></a>Über VMware Tanzu Application Service (TAS) (vormals Pivotal Cloud Foundry) konfigurierte Ressourcen

Bei Anwendungen, die mit TAS verwaltet werden, werden externe Ressourcen (einschließlich der weiter oben beschriebenen) häufig über TAS-Dienstbindungen konfiguriert. Verwenden Sie zum Untersuchen der Konfiguration solcher Ressourcen die [TAS-Befehlszeilenschnittstelle (Cloud Foundry)](https://docs.cloudfoundry.org/cf-cli/), um die Variable `VCAP_SERVICES` für die Anwendung anzuzeigen.

```bash
# Log into TAS, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <organization name>
cf target space <space name>

# Display variables for the application
cf env <Application Name>
```

Überprüfen Sie die Variable `VCAP_SERVICES` auf Konfigurationseinstellungen von externen Diensten, die an die Anwendung gebunden sind. Weitere Informationen finden Sie in der [Dokumentation zu TAS (Cloud Foundry)](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

#### <a name="all-other-external-resources"></a>Alle anderen externen Ressourcen

Es würde den Rahmen dieses Leitfadens sprengen, jede mögliche externe Abhängigkeit zu dokumentieren. Vergewissern Sie sich daher nach der Migration, dass alle externen Abhängigkeiten Ihrer Anwendung abgedeckt wurden.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-cloud](includes/inventory-configuration-sources-and-secrets-spring-cloud.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-cloud](includes/inspect-the-deployment-architecture-spring-cloud.md)]

## <a name="migration"></a>Migration

### <a name="remove-explicit-configuration-server-settings"></a>Entfernen expliziter Konfigurationsservereinstellungen

Suchen Sie in den zu migrierenden Diensten nach expliziten Zuweisungen von Eureka-Einstellungen, und entfernen Sie sie. Solche Einstellungen befinden sich in der Regel in der Datei *application.properties* oder *application.yml*:

**application.yml**

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://myusername:mysecretpassword@localhost:8761/eureka/
```

Falls Ihre Anwendungskonfiguration eine solche Einstellung enthält, entfernen Sie sie. Die Verbindungsinformationen des Azure Spring Cloud-Konfigurationsservers werden automatisch eingefügt.

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Erstellen von Azure Spring Cloud-Instanz und Apps

Stellen Sie eine Azure Spring Cloud-Instanz in Ihrem Azure-Abonnement bereit. Stellen Sie dann für jeden zu migrierenden Dienst eine App bereit. Schließen Sie den Registrierungs- und den Konfigurationsserver von Spring Cloud nicht mit ein. Schließen Sie den Spring Cloud-Gatewaydienst ein. Anweisungen dazu finden Sie unter [Schnellstart: Starten einer vorhandenen Azure Spring Cloud-Anwendung über das Azure-Portal](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal) vor.

### <a name="prepare-the-spring-cloud-config-server"></a>Vorbereiten des Spring Cloud Config-Servers

Konfigurieren Sie den Konfigurationsserver in Ihrer Azure Spring Cloud-Instanz. Weitere Informationen finden Sie im [Tutorial: Einrichten einer Spring Cloud-Konfigurationsserverinstanz für Ihren Dienst](/azure/spring-cloud/spring-cloud-tutorial-config-server).

> [!NOTE]
> Sollte sich Ihr derzeitiges Spring Cloud Config-Repository im lokalen Dateisystem oder in der lokalen Umgebung befinden, müssen Sie Ihre Konfigurationsdateien zunächst zu einem Repository in einer privaten Cloud (beispielsweise GitHub, Azure Repos oder BitBucket) migrieren bzw. dort replizieren.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-spring-cloud-vault-secrets-to-azure-keyvault"></a>Migrieren von Spring Cloud Vault-Geheimnissen zu Azure Key Vault

Mithilfe von Spring Boot Starter für Azure Key Vault können Geheimnisse über Spring direkt in Anwendungen eingefügt werden. Weitere Informationen finden Sie unter [Verwenden von Spring Boot Starter für Azure Key Vault](../spring-framework/configure-spring-boot-starter-java-app-with-azure-key-vault.md).

> [!NOTE]
> Für die Migration müssen ggf. einige Geheimnisse umbenannt werden. Aktualisieren Sie Ihren Anwendungscode entsprechend.

### <a name="migrate-all-certificates-to-keyvault"></a>Migrieren aller Zertifikate zu Key Vault

Azure Spring Cloud bietet keinen Zugriff auf den JRE-Keystore. Daher müssen Sie Zertifikate zu Azure Key Vault migrieren und den Anwendungscode ändern, um auf Zertifikate in Key Vault zuzugreifen. Weitere Informationen finden Sie unter [Erste Schritte mit Key Vault-Zertifikaten](/azure/key-vault/certificates/certificate-scenarios) sowie unter [Azure Key Vault-Zertifikat: Clientbibliothek für Java (Version 4.0.3)](/java/api/overview/azure/security-keyvault-certificates-readme).

### <a name="remove-application-performance-management-apm-integrations"></a>Entfernen von APM-Integrationen (Application Performance Management, Anwendungsleistungsverwaltung)

Entfernen Sie sämtliche Integrationen für APM-Tools/-Agents. Informationen zum Konfigurieren der Leistungsverwaltung mit Azure Monitor finden Sie im Abschnitt [Nach der Migration](#post-migration).

### <a name="replace-explicit-zipkin-dependencies-with-spring-cloud-starters"></a>Ersetzen expliziter Zipkin-Abhängigkeiten durch Spring Cloud Starter

Falls migrierte Anwendungen explizite Zipkin-Abhängigkeiten aufweisen, entfernen Sie sie, und ersetzen Sie sie durch Spring Cloud Starter, wie unter [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) im Abschnitt [Abhängigkeit für die verteilte Ablaufverfolgung](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#distributed-tracing-dependency) beschrieben. Informationen zur verteilten Ablaufverfolgung mit Azure Application Insights finden Sie im Abschnitt [Nach der Migration](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Deaktivieren von Metrikclients und -endpunkten in Ihren Anwendungen

Entfernen Sie alle in Ihren Anwendungen verwendeten Metrikclients oder verfügbar gemachten Metrikendpunkte.

### <a name="deploy-the-services"></a>Bereitstellen der Dienste

Stellen Sie die einzelnen migrierten Microservices (ohne Konfigurations- und Registrierungsserver von Spring Cloud) bereit, wie unter [Schnellstart: Starten einer vorhandenen Azure Spring Cloud-Anwendung über das Azure-Portal](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal) beschrieben.

### <a name="configure-per-service-secrets-and-externalized-settings"></a>Konfigurieren dienstspezifischer Geheimnisse und externalisierter Einstellungen

Dienstspezifische Konfigurationseinstellungen können den einzelnen Diensten als Umgebungsvariablen hinzugefügt werden. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zur Azure Spring Cloud-Instanz, und wählen Sie **Apps** aus.
1. Wählen Sie den zu konfigurierenden Dienst aus.
1. Wählen Sie **Konfiguration** aus.
1. Geben Sie die zu konfigurierenden Variablen ein.
1. Wählen Sie **Speichern** aus.

![Spring Cloud: App-Konfigurationseinstellungen](media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>Migrieren und Aktivieren des Identitätsanbieters

Sollte für Spring Cloud-Anwendungen eine Authentifizierung oder Autorisierung erforderlich sein, stellen Sie sicher, dass sie für den Zugriff auf den Identitätsanbieter konfiguriert sind:

* Wenn es sich bei dem Identitätsanbieter um Azure Active Directory handelt, sollten keine Änderungen erforderlich sein.
* Handelt es sich bei dem Identitätsanbieter um eine lokale Active Directory-Gesamtstruktur, empfiehlt sich ggf. die Implementierung einer Hybrididentitätslösung mit Azure Active Directory. Entsprechende Informationen finden Sie in der [Dokumentation zur Hybrididentität](/azure/active-directory/hybrid/).
* Wenn es sich bei dem Identitätsanbieter um eine andere lokale Lösung (beispielsweise PingFederate) handelt, erfahren Sie im Thema [Benutzerdefinierte Installation von Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), wie Sie einen Verbund mit Azure Active Directory konfigurieren. Alternativ können Sie Spring Security nutzen, um Ihren Identitätsanbieter über [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) oder [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2) zu verwenden.

### <a name="update-client-applications"></a>Aktualisieren von Clientanwendungen

Aktualisieren Sie die Konfiguration aller Clientanwendungen, um die veröffentlichten Azure Spring Cloud-Endpunkte für migrierte Anwendungen zu verwenden.

## <a name="post-migration"></a>Nach der Migration

* Fügen Sie ggf. eine Bereitstellungspipeline für automatische, konsistente Bereitstellungen hinzu. Eine entsprechende Anleitung steht für [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), für [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) und für [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service) zur Verfügung.

* Verwenden Sie ggf. Stagingbereitstellungen, um Codeänderungen in der Produktionsumgebung zu testen, bevor sie für einige oder alle Endbenutzer verfügbar werden. Weitere Informationen finden Sie unter [Einrichten einer Stagingumgebung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Fügen Sie ggf. Dienstbindungen hinzu, um Ihre Anwendung mit unterstützten Azure-Datenbanken zu verbinden. Bei Verwendung dieser Dienstbindungen müssten für Ihre Spring Cloud-Anwendungen keine Verbindungsinformationen mehr angegeben werden (auch keine Anmeldeinformationen).

* Erwägen Sie die [Verwendung von verteilter Ablaufverfolgung und Azure Application Insights](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing), um die Leistung und Interaktionen Ihrer Anwendungen zu überwachen.

* Fügen Sie zur schnellen Erkennung und Behandlung von Anomalien ggf. Azure Monitor-Warnungsregeln und -Aktionsgruppen hinzu. Weitere Informationen finden Sie im [Tutorial: Überwachen von Spring Cloud-Ressourcen mithilfe von Warnungen und Aktionsgruppen](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Replizieren Sie die Azure Spring Cloud-Bereitstellung ggf. in einer anderen Region, um von einer kürzeren Wartezeit sowie von einer höheren Zuverlässigkeit und Fehlertoleranz zu profitieren. Verwenden Sie [Azure Traffic Manager](/azure/traffic-manager) für den Lastenausgleich zwischen Bereitstellungen oder [Azure Front Door](/azure/frontdoor), um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.

* Sollte keine Georeplikation benötigt werden, können Sie eine [Azure Application Gateway-Instanz](/azure/application-gateway) verwenden, um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.

* Falls von Ihren Anwendungen alte Spring Cloud-Netflix-Komponenten verwendet werden, empfiehlt es sich gegebenenfalls, diese durch aktuelle Alternativen zu ersetzen:

   | Alt                        | Aktuell                                                |
   |-------------------------------|--------------------------------------------------------|
   | Spring Cloud Eureka           | Spring Cloud Service Registry                          |
   | Spring Cloud Netflix Zuul     | Spring Cloud Gateway                                   |
   | Spring Cloud Netflix Archaius | Spring Cloud-Konfigurationsserver                             |
   | Spring Cloud Netflix Ribbon   | Spring Cloud Load Balancer (clientseitiger Lastenausgleich) |
   | Spring Cloud Hystrix          | Spring Cloud Circuit Breaker + Resilience4J            |
   | Spring Cloud Netflix Turbine  | Micrometer + Prometheus                                |