---
title: Migrieren von Java-Anwendungen zu Azure
description: Dieses Thema enthält eine Übersicht über die empfohlenen Strategien zum Migrieren von Java-Anwendungen zu Azure.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: bbc7dd44e975fd47b52df41baa1ec526ac1908f8
ms.sourcegitcommit: a9b9157bb3a802ecfe3699854788d010a3f08d7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84202829"
---
# <a name="migrate-java-applications-to-azure"></a>Migrieren von Java-Anwendungen zu Azure

Dieses Thema enthält eine Übersicht über die empfohlenen Strategien zum Migrieren von Java-Anwendungen zu Azure.

Dieser Migrationsleitfaden wurde für gängige Szenarien mit Java in Azure konzipiert und enthält allgemeine Planungsvorschläge und -aspekte. Wenn Sie ein bestimmtes Java-App-Migrationsszenario mit dem Microsoft-Team für Java in Azure besprechen möchten, füllen Sie bitte den folgenden Fragebogen aus. Ein Mitarbeiter setzt sich dann mit Ihnen in Verbindung.

> [!div class="nextstepaction"]
> [Fragebogen für die Java-Migration](https://aka.ms/migrate-my-Java-app-requested-thru-docs)

## <a name="identifying-application-type"></a>Identifizieren des Anwendungstyps

Bevor Sie für Ihre Java-Anwendung ein Cloudziel auswählen, müssen Sie den Anwendungstyp ermitteln. Bei den meisten Java-Anwendungen handelt es sich um einen der folgenden Typen:

* [Spring Boot-/JAR-Anwendungen](#spring-boot--jar-applications)
* [Spring Cloud/Microservices](#spring-cloud--microservices)
* [Webanwendungen](#web-applications)
* [Java EE-Anwendungen](#java-ee-applications)
* [Batchaufträge/Geplante Aufträge](#batch--scheduled-jobs)

Diese Anwendungstypen werden in den folgenden Abschnitten beschrieben.

### <a name="spring-boot--jar-applications"></a>Spring Boot-/JAR-Anwendungen

Viele neuere Anwendungen werden direkt über die Befehlszeile aufgerufen. Mit diesen Anwendungen werden auch weiterhin Webanforderungen verarbeitet. Aber anstatt den Anwendungsserver für die Verarbeitung von HTTP-Anforderungen zu nutzen, werden die HTTP-Kommunikation und alle anderen Abhängigkeiten direkt in das Anwendungspaket eingebunden. Anwendungen dieser Art werden häufig mit Frameworks erstellt, z. B. Spring Boot, Dropwizard, Micronaut, MicroProfile, Vert.x und anderen.

Diese Anwendungen werden in Archiven mit der Erweiterung *.jar* (JAR-Dateien) angeordnet.

### <a name="spring-cloud--microservices"></a>Spring Cloud/Microservices

Der Microservice-Architekturstil ist ein Ansatz für die Entwicklung einer einzelnen Anwendung als Suite mit kleineren Diensten, die jeweils als eigener Prozess ausgeführt werden und mit einfachen Mechanismen kommunizieren, z. B. häufig mit einer HTTP-Ressourcen-API. Diese Dienste basieren auf den Geschäftsfunktionen und können über vollständig automatisierte Prozesse unabhängig voneinander bereitgestellt werden. Der Aufwand für die zentralisierte Verwaltung dieser Dienste ist minimal. Sie können in verschiedenen Programmiersprachen erstellt werden, und der Einsatz unterschiedlicher Speichertechnologien ist möglich. Dienste dieser Art werden häufig mit Frameworks, z. B. Spring Cloud, erstellt.

Die Dienste werden in mehreren Anwendungen mit der Erweiterung *.jar* (JAR-Dateien) angeordnet.

### <a name="web-applications"></a>Webanwendungen

Webanwendungen werden in einem [Servlet](https://en.wikipedia.org/wiki/Java_servlet)-Container ausgeführt. In einigen Fällen werden Servlet-APIs direkt genutzt. Häufig kommen aber auch zusätzliche Frameworks zum Einsatz, in die Servlet-APIs eingekapselt sind, z. B. Apache Struts, Spring MVC, JavaServer Faces (JSF) und andere.

Webanwendungen werden in Archiven mit der Erweiterung *.war* (WAR-Dateien) angeordnet.

### <a name="java-ee-applications"></a>Java EE-Anwendungen

Java EE-Anwendungen (auch als J2EE-Anwendungen oder neuerdings als Jakarta EE-Anwendungen bezeichnet) können einige, alle oder keine Elemente von Webanwendungen enthalten. Darüber hinaus können sie auch viele zusätzliche Komponenten enthalten und nutzen, die über die [Java EE-Spezifikation](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition) definiert sind.

Java EE-Anwendungen können in Archiven mit der Erweiterung *.ear* (EAR-Dateien) oder *.war* (WAR-Dateien) angeordnet werden.

Java EE-Anwendungen müssen auf Java EE-konformen Anwendungsservern (z. B. WebLogic, WebSphere, WildFly, GlassFish, Payara und andere) bereitgestellt werden.

Anwendungen, die nur auf den Features der Java EE-Spezifikation basieren (also vom App-Server unabhängige Anwendungen), können von einem konformen Anwendungsserver zu einem anderen migriert werden. Falls Ihre Anwendung von einem bestimmten Anwendungsserver abhängig ist (App-Server-Abhängigkeit), müssen Sie ggf. ein Azure-Dienstziel auswählen, auf dem Sie den Anwendungsserver hosten können.

### <a name="batch--scheduled-jobs"></a>Batchaufträge/Geplante Aufträge

Für einige Anwendungen ist vorgesehen, dass sie nur kurz ausgeführt werden, eine bestimmte Workload ausführen und dann beendet werden, anstatt auf Anforderungen oder Benutzereingaben zu warten. Diese Aufträge müssen unter Umständen nur einmal oder in regelmäßigen geplanten Intervallen ausgeführt werden. In lokalen Umgebungen werden diese Aufträge häufig über das crontab-Element eines Servers aufgerufen.

Diese Anwendungen werden in Archiven mit der Erweiterung *.jar* (JAR-Dateien) angeordnet.

> [!NOTE]
> Wenn für Ihre Anwendung ein Planer (z. B. Spring Batch oder Quartz) zum Ausführen von geplanten Aufgaben genutzt wird, empfehlen wir Ihnen dringend eine Vorgehensweise, bei der diese Aufgaben außerhalb der Anwendung ausgeführt werden. Wenn Ihre Anwendung auf mehrere Instanzen in der Cloud skaliert wird, wird derselbe Auftrag mehr als einmal ausgeführt. Falls für Ihren Planungsmechanismus die lokale Zeitzone des Hosts genutzt wird, kann es außerdem zu unerwünschtem Verhalten kommen, wenn Sie Ihre Anwendung regionsübergreifend skalieren.

## <a name="selecting-the-target-azure-service-destination"></a>Auswählen des Azure-Dienstziels

In den folgenden Abschnitten wird veranschaulicht, welche Dienstziele Ihre Anwendungsanforderungen erfüllen und welche Zuständigkeiten damit verbunden sind.

### <a name="hosting-options-grid"></a>Übersicht über die Hostingoptionen

Anhand der folgenden Übersicht können Sie potenzielle Ziele für Ihren Anwendungstyp ermitteln. Wie Sie sehen, werden von AKS und Virtual Machines alle Anwendungstypen unterstützt, Ihr Team muss jedoch mehr Aufgaben übernehmen, wie im nächsten Abschnitt gezeigt.

|   |App<br>Dienst<br>Java SE|App<br>Dienst<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Virtual Machines|
|---|---|---|---|---|---|---|
| Spring Boot-/JAR-Anwendungen                                    |&#x2714;|        |&#x2714;|&#x2714;|&#x2714;|
| Spring Cloud/Microservices                                      |        |        |&#x2714;|&#x2714;|&#x2714;|
| Webanwendungen                                                  |        |&#x2714;|        |&#x2714;|&#x2714;|
| Java EE-Anwendungen                                              |        |        |        |&#x2714;|&#x2714;|
| Kommerzielle Anwendungsserver<br>(z. B. WebLogic oder WebSphere) |        |        |        |&#x2714;|&#x2714;|
| Langfristige Aufbewahrung im lokalen Dateisystem                         |&#x2714;|&#x2714;|        |&#x2714;|&#x2714;|
| Clustering auf Anwendungsserverebene                               |        |        |        |&#x2714;|&#x2714;|
| Batchaufträge/Geplante Aufträge                                            |        |        |&#x2714;|&#x2714;|&#x2714;|
| VNET-Integration/Hybridkonnektivität                              |Vorschau |Vorschau |        |&#x2714;|&#x2714;|
| Verfügbarkeit von Azure-Regionen                | [Details][10] | [Details][10] | [Details][11] |[Details][12]|[Details][13]|

### <a name="ongoing-responsibility-grid"></a>Übersicht zu laufenden Zuständigkeiten

Ermitteln Sie anhand des folgenden Rasters, welche Zuständigkeiten sich nach der Migration in Bezug auf die einzelnen Ziele für Ihr Team ergeben.

Für Ihr Team gilt eine fortlaufende Zuständigkeit für die Aufgaben, die mit „&#x1F449;“ gekennzeichnet sind. Wir empfehlen Ihnen, zur Erfüllung dieser Zuständigkeiten einen stabilen, stark automatisierten Prozess zu implementieren. 

> [!NOTE]
> Hinweis: Es handelt sich hierbei nicht um eine umfassende Liste mit Zuständigkeiten.

|                                                                       | App Service | Azure Spring Cloud | AKS | Virtual Machines |
|---|---|---|---|---|
| Aktualisieren von Bibliotheken<br>(einschließlich Behebung von Sicherheitsrisiken)                 | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |
| Aktualisieren des Anwendungsservers<br>(einschließlich Behebung von Sicherheitsrisiken)    | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Aktualisieren der Java Runtime<br>(einschließlich Behebung von Sicherheitsrisiken)          | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Auslösen von Kubernetes-Updates<br>(durchgeführt von Azure per manuellem Trigger) | –         | ![Azure][1] | &#x1F449;   | –       |
| Abstimmen von nicht abwärtskompatiblen Änderungen der Kubernetes-API                  | –         | ![Azure][1] | &#x1F449;   | –       |
| Aktualisieren des Container-Basisimages<br>(einschließlich Behebung von Sicherheitsrisiken)      | –         | ![Azure][1] | &#x1F449;   | –       |
| Aktualisieren des Betriebssystems<br>(einschließlich Behebung von Sicherheitsrisiken)      | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Erkennen und Neustarten von Instanzen mit Fehlern                                   | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Implementieren des Ausgleichs und parallelen Neustarts für Updates                       | ![Azure][1] | ![Azure][1] | ![Azure][1] | &#x1F449; |
| Infrastrukturverwaltung                                                   | ![Azure][1] | ![Azure][1] | &#x1F449;   | &#x1F449; |
| Überwachung und Warnungsverwaltung                                             | &#x1F449;   | &#x1F449;   | &#x1F449;   | &#x1F449; |

Wenn Sie den Servlet-Container (z. B. Spring Boot) als Teil Ihrer Anwendung bereitstellen, sollten Sie diesen als Bibliothek ansehen. Er liegt somit in Ihrem Zuständigkeitsbereich.

## <a name="ensuring-on-premises-connectivity"></a>Sicherstellen der lokalen Konnektivität

Wenn Ihre Anwendung auf Ihre lokalen Dienste zugreifen muss, müssen Sie einen der Konnektivitätsdienste von Azure bereitstellen. Weitere Informationen finden Sie unter [Auswählen einer Lösung zum Herstellen einer Verbindung zwischen einem lokalen Netzwerk und Azure](/azure/architecture/reference-architectures/hybrid-networking/). Alternativ müssen Sie Ihre Anwendung so umgestalten, dass öffentlich zugängliche APIs genutzt werden, die von Ihren lokalen Ressourcen verfügbar gemacht werden.

Sie sollten diesen Vorgang durchführen, bevor Sie mit der Migration beginnen.

## <a name="inventory-current-capacity-and-resource-usage"></a>Bestand: Aktuelle Kapazität und Ressourcennutzung

Dokumentieren Sie die Hardware der aktuellen Produktionsserver sowie die durchschnittliche und maximale Anzahl von Anforderungen und die Ressourcennutzung. Sie benötigen diese Informationen, um Ressourcen auf dem Dienstziel bereitzustellen.

## <a name="migration-guidance"></a>Hinweise zur Migration

Verwenden Sie die folgenden Raster, um Informationen zur Migration nach Anwendungstyp und gewünschtem Azure-Dienstziel zu erhalten.

**Java-Anwendungen**

Ermitteln Sie in den folgenden Zeilen Ihren Java-Anwendungstyp und die Spalten, um das Azure-Dienstziel zu finden, auf dem Ihre Anwendung gehostet wird.

Wenn Sie eine JBoss EAP-App zu Tomcat unter App Service migrieren möchten, sollten Sie zuerst die Java EE-App in Java-Web-Apps (Servlets) konvertieren, die unter Tomcat ausgeführt werden, und dann den unten angegebenen Leitfaden befolgen.

Falls Sie eine Web-App unter Tomcat zu Azure Spring Cloud migrieren möchten, sollten Sie die App zuerst in Spring Cloud-Microservices konvertieren und dann den unten angegebenen Leitfaden befolgen.

|Ziel&nbsp;→<br><br>Anwendungstyp&nbsp;&nbsp;↓|App<br>Dienst<br>Java SE|App<br>Dienst<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Virtual Machines|
|---|---|---|---|---|---|---|
| Spring Boot-/<br>JAR-Anwendungen | [Leitfaden][5] | Leitfaden<br>Geplant | Leitfaden<br>Geplant | [Leitfaden][14]      | Leitfaden<br>Geplant |
| Spring Cloud/<br>Microservices   | –           | –                 | [Leitfaden][15]      | Leitfaden<br>Geplant | Leitfaden<br>Geplant |
| Webanwendungen<br>unter Tomcat     | –           | [Leitfaden][2]       | –                 | [Leitfaden][3]       | Leitfaden<br>Geplant |

**Java EE-Anwendungen**

Ermitteln Sie anhand der unten angegebenen Zeilen den Typ Ihrer Java EE-Anwendung, die auf einem bestimmten App-Server ausgeführt wird. In den Spalten finden Sie das Azure-Dienstziel, auf dem Ihre Anwendung gehostet wird.

|Ziel&nbsp;→<br><br>App-Server&nbsp;↓|App<br>Dienst<br>Java SE|App<br>Dienst<br>Tomcat|Azure<br>Spring<br>Cloud|AKS|Virtual Machines|
|---|---|---|---|---|---|---|
| WildFly/<br>JBoss AS | – | – | – | [Leitfaden][9] | Leitfaden<br>Geplant |
| WebLogic              | – | – | – | [Leitfaden][6] | [Leitfaden][4]       |
| WebSphere             | – | – | – | [Leitfaden][7] | Leitfaden<br>Geplant |
| JBoss EAP             | – | – | – | [Leitfaden][8] | Leitfaden<br>Geplant |

<!-- reference links, for use with tables -->
[1]: media/migration-overview/logo_azure.svg
[2]: migrate-tomcat-to-tomcat-app-service.md
[3]: migrate-tomcat-to-containers-on-azure-kubernetes-service.md
[4]: migrate-weblogic-to-virtual-machines.md
[5]: migrate-spring-boot-to-app-service.md
[6]: migrate-weblogic-to-wildfly-on-azure-kubernetes-service.md
[7]: migrate-websphere-to-wildfly-on-azure-kubernetes-service.md
[8]: migrate-jboss-eap-to-wildfly-on-azure-kubernetes-service.md
[9]: migrate-wildfly-to-wildfly-on-azure-kubernetes-service.md
[10]: https://azure.microsoft.com/global-infrastructure/services/?products=app-service-linux
[11]: https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud
[12]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[13]: https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines
[14]: migrate-spring-boot-to-azure-kubernetes-service.md
[15]: migrate-spring-cloud-to-azure-spring-cloud.md