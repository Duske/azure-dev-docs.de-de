---
title: Migrieren von WebLogic-Anwendungen zu virtuellen Azure-Computern
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebLogic-Anwendung für die Ausführung auf Azure Virtual Machines migrieren möchten.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 86d1afd365c0c1f9126792d57fbeda3817500735
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894172"
---
# <a name="migrate-weblogic-applications-to-azure-virtual-machines"></a>Migrieren von WebLogic-Anwendungen zu virtuellen Azure-Computern

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene WebLogic-Anwendung für die Ausführung auf Azure Virtual Machines migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

### <a name="define-what-you-mean-by-migration-complete"></a>Definieren der Bedeutung von „Migration abgeschlossen“

Dieser Leitfaden und die entsprechenden Azure Marketplace-Angebote sind ein guter Ausgangspunkt für die Beschleunigung der Migration Ihrer WebLogic Server-Workloads für Azure. Es ist wichtig, den Umfang Ihres Migrationsablaufs zu definieren. Führen Sie beispielsweise einen strikten „Lift & Shift“-Vorgang aus Ihrer vorhandenen Infrastruktur auf Azure Virtual Machines durch? Wenn ja, kann es verlockend sein, während der Migration auch nach dem Motto „Lift & Improve“ vorzugehen.

Es ist aber besser, möglichst nah am „Lift & Shift“-Ansatz zu bleiben und die erforderlichen Änderungen vorzunehmen, die in diesem Leitfaden beschrieben werden. Definieren Sie, was genau mit „Migration abgeschlossen“ gemeint ist, damit Sie wissen, wann dieser Meilenstein erreicht ist. Nach dem Erreichen des Zustands „Migration abgeschlossen“ können Sie eine Momentaufnahme Ihrer virtuellen Computer erstellen. Dies ist unter [Erstellen einer Momentaufnahme](/azure/virtual-machines/windows/snapshot-copy-managed-disk) beschrieben. Nachdem Sie sich vergewissert haben, dass eine erfolgreiche Wiederherstellung über die Momentaufnahme möglich ist, fühlen Sie sich beim Ausführen der Verbesserungsschritte („Improve“) sicherer. Sie müssen dann keine Bedenken haben, dass die bisher durchgeführte Migrationsarbeit verloren gehen kann.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>Ermitteln, ob die vordefinierten Marketplace-Angebote ein guter Ausgangspunkt sind

Oracle und Microsoft haben als Partner zusammengearbeitet, um auf dem Azure Marketplace einige Azure-Lösungsvorlagen bereitzustellen und so einen guten Ausgangspunkt für die Migration zu Azure zu schaffen. In der Dokumentation zur [Oracle Fusion Middleware](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/) finden Sie eine Liste mit Angeboten. Wählen Sie ein Angebot aus, das für Ihre vorhandene Bereitstellung am besten geeignet ist. Die Liste mit den Angeboten ist in [dieser Oracle-Dokumentation](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/select-required-oracle-weblogic-server-offer-azure-marketplace.html#GUID-187739C5-EE7A-47C6-B3BA-C0A0333DC398) enthalten.

Falls keines der vorhandenen Angebote ein guter Ausgangspunkt ist, müssen Sie die Bereitstellung mit Azure-VM-Ressourcen manuell reproduzieren. Weitere Informationen finden Sie unter [Was ist IaaS?](https://azure.microsoft.com/overview/what-is-iaas/).

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>Ermitteln, ob die WebLogic-Version kompatibel ist

Ihre vorhandene WebLogic-Version muss mit der Version in den IaaS-Angeboten kompatibel sein. Mit dieser Abfrage werden die Angebote für [WebLogic-Version 12.2.1.3](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1) angezeigt. Wenn Ihre vorhandene WebLogic-Version mit dieser Version nicht kompatibel ist, müssen Sie die Bereitstellung mit Azure-IaaS-Ressourcen manuell reproduzieren. Weitere Informationen finden Sie in der [Azure-Dokumentation](https://azure.microsoft.com/overview/what-is-iaas/).

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/migration/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/migration/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly](includes/migration/validate-that-the-supported-java-version-works-correctly.md)]

[!INCLUDE [inventory-jndi-resources](includes/migration/inventory-jndi-resources.md)]

[!INCLUDE [domain-configuration](includes/migration/domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/migration/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/migration/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/migration/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/migration/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/migration/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/migration/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/migration/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/migration/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/migration/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/migration/determine-whether-wlst-is-used.md)]

[!INCLUDE [validate-whether-and-how-the-file-system-is-used](includes/migration/validate-whether-and-how-the-file-system-is-used.md)]

[!INCLUDE [determine-the-network-topology](includes/migration/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/migration/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/migration/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/migration/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/migration/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>Migration

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Auswählen eines WebLogic-Angebots für Azure Virtual Machines

Die folgenden Angebote sind für WebLogic auf Azure Virtual Machines verfügbar.

Während der Bereitstellung eines Angebots werden Sie aufgefordert, die Größe des virtuellen Computers für Ihre WebLogic Server-Knoten auszuwählen. Es ist wichtig, bei der Auswahl der VM-Größe alle Größenaspekte (Arbeitsspeicher, Prozessor, Datenträger) zu berücksichtigen. Weitere Informationen finden Sie in der [Dokumentation zu den Angeboten](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/deploy-oracle-weblogic-server-administration-server-single-node.html) und in der [Azure-Dokumentation zur Größenanpassung virtueller Computer](/azure/cloud-services/cloud-services-sizes-specs).

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>WebLogic Server-Einzelknoten ohne Admin-Server

Bei diesem Angebot wird nur ein virtueller Computer erstellt, auf dem dann die Installation von WebLogic erfolgt, aber es werden keine Domänen konfiguriert. Dies ist nützlich für Szenarien, in denen Sie eine stark angepasste Domänenkonfiguration nutzen.

#### <a name="weblogic-server-single-node-with-admin-server"></a>WebLogic Server-Einzelknoten mit Admin-Server

Bei diesem Angebot wird nur ein virtueller Computer bereitgestellt und WebLogic Server 12.1.2.3 darauf installiert. Es wird eine Domäne erstellt und der Admin-Server gestartet.

#### <a name="weblogic-server-n-node-cluster"></a>WebLogic Server-Cluster mit „n“ Knoten

Bei diesem Angebot wird ein hoch verfügbarer Cluster mit WebLogic Server-VMs erstellt.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>Dynamischer WebLogic Server-Cluster mit „n“ Knoten

Bei diesem Angebot wird ein hoch verfügbarer und skalierbarer dynamischer Cluster mit WebLogic Server-VMs erstellt.

### <a name="provision-the-offer"></a>Bereitstellen des Angebots

Befolgen Sie nach der Auswahl des Anfangsangebots die Anleitung in der [entsprechenden Dokumentation](https://wls-eng.github.io/arm-oraclelinux-wls/), um das Angebot bereitzustellen. Achten Sie darauf, dass Sie den Domänennamen auswählen, der mit Ihrem vorhandenen Domänennamen übereinstimmt. Sie können auch das Domänenkennwort an Ihr vorhandenes Domänenkennwort anpassen.

### <a name="migrate-the-domains"></a>Migrieren der Domänen

Nachdem Sie das Angebot bereitgestellt haben, können Sie die Domänenkonfiguration untersuchen und die Informationen zum Migrieren der Domänen in [diesem Leitfaden](https://support.oracle.com/knowledge/Middleware/2336356_1.html) verwenden.

### <a name="connect-the-databases"></a>Verbinden der Datenbanken

Nachdem Sie die Domänen migriert haben, können Sie die Datenbanken verbinden, indem Sie die Anleitung in der [Dokumentation zum Angebot](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster) befolgen. Mit dieser Anleitung stellen Sie sicher, dass Sie die relevanten Datenbankgeheimnisse und Zugriffszeichenfolgen berücksichtigen.

### <a name="account-for-keystores"></a>Berücksichtigen von Keystores

Sie müssen die Migration der SSL-Keystores berücksichtigen, die von Ihrer Anwendung verwendet werden. Weitere Informationen finden Sie unter [Konfigurieren von Keystores](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC).

### <a name="connect-the-jms-sources"></a>Verbinden der JMS-Quellen

Nachdem Sie die Datenbanken verbunden haben, können Sie JMS konfigurieren, indem Sie in der WebLogic-Dokumentation die Anleitung unter [Fusion Middleware: Verwalten von JMS-Ressourcen für Oracle WebLogic Server](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm) befolgen.

### <a name="account-for-logging"></a>Berücksichtigen der Protokollierung

Die vorhandene Konfiguration der Protokollierung sollte beim Migrieren der Domäne übertragen werden. Weitere Informationen finden Sie unter [Konfigurieren von „java.util.logging“-Protokollierungsebenen](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlach/taskhelp/logging/ConfigureJavaLoggingLevels.html) und [Konfigurieren von Protokolldateien und Filtern von Protokollmeldungen für Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wllog/index.html).

### <a name="migrating-your-applications"></a>Migrieren Ihrer Anwendungen

Die verwendeten Verfahren zum Bereitstellen von Anwendungen über das Entwicklungsteam auf Test-, Staging- und Produktionsservern können von Fall zu Fall stark variieren. In einigen Fällen wird eine hoch entwickelte CI/CD-Plattform genutzt, die dazu führt, dass die Anwendungen auf dem WebLogic Server bereitgestellt werden. In anderen Fällen kann es sein, dass der Prozess manuell durchgeführt wird. Ein Vorteil der Nutzung von Azure Virtual Machines zum Migrieren von WebLogic-Anwendungen zur Cloud ist, dass Ihre vorhandenen Prozesse weiterhin funktionieren.

Sie müssen die über das Angebot bereitgestellte Netzwerksicherheitsgruppe konfigurieren, um den Zugriff auf Ihre CI/CD-Pipeline oder über das manuelle Bereitstellungssystem zuzulassen. Weitere Informationen finden Sie unter [Sicherheitsgruppen](/azure/virtual-network/security-overview) in der Azure-Dokumentation.

### <a name="testing"></a>Testen

Alle containerinternen Tests von Anwendungen müssen so konfiguriert werden, dass auf die neuen Server zugegriffen wird, die in Azure ausgeführt werden. Wie bei CI/CD auch, müssen Sie sicherstellen, dass für Ihre Tests gemäß den erforderlichen Netzwerksicherheitsregeln der Zugriff auf die Anwendungen möglich ist, die in Azure bereitgestellt werden. Weitere Informationen finden Sie unter [Sicherheitsgruppen](/azure/virtual-network/security-overview) in der Azure-Dokumentation.

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie die Migrationsziele erreicht haben, die Sie unter [Vor der Migration](#pre-migration) definiert haben, führen Sie einige End-to-End-Akzeptanztests durch. Hiermit soll sichergestellt werden, dass alles wie gewünscht funktioniert. Hier sind einige Themenbereiche für Verbesserungen nach der Migration aufgeführt (keine umfassende Liste):

* Verwenden Sie Azure Storage zum Bereitstellen von statischem Inhalt auf den virtuellen Computern. Weitere Informationen finden Sie unter [Anfügen oder Trennen eines Datenträgers an einen oder von einem virtuellen Computer in Azure DevTest Labs](/azure/lab-services/devtest-lab-attach-detach-data-disk).

* Stellen Sie Ihre Anwendungen mit Azure DevOps in Ihrem migrierten WebLogic-Cluster bereit. Weitere Informationen finden Sie in der [Azure DevOps-Dokumentation zu den ersten Schritten](/azure/devops/get-started/?view=azure-devops).

* Verbessern Sie Ihre Netzwerktopologie mit erweiterten Diensten für den Lastenausgleich. Weitere Informationen finden Sie unter [Verwenden von Lastenausgleichsdiensten in Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure).

* Nutzen Sie verwaltete Azure-Identitäten, um verwaltete Geheimnisse zu verwalten und Azure-Ressourcen den rollenbasierten Zugriff zuzuweisen. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](/azure/active-directory/managed-identities-azure-resources/overview).

* Integrieren Sie die WebLogic Java EE-Authentifizierung und -Autorisierung in Azure Active Directory. Weitere Informationen finden Sie unter [Erste Schritte zur Integration von Anwendungen in Azure Active Directory](/azure/active-directory/manage-apps/plan-an-application-integration).

* Verwenden Sie Azure Key Vault zum Speichern von Informationen, die als „Geheimnisse“ dienen. Weitere Informationen finden Sie unter [Grundlegende Konzepte von Azure Key Vault](/azure/key-vault/basic-concepts).
