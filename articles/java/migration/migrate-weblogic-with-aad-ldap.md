---
title: Autorisierung und Authentifizierung von Endbenutzern mit Azure Active Directory für die Migration von Java-Apps in WebLogic Server zu Azure
description: In diesem Leitfaden erfahren Sie, wie Sie Oracle WebLogic Server konfigurieren, um über LDAP eine Verbindung mit Azure Active Directory Domain Services herzustellen.
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/10/2020
ms.openlocfilehash: 58a36de1e52415fc563b294215818b1860cd7ba2
ms.sourcegitcommit: b0a119a624e9cb6b76d968951543a414bd08eaa0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220379"
---
# <a name="end-user-authorization-and-authentication-for-migrating-java-apps-on-weblogic-server-to-azure"></a>Autorisierung und Authentifizierung von Endbenutzern für die Migration von Java-Apps in WebLogic Server zu Azure

Dieser Leitfaden unterstützt Sie bei der Aktivierung der professionellen Authentifizierung und Autorisierung von Endbenutzern für Java-Apps in WebLogic Server unter Verwendung von Azure Active Directory.

Java EE-Entwickler verlassen sich darauf, dass die [standardmäßigen Plattformsicherheitsmechanismen](https://javaee.github.io/tutorial/security-intro.html#BNBWJ) einfach funktionieren, auch wenn sie ihre Workloads zu Azure migrieren.  Bei [Oracle WLS-Anwendungen (WebLogic Server)](/azure/virtual-machines/workloads/oracle/oracle-weblogic) können Sie den integrierten Sicherheitsbereich mit Benutzern aus Azure Active Directory Domain Services (Azure AD DS) füllen.  Verwenden Sie das Standardelement `<security-role>` in Ihrer Java EE-Anwendungen in Azure. Die Benutzerinformationen werden von Azure AD DS per Lightweight Directory Access Protocol (LDAP) übertragen.

Dieser Leitfaden ist in zwei Teile gegliedert.  Falls Sie bereits Azure AD DS mit Secure LDAP verfügbar gemacht haben, können Sie direkt mit dem zweiten Teil fortfahren.

* [Azure Active Directory-Konfiguration](#azure-active-directory-configuration)
* [WLS-Konfiguration](#wls-configuration)

In diesem Leitfaden wird Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne
> * Konfigurieren von Secure LDAP (Lightweight Directory Access Protocol) für eine verwaltete Azure AD DS-Domäne
> * Ermöglichen des WebLogic Server-Zugriffs auf LDAP als Standardsicherheitsbereich

Dieser Leitfaden hilft Ihnen zwar nicht beim Ändern der Konfiguration einer vorhandenen Azure AD-Bereitstellung, Sie sollten dem Leitfaden aber folgen und die überspringbaren Schritte erkennen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Abonnement.
  * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
* Die Fähigkeit, eine der in [Oracle WebLogic Server: Azure-Anwendungen](/azure/virtual-machines/workloads/oracle/oracle-weblogic) aufgeführten WLS-Azure-Anwendungen bereitzustellen.

## <a name="migration-context"></a>Migrationskontext

In diesem Abschnitt werden einige Punkte erläutert, die im Zusammenhang mit der Migration von lokalen WLS-Installationen und Azure AD zu beachten sind.

* Wenn Sie bereits über einen Azure AD-Mandanten verfügen, bei dem Domain Services nicht über LDAP verfügbar gemacht wird, erfahren Sie in diesem Leitfaden, wie Sie die LDAP-Funktion verfügbar machen und in WLS integrieren.
* Sollte Ihr Szenario eine lokale Active Directory-Gesamtstruktur beinhalten, empfiehlt sich ggf. die Implementierung einer Hybrididentitätslösung mit Azure AD.  Weitere Informationen finden Sie in der [Dokumentation zur Hybrid-Identität](/azure/active-directory/hybrid/).
* Wenn Sie bereits über eine lokale AD DS-Bereitstellung (Active Directory Domain Services) verfügen, informieren Sie sich unter [Vergleichen von selbstverwalteten Active Directory Domain Services, Azure Active Directory und verwalteten Azure Active Directory Domain Services](/azure/active-directory-domain-services/compare-identity-solutions) über mögliche Migrationspfade.
* Wenn Sie eine Optimierung für die Cloud durchführen, erfahren Sie in diesem Leitfaden, wie Sie mit Azure AD DS-LDAP und WLS von Grund auf neu beginnen.
* Eine umfassende Übersicht über die Migration von WebLogic Server zu Azure Virtual Machines finden Sie unter [Migrieren von WebLogic-Anwendungen zu virtuellen Azure-Computern](migrate-weblogic-to-virtual-machines.md).

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-Konfiguration

In diesem Abschnitt werden die Schritte zum Einrichten einer Azure AD DS-Instanz mit WLS-Integration erläutert.  LDAP (Lightweight Directory Access Protocol) oder Secure LDAP wird von Azure Active Directory nicht direkt unterstützt. Stattdessen wird die Unterstützung über die Azure AD DS-Instanz (Azure AD Domain Services) innerhalb Ihres Azure AD-Mandanten ermöglicht.

>[!NOTE]
> In diesem Leitfaden wird das Feature für reine Cloudbenutzerkonten von Azure AD DS verwendet.  Andere Arten von Benutzerkonten werden zwar unterstützt, aber in diesem Handbuch nicht beschrieben.

### <a name="create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne

In diesem Abschnitt wird ein separates Tutorial zum Einrichten einer verwalteten Azure AD DS-Domäne beschrieben.

Durchlaufen Sie das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne](/azure/active-directory-domain-services/tutorial-create-instance) bis zum Abschnitt [Aktivieren von Benutzerkonten für Azure AD DS](/azure/active-directory-domain-services/tutorial-create-instance#enable-user-accounts-for-azure-ad-ds).  Dieser Abschnitt erfordert im Kontext dieses Tutorials eine spezielle Vorgehensweise, wie im nächsten Abschnitt beschrieben.  Führen Sie die DNS-Aktionen vollständig und korrekt aus.

Notieren Sie sich den Wert, den Sie im Rahmen des Schritts „Geben Sie einen DNS-Domänennamen für Ihre verwaltete Domäne ein...“ angegeben haben.  Sie werden ihn später in diesem Artikel brauchen.

### <a name="create-users-and-reset-passwords"></a>Erstellen von Benutzern und Zurücksetzen von Kennwörtern

Dieser Abschnitt enthält die Schritte zum Erstellen von Benutzern und zum Ändern ihres Kennworts. Dies ist erforderlich, damit die Benutzer erfolgreich über LDAP verteilt werden können.  Falls Sie über eine bereits vorhandene Azure AD DS-Installation verfügen, ist dieser Schritt möglicherweise nicht erforderlich.

1. Vergewissern Sie sich im Azure-Portal, dass das dem Azure AD-Mandanten entsprechende Verzeichnis das derzeit aktive Verzeichnis ist.  Informationen zum Auswählen des korrekten Verzeichnisses finden Sie unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory).  Wenn das falsche Verzeichnis ausgewählt ist, können entweder keine Benutzer erstellt werden, oder die Benutzer werden im falschen Verzeichnis erstellt.
1. Geben Sie oben im Azure-Portal den Suchbegriff „Benutzer“ in das Suchfeld ein.
1. Wählen Sie **Neuer Benutzer** aus.
1. Vergewissern Sie sich, dass **Benutzer erstellen** ausgewählt ist.
1. Geben Sie Werte für Benutzername, Name, Vorname und Nachname ein.  Behalten Sie in den übrigen Feldern die Standardwerte bei.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie den neu erstellten Benutzer in der Tabelle aus.
1. Wählen Sie **Kennwort zurücksetzen** aus.
1. Wählen Sie im angezeigten Bereich **Kennwort zurücksetzen** aus.
1. Notieren Sie sich das temporäre Kennwort.
1. Besuchen Sie in einem Inkognito-Browserfenster das [Azure-Portal](https://portal.azure.com/), und melden Sie sich mit den Anmeldeinformationen und dem Kennwort des Benutzers an.
1. Ändern Sie das Kennwort, wenn Sie dazu aufgefordert werden.  Notieren Sie sich das neue Kennwort.  Es wird später noch benötigt.
1. Melden Sie sich ab, und schließen Sie das Inkognito-Fenster.

Wiederholen Sie die Schritte von „Wählen Sie **Neuer Benutzer** aus.“ bis einschließlich „Melden Sie sich ab, und schließen Sie das Inkognito-Fenster.“ für jeden Benutzer, den Sie aktivieren möchten.

### <a name="allow-ldap-in-azure-ad-ds"></a>Zulassen von LDAP in Azure AD DS

In diesem Abschnitt wird ein separates Tutorial zum Extrahieren von Werten für die Konfiguration von WLS durchlaufen.

Öffnen Sie zunächst das Tutorial [Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](/azure/active-directory-domain-services/tutorial-configure-ldaps) in einem separaten Browserfenster, damit Sie sich beim Durchlaufen des Tutorials die folgenden Abweichungen ansehen können.  

Notieren Sie sich im Abschnitt [Exportieren eines Zertifikats für Clientcomputer](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers) den Speicherort der Zertifikatdatei (Datei mit der Erweiterung *.cer*).  Das Zertifikat wird als Eingabe für die WLS-Konfiguration verwendet.

Geben Sie im Abschnitt [Beschränken des Secure LDAP-Zugriffs über das Internet](/azure/active-directory-domain-services/tutorial-configure-ldaps#lock-down-secure-ldap-access-over-the-internet) für die Quelle den Wert **Beliebig** an.  Die Sicherheitsregel wird später in diesem Leitfaden noch mit einer bestimmten IP-Adresse verstärkt.

Führen Sie vor den unter [Testen von Abfragen in der verwalteten Domäne](/azure/active-directory-domain-services/tutorial-configure-ldaps#test-queries-to-the-managed-domain) beschriebenen Schritten erst die folgenden Schritte aus, um erfolgreich testen zu können.

   1. Besuchen Sie im Portal die Übersichtsseite für die Azure AD Domain Services-Instanz.
   1. Wählen Sie im Bereich mit den Einstellungen die Option **Eigenschaften** aus.
   1. Scrollen Sie im rechten Bereich der Seite nach unten zu **Administratorgruppe**.  Unter dieser Überschrift sollte sich ein Link für **AAD DC Administrators** (AAD-DC-Administratoren) befinden.  Klicken Sie auf diesen Link.
   1. Wählen Sie im Abschnitt **Verwalten** die Option **Mitglieder** aus.
   1. Wählen Sie **Mitglieder hinzufügen** aus.
   1. Geben Sie im Textfeld **Suchen** einige Zeichen ein, um nach einem der Benutzer zu suchen, die Sie in einem vorherigen Schritt erstellt haben.
   1. Wählen Sie den Benutzer aus, und aktivieren Sie die Schaltfläche **Auswählen**.
   1. Dieser Benutzer muss beim Ausführen der Schritte im Abschnitt **Testen von Abfragen in der verwalteten Domäne** verwendet werden.
   >[!NOTE]
   >
   > Im Anschluss folgen einige Tipps zum Abfragen der LDAP-Daten. Dies ist erforderlich, um einige Werte zu erfassen, die für die WLS-Konfiguration erforderlich sind.
   >
   > * Im Tutorial wird die Verwendung des Windows-Programms *LDP.exe* empfohlen.  Dieses Programm ist nur unter Windows verfügbar.  Nicht-Windows-Benutzer können auch [Apache Directory Studio](https://directory.apache.org/studio/downloads.html) verwenden.
   > * Wenn Sie sich mit *LDP.exe* bei LDAP anmelden, ist der Benutzername lediglich der Teil vor @.  Beim Benutzer `alice@contoso.onmicrosoft.com` lautet der Benutzername für die Bindungsaktion von *LDP.exe* also `alice`.  Lassen Sie *LDP.exe* außerdem für die weiteren Schritte ausgeführt und angemeldet.
   >
Notieren Sie sich im Abschnitt [Konfigurieren einer DNS-Zone für den externen Zugriff](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access) den Wert für **Externe Secure LDAP-IP-Adresse**.  Es wird später noch benötigt.

Wenn der Wert der **externen Secure LDAP-IP-Adresse** nicht direkt angezeigt wird, führen Sie die folgenden Schritte aus, um die IP-Adresse zu erhalten.

1. Suchen Sie im Portal die Ressourcengruppe, die die Azure AD Domain Services-Ressource enthält.
1. Wählen Sie in der Liste der Ressourcen die öffentliche IP-Ressource für die Azure AD Domain Services-Ressource aus, wie in der folgenden Abbildung dargestellt.  Die öffentliche IP-Adresse beginnt wahrscheinlich mit `aads`.
   :::image type="content" source="media/migrate-weblogic-with-aad-ldap/alternate-secure-ip-address-technique.png" alt-text="Browser, in dem die Auswahl der öffentlichen IP-Adresse angezeigt wird.":::
1. Die öffentliche IP-Adresse wird neben der Bezeichnung **IP-Adresse** angezeigt.

Führen Sie die Schritte unter [Bereinigen von Ressourcen](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) erst aus, wenn Sie in diesem Leitfaden dazu aufgefordert werden.

Führen Sie die unter [Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](/azure/active-directory-domain-services/tutorial-configure-ldaps) beschriebenen Schritte aus, und berücksichtigen Sie dabei die oben aufgeführten Abweichungen.  Nun können die für die WLS-Konfiguration erforderlichen Werte erfasst werden.

>[!NOTE]
> Warten Sie, bis die Verarbeitung der Secure LDAP-Konfiguration abgeschlossen ist, bevor Sie mit dem nächsten Abschnitt fortfahren.

### <a name="disable-weak-tls-v1"></a>Deaktivieren der unsicheren TLS v1

Standardmäßig ermöglicht Azure Active Directory Domain Services (Azure AD DS) die Verwendung von TLS v1. Diese gilt als unsicher und wird ab WebLogic Server 14 nicht mehr unterstützt. 

In diesem Abschnitt erfahren Sie, wie Sie das TLS v1-Verschlüsselungsverfahren deaktivieren.

Rufen Sie zunächst die Ressourcen-ID der Azure Domain Service-Instanz ab, die LDAP ermöglicht. Im folgenden Beispiel wird die ID einer Azure Domain Service-Instanz namens `aaddscontoso.com` in einer Ressourcengruppe mit dem Namen `aadds-rg` abgerufen.

```azurecli
AADDS_ID=$(az resource show --resource-group aadds-rg --resource-type "Microsoft.AAD/DomainServices" --name aaddscontoso.com --query "id" --output tsv)
```

Führen Sie den folgenden Befehl aus, um TLS v1 zu deaktivieren:

```azurecli
az resource update --ids $AADDS_ID --set properties.domainSecuritySettings.tlsV1=Disabled
```

Die Ausgabe zeigt `"tlsV1": "Disabled"` für `domainSecuritySettings` an, wie im folgenden Beispiel gezeigt:

```text
"domainSecuritySettings": {
      "ntlmV1": "Enabled",
      "syncKerberosPasswords": "Enabled",
      "syncNtlmPasswords": "Enabled",
      "syncOnPremPasswords": "Enabled",
      "tlsV1": "Disabled"
}
```

Weitere Informationen finden Sie unter [Deaktivieren von schwachen Verschlüsselungen und der Kennworthashsynchronisierung zum Schützen einer verwalteten Azure Active Directory Domain Services-Domäne](/azure/active-directory-domain-services/secure-your-domain).

## <a name="wls-configuration"></a>WLS-Konfiguration

In diesem Abschnitt erfahren Sie, wie Sie die Parameterwerte aus den zuvor bereitgestellten Azure AD DS-Instanz erfassen.

Wenn Sie eine der in [Oracle WebLogic Server: Azure-Anwendungen](/azure/virtual-machines/workloads/oracle/oracle-weblogic) aufgeführten Anwendungen bereitstellen, können Sie auswählen, dass von der Bereitstellung automatisch eine Verbindung mit einem bereits vorhandenen LDAP-Server hergestellt werden soll.  Alternativ können Sie die LDAP-Verbindung auch später durch Aufrufen der untergeordneten Vorlage für die Active Directory-Integration konfigurieren.  Diese Vorgehensweise wird in Anhang A der [offiziellen Dokumentation](https://wls-eng.github.io/arm-oraclelinux-wls/) beschrieben.  In beiden Fällen benötigen Sie jedoch die erforderlichen Parameterwerte, um sie an die ARM-Vorlage zu übergeben.

| Parametername | BESCHREIBUNG   | Details | 
|----------------|---------------|---------|
| `aadsServerHost` | Serverhost | Dieser Wert ist der öffentliche DNS-Name, den Sie unter [Tutorial: Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne](/azure/active-directory-domain-services/tutorial-create-instance) gespeichert haben. |
| `aadsPublicIP` | Externe Secure LDAP-IP-Adresse | Bei diesem Wert handelt es sich um die **externe Secure LDAP-IP-Adresse**, die Sie im Abschnitt [Konfigurieren einer DNS-Zone für den externen Zugriff](/azure/active-directory-domain-services/tutorial-configure-ldaps#configure-dns-zone-for-external-access) gespeichert haben.|
| `wlsLDAPPrincipal` | Prinzipal   | Kehren Sie zu *LDP.exe* zurück.  Führen Sie die folgenden Schritte aus, um einen zusätzlichen Wert für `wlsLDAPPrincipal` zu erhalten. <ol><li>Wählen Sie im Menü **Ansicht** die Option **Struktur** aus.</li><li>Lassen Sie im Dialogfeld **Strukturansicht** den Wert **BaseDN** leer, und wählen Sie **OK** aus.</li><li>Klicken Sie mit der rechten Maustaste auf den rechten Bereich, und wählen Sie **Ausgabe löschen** aus.</li><li>Erweitern Sie die Strukturansicht auf der linken Seite, und wählen Sie den Eintrag aus, der mit „OU=AADDC Users“ beginnt.</li><li>Wählen Sie im Menü **Durchsuchen** die Option **Suchen** aus.</li><li>Übernehmen Sie im angezeigten Dialogfeld die Standardeinstellungen, und wählen Sie **Ausführen** aus.</li><li>Warten Sie, bis im rechten Bereich eine Ausgabe angezeigt wird, und wählen Sie anschließend neben **Ausführen** die Option **Schließen** aus.</li><li>Überprüfen Sie die Ausgabe für den Eintrag **Dn**, der dem Benutzer entspricht, den Sie der Gruppe „AAD DC Administrators“ (AAD-DC-Administratoren) hinzugefügt haben.  Er beginnt mit **Dn: CN=&lt;Benutzername&gt;OU=AADDC Users**.</li></ol> |
| `wlsLDAPGroupBaseDN` und `wlsLDAPUserBaseDN` | Benutzerbasis-DN und Gruppenbasis-DN | Im Rahmen dieses Tutorials werden für beide Eigenschaften die gleichen Werte verwendet (der Teil von **wlsLDAPPrincipal** nach dem ersten Komma).|
| `wlsLDAPPrincipalPassword` | Kennwort für Prinzipal | Dieser Wert ist das Kennwort für den Benutzer, der der Gruppe **AAD DC Administrators** (AAD-DC-Administratoren) hinzugefügt wurde. |
| `wlsLDAPProviderName` | Anbietername | Für diesen Wert kann der Standardwert beibehalten werden.  Er wird als Name des Authentifizierungsanbieters in WLS verwendet. |
| `wlsLDAPSSLCertificate` | Öffentlicher Schlüssel für LDAPS-Verbindung von Azure AD DS | Bei diesem Wert handelt es sich um die Datei mit der Erweiterung *.cer*, die Sie nach Abschluss des Schritts [Exportieren eines Zertifikats für Clientcomputer](/azure/active-directory-domain-services/tutorial-configure-ldaps#export-a-certificate-for-client-computers) gespeichert haben.

### <a name="integrating-azure-ad-ds-ldap-with-wls"></a>Integrieren von Azure AD DS-LDAP in WLS

Wenn Sie über die oben genannten Konfigurationswerte verfügen und Azure AD DS-LDAP bereitgestellt wurde, können Sie die Konfiguration starten.  Es gibt zwei mögliche Vorgehensweisen:

#### <a name="during-wls-deployment"></a>Im Rahmen der WLS-Bereitstellung

Besuchen Sie [Oracle WebLogic Server: Azure-Anwendungen](/azure/virtual-machines/workloads/oracle/oracle-weblogic), und wählen Sie das Angebot mit Verwaltungsserver oder eines der Clusterangebote aus.  Während der Bereitstellung des Angebots wird unter anderem die Registerkarte **Azure Active Directory** angezeigt.  Legen Sie **Connect to Azure Active Directory** (Mit Azure Active Directory verbinden) auf **Ja** fest.  Füllen Sie die Werte auf der Grundlage der im vorherigen Abschnitt erfassten Informationen aus.  Für das Zertifikat muss die Datei mit der Erweiterung `.cer` direkt hochgeladen werden.

#### <a name="after-wls-deployment"></a>Nach der WLS-Bereitstellung

Wenn Sie **Connect to Azure Active Directory** (Mit Azure Active Directory verbinden) während der Bereitstellung nicht auf **Ja** festlegen, können Sie die Konfiguration mithilfe der im vorherigen Abschnitt erfassten Werte auch zu einem späteren Zeitpunkt durchzuführen.  Weitere Informationen finden Sie der [offiziellen Dokumentation](https://wls-eng.github.io/arm-oraclelinux-wls/).

### <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Vergewissern Sie sich mithilfe der folgenden Schritte, dass die Integration erfolgreich war, nachdem Sie WLS bereitgestellt und LDAP mit einer beiden oben genannten Methoden konfiguriert haben:

1. Besuchen Sie die WLS-Verwaltungskonsole.
1. Erweitern Sie die Struktur im linken Navigationsbereich, und wählen Sie **Security Realms** -> **myrealm** -> **Providers** („Sicherheitsbereiche“ > „myrealm“ > „Anbieter“) aus.
1. Bei erfolgreicher Integration wird der Azure AD-Anbieter angezeigt (beispielsweise `AzureActiveDirectoryProvider`).
1. Erweitern Sie die Struktur im linken Navigationsbereich, und wählen Sie **Security Realms** -> **myrealm** -> **Users and Groups** („Sicherheitsbereiche“ > „myrealm“ > „Benutzer und Gruppen“) aus.
1. Bei erfolgreicher Integration werden Benutzer aus dem Azure AD-Anbieter angezeigt.

### <a name="lock-down-and-secure-ldap-access-over-the-internet"></a>Beschränken des Secure LDAP-Zugriffs über das Internet

Beim Einrichten von Secure LDAP in den vorherigen Schritten wurde die Quelle für die Regel `AllowLDAPS` in der Netzwerksicherheitsgruppe auf **Beliebig** festgelegt.  Nachdem der WLS-Verwaltungsserver bereitgestellt und mit LDAP verbunden wurde, können Sie seine öffentliche IP-Adresse über das Azure-Portal abrufen.  Kehren Sie zu [Beschränken des Secure LDAP-Zugriffs über das Internet](/azure/active-directory-domain-services/tutorial-configure-ldaps#lock-down-secure-ldap-access-over-the-internet) zurück, und ändern Sie **Beliebig** in die spezifische IP-Adresse des WLS-Verwaltungsservers.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nun können Sie unter [Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) die Schritte des Abschnitts [Bereinigen von Ressourcen](/azure/active-directory-domain-services/tutorial-configure-ldaps#clean-up-resources) ausführen.

## <a name="next-steps"></a>Nächste Schritte

Erkunden Sie andere Aspekte der Migration von WebLogic Server-Apps zu Azure.

> [!div class="nextstepaction"]
> [Migrieren von WebLogic Server-Anwendungen zu virtuellen Azure-Computern](./migrate-weblogic-to-virtual-machines.md)
