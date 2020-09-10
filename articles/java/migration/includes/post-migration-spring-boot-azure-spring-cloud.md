---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 6b2ad5c8490cd4b1c450426f8e7d728cd39eaea3
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062054"
---
Nachdem Sie die Migration abgeschlossen haben, überprüfen Sie, ob Ihre Anwendung erwartungsgemäß funktioniert. Mithilfe der folgenden Empfehlungen können Sie Ihre Anwendung anschließend cloudnativer gestalten.

* Ziehen Sie in Erwägung, die Anwendung für die Verwendung der Spring Cloud-Registrierung zu aktivieren. Dadurch kann Ihre Anwendung von anderen bereitgestellten Microservices und Clients dynamisch erkannt werden. Weitere Informationen finden Sie im [Tutorial: Vorbereiten einer Java Spring-App für die Bereitstellung](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Ändern Sie dann alle Anwendungsclients so, dass sie den Spring Client Load Balancer verwenden. Dadurch kann der Client Adressen aller ausgeführten Instanzen der Anwendung abrufen und eine Instanz finden, die funktioniert, wenn eine andere Instanz beschädigt wird oder nicht mehr reagiert. Weitere Informationen finden Sie unter [Spring Tips: Spring Cloud Load Balancer](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) im Spring-Blog.

* Anstatt Ihre Anwendung öffentlich zu machen, können Sie wahlweise auch eine [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/current/reference/html/)-Instanz hinzufügen. Spring Cloud Gateway bietet einen einzigen Endpunkt für alle Anwendungen/Microservices, die in Ihrer Azure Spring Cloud-Instanz bereitgestellt werden. Wenn bereits ein Spring Cloud Gateway bereitgestellt wurde, stellen Sie sicher, dass es für die Weiterleitung von Datenverkehr an die neu bereitgestellte Anwendung konfiguriert ist.

* Fügen Sie ggf. einen Spring Cloud-Konfigurationsserver hinzu, um die Konfiguration und Versionskontrolle für all Ihre Spring Cloud-Microservices zentral zu verwalten. Erstellen Sie zunächst ein Git-Repository, um die Konfiguration zu speichern, und konfigurieren Sie die Azure Spring Cloud-Instanz dann für die Verwendung dieser Konfiguration. Weitere Informationen finden Sie im [Tutorial: Einrichten einer Spring Cloud-Konfigurationsserverinstanz für Ihren Dienst](/azure/spring-cloud/spring-cloud-tutorial-config-server). Migrieren Sie dann Ihre Konfiguration mit den folgenden Schritten:

  1. Erstellen Sie im Verzeichnis *src/main/resources* der Anwendung die Datei *bootstrap.yml* mit den folgenden Inhalten:

        ```yml
          spring:
            application:
              name: <your-application-name>
        ```

  1. Erstellen Sie im Git-Konfigurationsrepository, die Datei *<Name-Ihrer-Anwendung>.yml* mit demselben Namen für `your-application-name` wie im vorherigen Schritt. Verschieben Sie die Einstellungen aus der Datei *application.yml* in die *src/main/resources* in die soeben neu erstellte Datei. Wenn die Einstellungen zuvor in einer *PROPERTIES*-Datei gespeichert waren, müssen sie zunächst in YAML konvertiert werden. Für diese Konvertierung finden Sie Onlinetools oder IntelliJ-Plug-Ins.

  1. Erstellen Sie im obigen Verzeichnis die Datei *application.yml*. Sie können diese Datei verwenden, um Einstellungen und Ressourcen zu definieren, die für alle Anwendungen in der Azure Spring Cloud-Instanz freigegeben werden. Diese Einstellungen umfassen in der Regel u. a. Datenquellen, Protokollierungseinstellungen und die Konfiguration des Spring Boot-Aktors.

  1. Commiten und pushen Sie diese Änderungen in das Git-Repository.

  1. Entfernen Sie die Datei *application.properties* oder *application.yml* aus der Anwendung.

* Fügen Sie ggf. eine Bereitstellungspipeline für automatische, konsistente Bereitstellungen hinzu. Eine entsprechende Anleitung steht für [Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), für [GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) und für [Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service) zur Verfügung.

* Verwenden Sie ggf. Stagingbereitstellungen, um Codeänderungen in der Produktionsumgebung zu testen, bevor sie für einige oder alle Endbenutzer verfügbar werden. Weitere Informationen finden Sie unter [Einrichten einer Stagingumgebung in Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Fügen Sie ggf. Dienstbindungen hinzu, um Ihre Anwendung mit unterstützten Azure-Datenbanken zu verbinden. Bei Verwendung dieser Dienstbindungen müssten für Ihre Spring Cloud-Anwendungen keine Verbindungsinformationen mehr angegeben werden (auch keine Anmeldeinformationen).

* Erwägen Sie die Verwendung von verteilter Ablaufverfolgung und Azure Application Insights, um die Leistung und Interaktionen Ihrer Anwendungen zu überwachen. Weitere Informationen finden Sie unter [Verwenden der verteilten Ablaufverfolgung mit Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Fügen Sie zur schnellen Erkennung und Behandlung von Anomalien ggf. Azure Monitor-Warnungsregeln und -Aktionsgruppen hinzu. Weitere Informationen finden Sie im [Tutorial: Überwachen von Spring Cloud-Ressourcen mithilfe von Warnungen und Aktionsgruppen](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Replizieren Sie die Azure Spring Cloud-Bereitstellung ggf. in einer anderen Region, um von einer kürzeren Wartezeit sowie von einer höheren Zuverlässigkeit und Fehlertoleranz zu profitieren. Verwenden Sie [Azure Traffic Manager](/azure/traffic-manager) für den Lastenausgleich zwischen Bereitstellungen oder [Azure Front Door](/azure/frontdoor), um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.

* Sollte keine Georeplikation benötigt werden, können Sie eine [Azure Application Gateway-Instanz](/azure/application-gateway) verwenden, um SSL-Abladung und Web Application Firewall mit DDoS-Schutz hinzuzufügen.
