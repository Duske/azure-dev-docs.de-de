---
title: Migrieren von Spring Boot-Anwendungen zu Azure Spring Cloud
description: In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Boot-Anwendung für die Ausführung in Azure Spring Cloud migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 7bc4a5188181f3b4b6d98b5308a5027a42bbac5e
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810583"
---
# <a name="migrate-spring-boot-applications-to-azure-spring-cloud"></a>Migrieren von Spring Boot-Anwendungen zu Azure Spring Cloud

In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Boot-Anwendung für die Ausführung in Azure Spring Cloud migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

Führen Sie vor Beginn einer Migration die in den folgenden Abschnitten beschriebenen Schritte zur Bewertung und Bestandsermittlung aus, um eine erfolgreiche Migration zu gewährleisten.

Falls Sie keine dieser Voraussetzungen für die Migration erfüllen können, sehen Sie sich die folgenden Begleithandbücher an:

* Migrieren ausführbarer JAR-Anwendungen zu Containern in Azure Kubernetes Service (Leitfaden geplant)
* Migrieren ausführbarer JAR-Anwendungen zu Azure Virtual Machines (Leitfaden geplant)

### <a name="inspect-application-components"></a>Untersuchen der Anwendungskomponenten

[!INCLUDE [identify-local-state](includes/identify-local-state-azure-spring-cloud.md)]

[!INCLUDE [static-content-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Ermitteln, ob Dienste betriebssystemspezifischen Code enthalten

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Gehen Sie bei Anwendungen, die Spring Boot 1.x verwenden, wie im [Migrationshandbuch für Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) beschrieben vor, um sie auf eine unterstützte Spring Boot-Version zu aktualisieren. Informationen zu unterstützten Versionen finden Sie unter [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

### <a name="inventory-external-resources"></a>Bestand: Externe Ressourcen

Identifizieren Sie externe Ressourcen, z. B. Datenquellen, JMS-Nachrichtenbroker und URLs anderer Dienste. Bei Spring Boot-Anwendungen befindet sich die Konfiguration solcher Ressourcen üblicherweise im Ordner *src/main/directory* in einer Datei namens *application.properties* oder *application.yml*.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Nachdem Sie den oder die verwendeten Broker ermittelt haben, können Sie nach den entsprechenden Einstellungen suchen. Bei Spring Boot-Anwendungen befinden sich diese in der Regel im Anwendungsverzeichnis in den Dateien *application.properties* und *application.yml*.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot.md](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="identify-any-clients-relying-on-a-non-standard-port"></a>Identifizieren von Clients, die einen nicht standardmäßigen Port verwenden

Azure Spring Cloud überschreibt die `server.port`-Einstellung in der bereitgestellten Anwendung. Wenn Clients davon abhängig sind, dass die Anwendung an einem anderen Port als 443 verfügbar ist, müssen Sie die betreffende Einstellung ändern.

#### <a name="all-other-external-resources"></a>Alle anderen externen Ressourcen

Es würde den Rahmen dieses Leitfadens sprengen, jede mögliche externe Abhängigkeit zu dokumentieren. Vergewissern Sie sich daher nach der Migration, dass alle externen Abhängigkeiten Ihrer Anwendung abgedeckt wurden.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

## <a name="migration"></a>Migration

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Erstellen von Azure Spring Cloud-Instanz und Apps

Stellen Sie eine Azure Spring Cloud-Instanz in Ihrem Azure-Abonnement bereit, wenn noch keine solche vorhanden ist. Erstellen Sie dort anschließend eine Anwendung. Weitere Informationen finden Sie unter [Quickstart: Starten einer vorhandenen Azure Spring Cloud-Anwendung über das Azure-Portal](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal) vor.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-all-certificates-to-keyvault"></a>Migrieren aller Zertifikate zu Key Vault

Azure Spring Cloud bietet keinen Zugriff auf den JRE-Keystore. Daher müssen Sie Zertifikate zu Azure Key Vault migrieren und den Anwendungscode ändern, um auf Zertifikate in Key Vault zuzugreifen. Weitere Informationen finden Sie unter [Erste Schritte mit Key Vault-Zertifikaten](/azure/key-vault/certificates/certificate-scenarios) sowie unter [Azure Key Vault-Zertifikat: Clientbibliothek für Java (Version 4.0.3)](/java/api/overview/azure/security-keyvault-certificates-readme).

### <a name="remove-application-performance-management-apm-integrations"></a>Entfernen von APM-Integrationen (Application Performance Management, Anwendungsleistungsverwaltung)

Entfernen Sie sämtliche Integrationen für APM-Tools/-Agents. Informationen zum Konfigurieren der Leistungsverwaltung mit Azure Monitor finden Sie im Abschnitt [Nach der Migration](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Deaktivieren von Metrikclients und -endpunkten in Ihren Anwendungen

Entfernen Sie alle in Ihren Anwendungen verwendeten Metrikclients oder verfügbar gemachten Metrikendpunkte.

### <a name="deploy-the-application"></a>Bereitstellen der Anwendung

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
* Handelt es sich bei dem Identitätsanbieter um eine lokale Active Directory-Gesamtstruktur, empfiehlt sich ggf. die Implementierung einer Hybrididentitätslösung mit Azure Active Directory. Weitere Informationen finden Sie in der [Dokumentation zur Hybrid-Identität](/azure/active-directory/hybrid/).
* Wenn es sich bei dem Identitätsanbieter um eine andere lokale Lösung (beispielsweise PingFederate) handelt, erfahren Sie im Thema [Benutzerdefinierte Installation von Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), wie Sie einen Verbund mit Azure Active Directory konfigurieren. Alternativ können Sie Spring Security nutzen, um Ihren Identitätsanbieter über [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) oder [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2) zu verwenden.

### <a name="expose-the-application"></a>Verfügbarmachen der Anwendung

Anwendungen, die in der Azure Spring Cloud bereitgestellt werden, sind standardmäßig nicht extern sichtbar. Sie können Ihre Anwendung verfügbar machen, indem Sie sie mit dem folgenden Befehl für die Öffentlichkeit freigeben:

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Überspringen Sie diesen Schritt, wenn Sie ein Spring Cloud-Gateway verwenden oder dies beabsichtigen (weitere Informationen hierzu finden Sie im folgenden Abschnitt).

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie die Migration abgeschlossen haben, überprüfen Sie, ob Ihre Anwendung erwartungsgemäß funktioniert. Mithilfe der folgenden Empfehlungen können Sie Ihre Anwendung anschließend cloudnativer gestalten.

* Ziehen Sie in Erwägung, die Anwendung für die Verwendung der Spring Cloud-Registrierung zu aktivieren. Dadurch kann Ihre Anwendung von anderen bereitgestellten Microservices und Clients dynamisch erkannt werden. Weitere Informationen finden Sie im [Tutorial: Vorbereiten einer Java Spring-App für die Bereitstellung](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Ändern Sie dann alle Anwendungsclients so, dass sie den Spring Client Load Balancer verwenden. Dadurch kann der Client Adressen aller ausgeführten Instanzen der Anwendung abrufen und eine Instanz finden, die funktioniert, wenn eine andere Instanz beschädigt wird oder nicht mehr reagiert. Weitere Informationen finden Sie unter [Spring Tips: Spring Cloud LoadBalancer](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) im Spring-Blog.

* Anstatt Ihre Anwendung öffentlich zu machen, können Sie wahlweise auch eine [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-gateway/reference/html/)-Instanz hinzufügen. Spring Cloud Gateway bietet einen einzigen Endpunkt für alle Anwendungen/Microservices, die in Ihrer Azure Spring Cloud-Instanz bereitgestellt werden. Wenn bereits ein Spring Cloud Gateway bereitgestellt wurde, stellen Sie sicher, dass es für die Weiterleitung von Datenverkehr an die neu bereitgestellte Anwendung konfiguriert ist.

* Fügen Sie ggf. einen Spring Cloud-Konfigurationsserver hinzu, um die Konfiguration und Versionskontrolle für all Ihre Spring Cloud-Microservices zentral zu verwalten. Erstellen Sie zunächst ein Git-Repository, um die Konfiguration zu speichern, und konfigurieren Sie die Azure Spring Cloud-Instanz dann für die Verwendung dieser Konfiguration. Weitere Informationen finden Sie im [Tutorial: Einrichten einer Spring Cloud-Konfigurationsserverinstanz für Ihren Dienst](/azure/spring-cloud/spring-cloud-tutorial-config-server). Migrieren Sie dann Ihre Konfiguration mit den folgenden Schritten:

  1. Erstellen Sie ein Verzeichnis im Git-Konfigurationsrepository mit dem gleichen Namen wie die Anwendung, die Sie in der Azure Spring Cloud-Instanz definiert haben.

  1. Erstellen Sie in diesem Verzeichnis eine Datei *bootstrap.yml* mit folgendem Inhalt:

     ```yml
     spring:
       application:
         name: <Your the application name used in the previous step>
     ```

  1. Erstellen Sie eine Datei *application.yml* im oben genannten Verzeichnis, und verschieben Sie die Anwendungseinstellungen dann dorthin. Wenn die Einstellungen zuvor in einer *PROPERTIES*-Datei gespeichert wurden, müssen sie in YAML konvertiert werden.

  1. Commiten und pushen Sie diese Änderungen in das Git-Repository.

* Fügen Sie ggf. eine Bereitstellungspipeline für automatische, konsistente Bereitstellungen hinzu. Eine entsprechende Anleitung steht für [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), für [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) und für [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service) zur Verfügung.

* Verwenden Sie ggf. Stagingbereitstellungen, um Codeänderungen in der Produktionsumgebung zu testen, bevor sie für einige oder alle Endbenutzer verfügbar werden. Weitere Informationen finden Sie unter [Einrichten einer Stagingumgebung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Fügen Sie ggf. Dienstbindungen hinzu, um Ihre Anwendung mit unterstützten Azure-Datenbanken zu verbinden. Bei Verwendung dieser Dienstbindungen müssten für Ihre Spring Cloud-Anwendungen keine Verbindungsinformationen mehr angegeben werden (auch keine Anmeldeinformationen).

* Erwägen Sie die Verwendung von verteilter Ablaufverfolgung und Azure Application Insights, um die Leistung und Interaktionen Ihrer Anwendungen zu überwachen. Weitere Informationen finden Sie unter [Verwenden der verteilten Ablaufverfolgung mit Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Fügen Sie zur schnellen Erkennung und Behandlung von Anomalien ggf. Azure Monitor-Warnungsregeln und -Aktionsgruppen hinzu. Weitere Informationen finden Sie im [Tutorial: Überwachen von Spring Cloud-Ressourcen mithilfe von Warnungen und Aktionsgruppen](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Replizieren Sie die Azure Spring Cloud-Bereitstellung ggf. in einer anderen Region, um von einer kürzeren Wartezeit sowie von einer höheren Zuverlässigkeit und Fehlertoleranz zu profitieren. Verwenden Sie [Azure Traffic Manager](/azure/traffic-manager) für den Lastenausgleich zwischen Bereitstellungen oder [Azure Front Door](/azure/frontdoor), um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.

* Sollte keine Georeplikation benötigt werden, können Sie eine [Azure Application Gateway-Instanz](/azure/application-gateway) verwenden, um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.
