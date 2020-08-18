---
author: edburns
ms.author: edburns
ms.date: 08/09/2020
ms.openlocfilehash: e006f2ec9d6f0d3b2d83a6653d65da19489dc221
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052256"
---
### <a name="account-for-authentication-and-authorization"></a>Konto für Authentifizierung und Autorisierung

Die meisten Anwendungen verfügen über eine Art von Authentifizierung und Autorisierung.  Wenn Sie LDAP für die Authentifizierung verwenden, unterstützt WebLogic Server in Azure die automatische Integration. Das Marketplace-Angebot verwendet Azure Active Directory Domain Services (Azure AD DS) mit sicherem LDAP.  Das Angebot erstellt den Standardbereich für WebLogic Server aus Daten in Azure AD DS.  Weitere Informationen finden Sie unter [Autorisierung und Authentifizierung von Endbenutzern für die Migration von Java-Apps in WebLogic Server zu Azure](../migrate-weblogic-with-aad-ldap.md).
