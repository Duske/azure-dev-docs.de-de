---
title: 'Tutorial: Migrieren eines WebLogic Server-Clusters zu Azure mit Azure Application Gateway als Lastenausgleich'
description: Dieses Tutorial führt Sie durch die Bereitstellung von WebLogic Server in Azure mit Azure Application Gateway als Lastenausgleich.
author: edburns
ms.author: edburns
ms.topic: tutorial
ms.date: 08/05/2020
ms.openlocfilehash: 166e6f90218eb519242da0d89ae6146a2d589863
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057551"
---
# <a name="tutorial-migrate-a-weblogic-server-cluster-to-azure-with-azure-application-gateway-as-a-load-balancer"></a>Tutorial: Migrieren eines WebLogic Server-Clusters zu Azure mit Azure Application Gateway als Lastenausgleich

Dieses Tutorial führt Sie durch den Prozess der Bereitstellung von WebLogic Server (WLS) mit Azure Application Gateway.  Es behandelt die spezifischen Schritte zum Erstellen einer Key Vault-Instanz, zum Speichern eines SSL-Zertifikats in der Key Vault-Instanz und zum Verwenden dieses Zertifikats für die SSL-Terminierung.  Diese einzelnen Elemente sind zwar jeweils gut dokumentiert, aber in diesem Tutorial wird gezeigt, wie alle diese Elemente zusammen eine einfache, aber leistungsfähige Lastenausgleichslösung für WLS in Azure bilden.

:::image type="content" border="false" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-key-vault.png" alt-text="Diagramm, das die Beziehung zwischen WLS, App Gateway und Key Vault zeigt":::

Der Lastenausgleich ist ein wesentlicher Bestandteil der Migration Ihres Oracle WebLogic Server-Clusters zu Azure.  Die einfachste Lösung besteht darin, die integrierte Unterstützung für [Azure Application Gateway](/azure/application-gateway/overview) zu verwenden.  App Gateway ist als Teil der WebLogic-Clusterunterstützung in Azure enthalten.  Eine Übersicht über die Unterstützung von WebLogic-Clustern in Azure finden Sie unter [Was ist Oracle WebLogic Server in Azure?](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Azure Key Vault-Instanz
> * Erstellen eines SSL-Zertifikats
> * Speichern des SSL-Zertifikats in Key Vault
> * Bereitstellen von WebLogic Server mit Azure Application Gateway in Azure
> * Überprüfen der erfolgreichen Bereitstellung von WLS und App Gateway

## <a name="prerequisites"></a>Voraussetzungen

* [OpenSSL](https://www.openssl.org/) auf einem Computer, auf dem eine UNIX-ähnliche Befehlszeilenumgebung ausgeführt wird.

   Zwar gibt es möglicherweise andere Tools für die Zertifikatverwaltung, in diesem Tutorial wird aber OpenSSL verwendet. OpenSSL gehört zu vielen GNU/Linux-Distributionen, so z. B. Ubuntu.
* Ein aktives Azure-Abonnement.
  * Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
* Die Fähigkeit, eine der in [Oracle WebLogic Server: Azure-Anwendungen](/azure/virtual-machines/workloads/oracle/oracle-weblogic) aufgeführten WLS-Azure-Anwendungen bereitzustellen.

## <a name="migration-context"></a>Migrationskontext

Hier werden einige Punkte erläutert, die im Zusammenhang mit der Migration von lokalen WLS-Installationen und Azure Application Gateway zu beachten sind.  Die Schritte in diesem Tutorial sind die einfachste Möglichkeit, einen Lastenausgleich vor Ihrem WebLogic Server-Cluster in Azure einzurichten, allerdings gibt es noch zahlreiche weitere Möglichkeiten.  Diese Liste enthält weitere Aspekte, die zu berücksichtigen sind.

* Wenn bereits Lastenausgleichslösung vorhanden ist, stellen Sie sicher, dass diese mindestens über die Funktionen von Azure Application Gateway verfügt.  Eine Zusammenfassung der Funktionen von Azure Application Gateway im Vergleich zu anderen Azure-Lastenausgleichslösungen finden Sie unter [Übersicht über Lastenausgleichsoptionen in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).
* Wenn Ihre vorhandene Lastenausgleichslösung Schutz vor gängigen Exploits und Sicherheitsrisiken bietet, ist Application Gateway für Sie geeignet. Die integrierte Web Application Firewall (WAF) von Application Gateway implementiert die Kernregelsätze von [OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project).  Weitere Informationen zur WAF-Unterstützung in Application Gateway finden Sie unter [Application Gateway-Funktionen](/azure/application-gateway/features#web-application-firewall).
* Wenn Ihre vorhandene Lastenausgleichslösung eine End-to-End-SSL-Verschlüsselung erfordert, müssen Sie nach dem Ausführen der Schritte in dieser Anleitung zusätzliche Konfigurationsschritte ausführen.  Weitere Informationen finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](/azure/application-gateway/ssl-overview#end-to-end-tls-encryption) und der Oracle-Dokumentation zur [Konfiguration von SSL in Oracle Fusion Middleware](https://docs.oracle.com/en/middleware/fusion-middleware/12.2.1.3/asadm/configuring-ssl1.html#GUID-623906C0-B1FD-423F-AE51-061B5800E927).
* Wenn Sie eine Optimierung für die Cloud durchführen, erfahren Sie in diesem Leitfaden, wie Sie mit Azure App Gateway und WLS von Grund auf neu beginnen.
* Eine umfassende Übersicht über die Migration von WebLogic Server zu Azure Virtual Machines finden Sie unter [Migrieren von WebLogic-Anwendungen zu virtuellen Azure-Computern](migrate-weblogic-to-virtual-machines.md).

## <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz

Dieser Abschnitt zeigt, wie eine Azure Key Vault-Instanz mithilfe des Azure-Portals erstellt wird.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
1. Geben Sie **Key Vault** in das Suchfeld ein.
1. Wählen Sie in der Ergebnisliste **Key Vault** aus.
1. Klicken Sie im Abschnitt „Key Vault“ auf **Erstellen**.
1. Geben Sie im Abschnitt **Schlüsseltresor erstellen** folgende Informationen ein:
    * **Abonnement**: Wählen Sie ein Abonnement aus.
    * Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie einen Ressourcengruppennamen ein.  Notieren Sie sich den Schlüsseltresornamen.  *Sie benötigen ihn später, wenn Sie WLS bereitstellen.*
    * **Name des Schlüsseltresors**: Es ist ein eindeutiger Name erforderlich.  Notieren Sie sich den Schlüsseltresornamen.  *Sie benötigen ihn später, wenn Sie WLS bereitstellen.*
    > [!NOTE]
    > Sie können den gleichen Namen für die **Ressourcengruppe** und den **Schlüsseltresor** verwenden.
    * Wählen Sie im Pulldownmenü **Speicherort** einen Speicherort aus.
    * Behalten Sie bei den anderen Optionen die Standardeinstellungen bei.
1. Klicken Sie auf **Weiter: Zugriffsrichtlinie**.
1. Wählen Sie unter **Zugriff ermöglichen auf** die Option **Azure Resource Manager für Vorlagenbereitstellung** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

Die Erstellung des Schlüsseltresors ist recht einfach und wird in der Regel in weniger als zwei Minuten abgeschlossen.  Wenn die Bereitstellung abgeschlossen ist, wählen Sie **Zu Ressource wechseln**, und fahren Sie mit dem nächsten Abschnitt fort.

## <a name="create-an-ssl-certificate"></a>Erstellen eines SSL-Zertifikats

In diesem Abschnitt wird gezeigt, wie Sie ein selbstsigniertes SSL-Zertifikat in einem Format erstellen, das von einer Application Gateway-Instanz verwendet werden kann, die mit WebLogic in Azure bereitgestellt ist.  Das Zertifikat muss über ein Kennwort verfügen, das nicht leer ist.  Wenn Sie bereits über ein gültiges SSL-Zertifikat mit nicht leerem Kennwort im *PFX*-Format verfügen, können Sie diesen Abschnitt überspringen und mit dem nächsten fortfahren.  Wenn Ihr vorhandenes gültiges SSL-Zertifikat mit nicht leerem Kennwort nicht im *PFX*-Format vorliegt, konvertieren Sie es zuerst in eine *PFX-* Datei, bevor Sie mit dem nächsten Abschnitt fortfahren.  Andernfalls öffnen Sie eine Befehlsshell, und geben Sie die folgenden Befehle ein.

> [!NOTE]
> In diesem Abschnitt wird gezeigt, wie Sie das Zertifikat mit Base64 codieren, bevor Sie es als Geheimnis in Key Vault speichern.  Dies ist für die zugrunde liegende Azure-Bereitstellung erforderlich, mit der die WebLogic Server- und Application Gateway-Instanz erstellt werden.

Führen Sie die folgenden Schritte aus, um das Zertifikat zu erstellen und mit Base64 zu codieren:

1. Erstellen Sie einen `RSA PRIVATE KEY`.

   ```bash
   openssl genrsa 2048 > private.pem
   ```
1. Erstellen Sie einen entsprechenden öffentlichen Schlüssel.

   ```bash
   openssl req -x509 -new -key private.pem -out public.pem
   ```

   Sie müssen mehrere Fragen beantworten, wenn Sie vom OpenSSL-Tool dazu aufgefordert werden.  Diese Werte werden in das Zertifikat aufgenommen.  In diesem Tutorial wird ein selbstsigniertes Zertifikat verwendet, daher sind die Werte nicht relevant.  Die folgenden Literalwerte sind in Ordnung.
     1. Geben Sie einen Code mit zwei Buchstaben als **Ländernamen** ein.
     1. Geben Sie „WA“ als **Name des Bundeslands bzw. des Kantons** ein.
     1. Geben Sie „Contoso“ als **Organisationsnamen** ein.  Geben Sie „Abrechnung“ als Name der Organisationseinheit ein.
     1. Geben Sie „Contoso“ als **allgemeinen Namen** ein.
     1. Geben Sie billing@contoso.com als **E-Mail-Adresse** ein.

1. Exportieren Sie das Zertifikat als *PFX*-Datei.

   ```bash
   openssl pkcs12 -export -in public.pem -inkey private.pem -out mycert.pfx
   ```

   Geben Sie das Kennwort zweimal ein.  Notieren Sie sich das Kennwort.  *Sie benötigen es später, wenn Sie WLS bereitstellen.*

1. Codieren Sie die Datei *mycert.pfx* mit Base64.

   ```bash
   base64 mycert.pfx > mycert.txt
   ```

Nachdem Sie nun eine Key Vault-Instanz erstellt haben und über ein gültiges SSL-Zertifikat mit einem nicht leeren Kennwort verfügen, können Sie das Zertifikat in Key Vault speichern.

## <a name="store-the-ssl-certificate-in-the-key-vault"></a>Speichern des SSL-Zertifikats in Key Vault

In diesem Abschnitt wird gezeigt, wie Sie das Zertifikat und sein Kennwort in der Key Vault-Instanz speichern, die Sie in den vorherigen Abschnitten erstellt haben.

Führen Sie die folgenden Schritte aus, um das Zertifikat zu speichern:

1. Platzieren Sie den Cursor im Azure-Portal oben auf der Seite in der Suchleiste, und geben Sie den Namen der Key Vault-Instanz ein, die Sie zuvor in diesem Tutorial erstellt haben.
1. Ihre Key Vault-Instanz sollte unter der Überschrift **Ressourcen** angezeigt werden.  Wählen Sie ihn aus.
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Geheimnis** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.
1. Lassen Sie unter **Uploadoptionen** den Standardwert stehen.
1. Geben Sie unter **Name** entweder `myCertSecretData` oder einen beliebigen Namen ein.
1. Geben Sie unter **Wert** den Inhalt der Datei *mycert.txt* ein.  Die Länge des Werts und das Vorhandensein von Zeilenumbrüche stellen kein Problem für das Textfeld dar.
1. Behalten Sie für die restlichen Werte die Standardwerte bei, und wählen Sie **Erstellen** aus.

Führen Sie die folgenden Schritte aus, um das Kennwort für das Zertifikat zu speichern:

1. Sie werden zur **Geheimnis** zurückgeleitet.  Wählen Sie die Option **Generieren/Importieren** aus.
1. Lassen Sie unter **Uploadoptionen** den Standardwert stehen.
1. Geben Sie unter **Name** entweder `myCertSecretPassword` oder einen beliebigen Namen ein.
1. Geben Sie unter **Wert** das Kennwort für das Zertifikat ein.
1. Behalten Sie für die restlichen Werte die Standardwerte bei, und wählen Sie **Erstellen** aus.
1. Sie werden zur **Geheimnis** zurückgeleitet.

## <a name="deploy-weblogic-server-with-application-gateway-to-azure"></a>Bereitstellen von WebLogic Server mit Application Gateway in Azure

In diesem Abschnitt wird gezeigt, wie Sie die Key Vault-Instanz, das SSL-Zertifikat und das Kennwort verwenden, die in den vorherigen Abschnitten erstellt wurden.  Sie stellen einen WLS-Cluster mit der Azure Application Gateway-Instanz bereit, die automatisch als Lastenausgleich für die Clusterknoten erstellt wurde.  Application Gateway verwendet das bereitgestellte SSL-Zertifikat für die SSL-Terminierung.  Ausführlichere Informationen zur SSL-Terminierung mit Application Gateway finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](/azure/application-gateway/ssl-overview).

Führen Sie die folgenden Schritte aus, um den WLS-Cluster und eine Application Gateway-Instanz zu erstellen:

1. Beginnen Sie mit den folgenden Schritten zum Bereitstellen eines WebLogic Server-Clusters gemäß der Beschreibung in der [Oracle-Dokumentation](https://aka.ms/arm-oraclelinux-wls-cluster-oracle-docs), kehren Sie jedoch auf diese Seite zurück, wenn Sie das hier gezeigte Blatte **Azure Application Gateway** erreichen.

   :::image type="content" source="media/migrate-weblogic-with-app-gateway/weblogic-app-gateway-blade.png" alt-text="Diagramm, das die Beziehung zwischen WLS, App Gateway und Key Vault zeigt":::

1. Wählen Sie auf dem Blatt **Azure Application Gateway** **Ja** aus.
1. Geben Sie als **Ressourcengruppennamen im aktuellen Abonnement, das die Key Vault-Instanz enthält**, den Namen der Ressourcengruppe ein, die die Key Vault-Instanz enthält, die Sie zuvor erstellt haben.
1. Geben Sie als **Namen der Azure Key Vault-Instanz, die Geheimnisse für das Zertifikat zur SSL-Terminierung enthält**, den Namen der Key Vault-Instanz ein.
1. Geben Sie als **Namen des Geheimnisses in der angegebenen Key Vault-Instanz, dessen Wert den SSL-Zertifikatsdaten entspricht**, entweder `myCertSecretData` oder den Namen ein, den Sie zuvor eingegeben haben.
1. Geben Sie als **Namen des Geheimnisses in der angegebenen Key Vault-Instanz, dessen Wert dem Kennwort für das SSL-Zertifikat entspricht**, entweder `myCertSecretData` oder den Namen ein, den Sie zuvor eingegeben haben.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.  Dies führt zu einer Überprüfung, dass das Zertifikat von der Key Vault-Instanz abgerufen werden kann, und dass sein Kennwort mit dem Wert übereinstimmt, den Sie als Kennwort in der Key Vault-Instanz gespeichert haben.  Wenn dieser Überprüfungsschritt fehlschlägt, überprüfen Sie die Eigenschaften der Key Vault-Instanz, und stellen Sie sicher, dass das Zertifikat und das Kennwort richtig eingegeben wurden.
1. Wenn **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Dadurch wird der Vorgang zum Erstellen des WLS-Clusters und dessen Application Gateway-Instanz am Front-End gestartet, was etwa 15 Minuten dauert.  Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressourcengruppe wechseln**. Wählen Sie in der Liste der Ressourcen in der Ressourcengruppe die Option **myAppGateway** aus.

## <a name="validate-successful-deployment-of-wls-and-app-gateway"></a>Überprüfen der erfolgreichen Bereitstellung von WLS und App Gateway

In diesem Abschnitt wird ein Verfahren zum schnellen Überprüfen der erfolgreichen Bereitstellung des WLS-Clusters und der Application Gateway-Instanz erläutert.

Wenn Sie am Ende des vorherigen Abschnitts **Zu Ressourcengruppe wechseln** und dann **myAppGateway** ausgewählt haben, wird die Übersichtsseite für die Application Gateway-Instanz angezeigt.  Andernfalls finden Sie diese Seite, indem Sie im Azure-Portal oben im Suchfeld `myAppGateway` eingeben und dann das richtige Ergebnis auswählen.  Achten Sie darauf, das Gateway in der Ressourcengruppe auszuwählen, das Sie für den WLS-Cluster erstellt haben.  Führen Sie anschließend die folgenden Schritte aus.

1. Scrollen Sie im linken Bereich der Übersichtsseite für **myAppGateway** nach unten zum Abschnitt **Überwachung**, und wählen Sie **Back-End-Integrität** aus.
1. Nachdem die Meldung **Laden** nicht mehr angezeigt wird, sollte in der Mitte des Bildschirms eine Tabelle mit den Knoten des Clusters angezeigt werden, die als Knoten im Back-End-Pool konfiguriert sind.
1. Vergewissern Sie sich, dass für jeden Knoten der Status **Fehlerfrei** angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den WLS-Cluster nicht weiter verwenden möchten, löschen Sie die Key Vault-Instanz und den WLS-Cluster mit den folgenden Schritten:

1. Rufen Sie die Übersichtsseite für **myAppGateway** auf, wie im vorherigen Abschnitt gezeigt.
1. Wählen Sie oben auf der Seite unter dem Text **Ressourcengruppe** die Ressourcengruppe aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Der Eingabefokus wird auf das Feld mit der Bezeichnung **RESSOURCENGRUPPENNAMEN EINGEBEN** festgelegt.  Geben Sie den Namen der Ressourcengruppe wie angefordert ein.
1. Dadurch wird die Schaltfläche **Löschen** aktiviert.  Klicken Sie auf die Schaltfläche **Löschen**.  Dieser Vorgang nimmt einige Zeit in Anspruch, aber Sie können mit dem nächsten Schritt fortfahren, während der Löschvorgang ausgeführt wird.
1. Suchen Sie die Key Vault-Instanz, indem Sie den ersten Schritt des Abschnitts [Speichern des SSL-Zertifikats in Key Vault]() ausführen.
1. Klicken Sie auf **Löschen**.
1. Wählen Sie im angezeigten Bereich **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich weiter über die Optionen zum Ausführen von WLS in Azure.
> [!div class="nextstepaction"]
> [Weitere Informationen zu Oracle WebLogic in Azure](/azure/virtual-machines/workloads/oracle/oracle-weblogic)
