---
title: Migrieren von Spring Boot-Anwendungen zu Azure Spring Cloud
description: In diesem Leitfaden erfahren Sie, worauf Sie achten müssen, wenn Sie eine vorhandene Spring Boot-Anwendung für die Ausführung in Azure Spring Cloud migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4d2f84c6c77294c9a2d25028608e2feb712599f8
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062051"
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

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Nach der Migration

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
