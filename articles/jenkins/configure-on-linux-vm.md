---
title: 'Schnellstart: Erstellen eines Jenkins-Servers in Azure'
description: Es wird beschrieben, wie Sie Jenkins über die Jenkins-Lösungsvorlage auf einem virtuellen Azure-Linux-Computer installieren und eine Java-Beispielanwendung erstellen.
keywords: Jenkins, Azure, DevOps, Portal, Linux, virtueller Computer, Lösungsvorlage
ms.topic: quickstart
ms.date: 03/03/2020
ms.openlocfilehash: 2ea038dad2294784804f45026ea26198a9b12d79
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82170336"
---
# <a name="quickstart-create-a-jenkins-server-on-an-azure-linux-vm"></a>Schnellstart: Erstellen eines Jenkins-Servers auf einem virtuellen Azure-Linux-Computer

In dieser Schnellstartanleitung erfahren Sie, wie Sie [Jenkins](https://jenkins.io) auf einem virtuellen Computer unter Ubuntu Linux mit den Tools und Plug-Ins installieren, die für die Arbeit mit Azure konfiguriert sind. Dadurch erhalten Sie einen in Azure ausgeführten Jenkins-Server, der eine Java-Beispielanwendung aus [GitHub](https://github.com) erstellt.

## <a name="prerequisites"></a>Voraussetzungen

* Zugriff auf SSH über die Befehlszeile Ihres Computers (beispielsweise Bash-Shell oder [PuTTY](https://www.putty.org/))

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Erstellen des virtuellen Jenkins-Computers auf der Grundlage der Lösungsvorlage

Jenkins unterstützt ein Modell, bei dem der Jenkins-Server die Arbeit an einen oder mehrere Agents delegiert, sodass eine einzelne Jenkins-Installation eine große Anzahl von Projekten hosten oder verschiedene Umgebungen bereitstellen kann, die für Builds oder Tests benötigt werden. Die Schritte in diesem Abschnitt führen Sie durch die Installation und Konfiguration eines Jenkins-Servers in Azure.

1. Öffnen Sie in Ihrem Browser das [Azure Marketplace-Image für Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Wählen Sie **Jetzt herunterladen** aus.

    ![Wählen Sie „Jetzt herunterladen“ aus, um den Installationsprozess für das Jenkins Marketplace-Image zu starten.](./media/install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Nachdem Sie die Preisdetails und die Nutzungsbedingungen überprüft haben, wählen Sie **Weiter** aus.

    ![Preise und Nutzungsbedingungen zum Jenkins Marketplace-Image.](./media/install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Wählen Sie **Erstellen** aus, um den Jenkins-Server im Azure-Portal zu konfigurieren. 

    ![Installieren Sie das Jenkins Marketplace-Image.](./media/install-from-azure-marketplace-image/jenkins-install-create.png)

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Werte an:

   - **Name**: Geben Sie `Jenkins` ein.
   - **Benutzername**: Geben Sie den Benutzernamen ein, der für die Anmeldung bei dem virtuellen Computer verwendet wird, auf dem Jenkins ausgeführt wird. Der Benutzername muss [bestimmte Anforderungen](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) erfüllen.
   - **Authentifizierungstyp**: Wählen Sie **Öffentlicher SSH-Schlüssel** aus.
   - **Öffentlicher SSH-Schlüssel**: Kopieren Sie einen öffentlichen RSA-Schlüssel im einzeiligen Format (beginnend mit `ssh-rsa`) oder im mehrzeiligen PEM-Format, und fügen Sie ihn ein. Sie können die SSH-Schlüssel mithilfe von „ssh-keygen“ unter Linux und macOS oder „PuTTYGen“ unter Windows generieren. Weitere Informationen zu SSH-Schlüsseln und Azure finden Sie im Artikel [Verwenden von SSH-Schlüsseln mit Windows in Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Abonnement**: Wählen Sie das Azure-Abonnement aus, für das Jenkins installiert werden soll.
   - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein, die als logischer Container für die Sammlung von Ressourcen dient, aus denen Ihre Jenkins-Installation besteht.
   - **Standort**: Wählen Sie **USA, Osten** aus.

     ![Geben Sie Authentifizierungs- und Ressourcengruppeninformationen für Jenkins auf der Registerkarte „Grundlagen“ ein.](./media/install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Wählen Sie **OK** aus, um mit der Registerkarte **Zusätzliche Einstellungen** fortzufahren. 

1. Geben Sie auf der Registerkarte **Zusätzliche Einstellungen** die folgenden Werte an:

   - **Größe**: Wählen Sie die geeignete Option für die Größenanpassung für den virtuellen Jenkins-Computer aus.
   - **VM-Datenträgertyp**: Geben Sie entweder HDD (Festplattenlaufwerk) oder SSD (Solid-State Drive) als den Speichertyp an, der für den virtuellen Jenkins-Computer zulässig ist.
   - **Virtuelles Netzwerk**: (Optional) Wählen Sie **Virtuelles Netzwerk** aus, um die Standardeinstellungen zu ändern.
   - **Subnetze**: Wählen Sie **Subnetze** aus, überprüfen Sie die Informationen, und wählen Sie **OK** aus.
   - **Öffentliche IP-Adresse**: Der IP-Adressname ist standardmäßig der Jenkins-Name, den Sie auf der vorherigen Seite mit dem Suffix „-IP“ angegeben haben. Sie können die Option auswählen, um diesen Standardwert zu ändern.
   - **Domänennamenbezeichnung**: Geben Sie den Wert für die vollqualifizierte URL zum virtuellen Jenkins-Computer an.
   - **Jenkins-Releasetyp**: Wählen Sie den gewünschten Releasetyp aus den Optionen aus: `LTS`, `Weekly build` oder `Azure Verified`. Die Optionen `LTS` und `Weekly build` werden in diesem Artikel erläutert: [Jenkins LTS Release Line](https://jenkins.io/download/lts/) (Jenkins LTS-Releases). Die Option `Azure Verified` bezieht sich auf eine [Jenkins LTS-Version](https://jenkins.io/download/lts/), die für die Ausführung auf Azure überprüft wurde. 
   - **JDK Type** (JDK-Typ): Das zu installierende JDK. Standardmäßig werden getestete und zertifizierte Zulu-Builds von OpenJDK verwendet.

     ![Geben Sie die Einstellungen für virtuelle Computer für Jenkins auf der Registerkarte „Einstellungen“ ein.](./media/install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Wählen Sie **OK** aus, um mit der Registerkarte **Integrationseinstellungen** fortzufahren.

1. Geben Sie auf der Registerkarte **Integrationseinstellungen** die folgenden Werte an:

    - **Dienstprinzipal**: Der Dienstprinzipal wird als Anmeldeinformationen zur Authentifizierung bei Azure zu Jenkins hinzugefügt. `Auto` bedeutet, dass der Prinzipal durch MSI (Managed Service Identity, verwaltete Dienstidentität) erstellt wird. `Manual` bedeutet, dass der Prinzipal von Ihnen erstellt werden muss. 
        - **Anwendungs-ID** und **Geheimnis**: Wenn Sie die Option `Manual` für **Dienstprinzipal** auswählen, müssen Sie `Application ID` und `Secret` für Ihren Dienstprinzipal angeben. Wenn Sie [einen Dienstprinzipal erstellen](/cli/azure/create-an-azure-service-principal-azure-cli), beachten Sie, dass die Standardrolle **Mitwirkender** ist. Dies ist ausreichend für die Arbeit mit Azure-Ressourcen.
    - **Cloud-Agents aktivieren**: Geben Sie die Standardvorlage für die Cloud für Agents an. Dabei bezieht sich `ACI` auf die Azure-Containerinstanz und `VM` auf virtuelle Computer. Sie können auch `No` angeben, wenn Sie keinen Cloud-Agent aktivieren möchten.

1. Wählen Sie **OK** aus, um mit der Registerkarte **Zusammenfassung** fortzufahren.

1. Wenn die Registerkarte **Zusammenfassung** angezeigt wird, werden die eingegebenen Informationen überprüft. Wenn die Meldung **Überprüfung erfolgreich** (oben auf der Registerkarte) angezeigt wird, wählen Sie **OK** aus. 

     ![Auf der Registerkarte „Zusammenfassung“ werden die ausgewählten Optionen angezeigt und überprüft.](./media/install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Wenn die Registerkarte **Erstellen** angezeigt wird, wählen Sie **Erstellen** aus, um den virtuellen Computer für Jenkins zu erstellen. Wenn Ihr Server bereit ist, wird im Azure-Portal eine entsprechende Benachrichtigung angezeigt.

     ![Bereitschaftsbenachrichtigung für Jenkins](./media/install-from-azure-marketplace-image/jenkins-install-notification.png)

## <a name="connect-to-jenkins"></a>Herstellen einer Verbindung mit Jenkins

1. Navigieren Sie in Ihrem Webbrowser zu Ihrem virtuellen Computer (beispielsweise `http://jenkins2517454.eastus.cloudapp.azure.com/`). Auf die Jenkins-Konsole kann nicht über eine unsichere HTTP-Verbindung zugegriffen werden. Daher ist auf der Seite beschrieben, wie Sie von Ihrem Computer aus über einen SSH-Tunnel sicher auf die Jenkins-Konsole zugreifen können.

    ![Entsperren von Jenkins](./media/install-solution-template-steps/jenkins-ssh-instructions.png)

1. Richten Sie den Tunnel ein, indem Sie den auf der Seite angegebenen Befehl `ssh` über die Befehlszeile ausführen. Ersetzen Sie dabei `username` durch den Namen des VM-Administratorbenutzers, den Sie beim Einrichten des virtuellen Computers auf der Grundlage der Lösungsvorlage gewählt haben.

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
    ```
    
1. Navigieren Sie nach dem Starten des Tunnels auf Ihrem lokalen Computer zu `http://localhost:8080/`. 

1. Führen Sie über die Befehlszeile den folgenden Befehl aus, während eine SSH-Verbindung mit dem virtuellen Jenkins-Computer besteht, um das anfängliche Kennwort abzurufen.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
    
1. Entsperren Sie das Jenkins-Dashboard zunächst mit diesem anfänglichen Kennwort.

    ![Entsperren von Jenkins](./media/install-solution-template-steps/jenkins-unlock.png)

1. Wählen Sie auf der nächsten Seite **Install suggested plugins** (Vorgeschlagene Plug-Ins installieren) aus, und erstellen Sie einen Jenkins-Administratorbenutzer für den Zugriff auf das Jenkins-Dashboard.

    ![Jenkins ist bereit!](./media/install-solution-template-steps/jenkins-welcome.png)

Der Jenkins-Server ist nun für die Codeerstellung bereit.

## <a name="create-your-first-job"></a>Erstellen Ihres ersten Auftrags

1. Wählen Sie in der Jenkins-Konsole die Option **Create new jobs** (Neue Aufträge erstellen) aus, und nennen Sie den Auftrag **mySampleApp**. Wählen Sie dann **Freestyle project** (Freestyle-Projekt) und anschließend **OK** aus.

    ![Erstellen eines neuen Auftrags](./media/install-solution-template-steps/jenkins-new-job.png) 

1. Aktivieren Sie auf der Registerkarte **Source Code Management** (Quellcodeverwaltung) die Option **Git**, und geben Sie im Feld **Repository URL** (Repository-URL) die folgende URL ein: `https://github.com/spring-guides/gs-spring-boot.git`

    ![Definieren des Git-Repositorys](./media/install-solution-template-steps/jenkins-job-git-configuration.png) 

1. Wählen Sie auf der Registerkarte **Build** die Option **Add build step** (Buildschritt hinzufügen) und anschließend **Invoke Gradle script** (Gradle-Skript aufrufen) aus. Wählen Sie **Use Gradle Wrapper** (Gradle-Wrapper verwenden) aus, und geben Sie dann im Feld **Wrapper location** (Wrapperspeicherort) die Zeichenfolge `complete` und im Feld **Tasks** (Aufgaben) die Zeichenfolge `build` ein.

    ![Verwenden des Gradle-Wrappers für die Erstellung](./media/install-solution-template-steps/jenkins-job-gradle-config.png) 

1. Wählen Sie **Erweitert** aus, und geben Sie dann im Feld **Root Build script** (Stammerstellungsskript) die Zeichenfolge `complete` ein. Wählen Sie **Speichern** aus.

    ![Festlegen erweiterter Einstellungen im Buildschritt des Gradle-Wrappers](./media/install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Erstellen des Codes

1. Wählen Sie **Build Now** (Jetzt erstellen) aus, um den Code zu kompilieren und das Paket für die Beispiel-App zu erstellen. Wählen Sie nach Abschluss des Buildvorgangs den Link **Workspace** (Arbeitsbereich) für das Projekt aus.

    ![Navigieren zum Arbeitsbereich, um die JAR-Datei aus dem Build abzurufen](./media/install-solution-template-steps/jenkins-access-workspace.png) 

1. Vergewissern Sie sich unter `complete/build/libs`, dass die Datei `gs-spring-boot-0.1.0.jar` vorhanden ist, um sicherzugehen, dass der Buildvorgang erfolgreich war. Der Jenkins-Server ist nun für die Erstellung Ihrer eigenen Projekte in Azure bereit.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Problembehandlung bei der Jenkins-Lösungsvorlage

Wenn bei der Jenkins-Lösungsvorlage Fehler auftreten, melden Sie das Problem im [Jenkins-GitHub-Repository](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/developer/jenkins)