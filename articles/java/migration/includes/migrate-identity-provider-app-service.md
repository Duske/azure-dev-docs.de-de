---
author: yevster
ms.author: yebronsh
ms.date: 5/19/2020
ms.openlocfilehash: 51ed106fde71e37467571baab2b327b092dddc15
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507630"
---
### <a name="migrate-and-enable-the-identity-provider"></a>Migrieren und Aktivieren des Identitätsanbieters

Sollte für Ihre Anwendung Authentifizierung oder Autorisierung erforderlich sein, stellen Sie anhand der folgenden Anweisungen sicher, dass sie für Zugriff auf den Identitätsanbieter konfiguriert ist:

* Wenn es sich bei dem Identitätsanbieter um Azure Active Directory handelt, sollten keine Änderungen erforderlich sein.
* Handelt es sich bei dem Identitätsanbieter um eine lokale Active Directory-Gesamtstruktur, empfiehlt sich ggf. die Implementierung einer Hybrididentitätslösung mit Azure Active Directory. Weitere Informationen finden Sie in der [Dokumentation zur Hybrid-Identität](/azure/active-directory/hybrid/).
* Wenn es sich bei dem Identitätsanbieter um eine andere lokale Lösung (beispielsweise PingFederate) handelt, erfahren Sie im Thema [Benutzerdefinierte Installation von Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom), wie Sie einen Verbund mit Azure Active Directory konfigurieren. Alternativ können Sie Spring Security nutzen, um Ihren Identitätsanbieter über [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) oder [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2) zu verwenden.
