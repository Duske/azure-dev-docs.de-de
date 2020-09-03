---
author: yevster
ms.author: yebronsh
ms.date: 8/25/2020
ms.openlocfilehash: 7970cae2b9ac39f7012e74b1334d12b2c5006af0
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062052"
---
### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Erstellen von Azure Spring Cloud-Instanz und Apps

Stellen Sie eine Azure Spring Cloud-Instanz in Ihrem Azure-Abonnement bereit, wenn noch keine solche vorhanden ist. Erstellen Sie dort anschließend eine Anwendung. Weitere Informationen finden Sie unter [Quickstart: Starten einer vorhandenen Azure Spring Cloud-Anwendung über das Azure-Portal](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal) vor.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](configure-persistent-storage-azure-spring-cloud.md)]

[!INCLUDE [migrate-all-certificates-to-keyvault-azure-spring-cloud](migrate-all-certificates-to-keyvault-azure-spring-cloud.md)]

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

![Spring Cloud: App-Konfigurationseinstellungen](../media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

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
