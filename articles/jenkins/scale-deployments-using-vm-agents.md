---
title: 'Tutorial: Skalieren von Jenkins-Bereitstellungen mit Azure-VM-Agents'
description: Es wird beschrieben, wie Sie virtuelle Azure-Computer mit dem Jenkins-Plug-In für Azure-Agents verwenden, um Ihren Jenkins-Pipelines zusätzliche Kapazität hinzuzufügen.
keywords: Jenkins, Azure, DevOps, virtueller Computer, Agents
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: c5ea0c3782414abfda47810ba68ad1092cd7b0d5
ms.sourcegitcommit: f65561589d22b9ba2d69b290daee82eb47b0b20f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88162049"
---
# <a name="tutorial-scale-jenkins-deployments-with-azure-vm-agents"></a>Tutorial: Skalieren von Jenkins-Bereitstellungen mit Azure-VM-Agents

Dieses Tutorial erläutert, wie Sie das [Jenkins-Plug-In für Azure-VM-Agents](https://plugins.jenkins.io/azure-vm-agents) verwenden, um mit in Azure ausgeführten virtuellen Linux-Computern bedarfsgesteuert Kapazität hinzuzufügen.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Installieren des Plug-Ins für Azure-VM-Agents
> * Konfigurieren des Plug-Ins für das Erstellen von Ressourcen in Ihrem Azure-Abonnement
> * Einrichten der Computeressourcen, die für jeden Agent verfügbar sind
> * Einrichten des Betriebssystems und der Tools auf jedem Agent
> * Erstellen eines neuen Jenkins-Freestyle-Auftrags
> * Ausführen des Auftrags in einem Azure-VM-Agent

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="install-azure-vm-agents-plugin"></a>Installieren des Azure-VM-Agents-Plug-In

1. Wählen Sie im Jenkins-Dashboard die Optionen **Manage Jenkins** und dann **Manage Plugins** aus.

1. Wählen Sie die Registerkarte **Available**, und suchen Sie nach **Azure VM Agents**. Aktivieren Sie das Kontrollkästchen neben dem Eintrag für das Plug-In, und wählen Sie **Install without restart** im unteren Bereich des Dashboards.

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurieren des Plug-Ins für Azure-VM-Agents

1. Wählen Sie im Jenkins-Dashboard die Optionen **Manage Jenkins** und dann **Configure System** aus.

1. Scrollen Sie zum Ende der Seite. Suchen Sie den Abschnitt **Cloud** die Dropdownliste **Add new cloud**, und wählen Sie **Microsoft Azure-VM-Agents**.

1. Wählen Sie aus der Dropdownliste **Add** im Abschnitt **Azure Credentials** einen vorhandenen Dienstprinzipal aus. Wenn kein Dienstprinzipal aufgelistet ist, führen Sie folgende Schritte aus, um für Ihr Azure-Konto [einen Dienstprinzipal zu erstellen](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) und zu Ihrer Jenkins-Konfiguration hinzuzufügen:

    a. Wählen Sie die Option **Add** neben **Azure Credentials**, und wählen Sie **Jenkins** aus.
    b. Wählen Sie im Dialogfeld **Add Credentials** aus der Dropdownliste **Kind** die Option **Microsoft Azure Service Principal** aus.
    c. Erstellen Sie über die Azure CLI oder die [Cloud Shell](/azure/cloud-shell/overview) einen Active Directory-Dienstprinzipal.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Geben Sie die Anmeldeinformationen des Dienstprinzipals im Dialogfeld **Add credentials** ein. Wenn Sie Ihre Azure-Abonnement-ID nicht kennen, können Sie sie mithilfe der CLI abfragen:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Der fertig gestellte Dienstprinzipal sollte folgende Werte verwenden: das Feld `id` für die **Subscription ID**, den `appId`-Wert für die **Client ID**, `password` für das **Client Secret** und `tenant` für die **Tenant ID**. Wählen Sie **Add**, um den Dienstprinzipal hinzuzufügen, und konfigurieren Sie dann das Plug-In für die Verwendung der neu erstellten Anmeldeinformationen.

    ![Konfigurieren eines Azure-Dienstprinzipals](./media/scale-deployments-using-vm-agents/new-service-principal.png)

    

1. Behalten Sie unter **Ressourcengruppenname** die Option **Neu erstellen** bei, und geben Sie `myJenkinsAgentGroup` ein.
1. Wählen Sie **Verify configuration**, um eine Verbindung mit Azure herzustellen und die Profileinstellungen zu testen.
1. Wählen Sie **Apply**, um die Plug-In-Konfiguration zu aktualisieren.

## <a name="configure-agent-resources"></a>Konfigurieren von Agent-Ressourcen

Konfigurieren Sie eine Vorlage zum Definieren eines Azure-VM-Agents. Diese Vorlage definiert die Computeressourcen, über die jeder Agent nach der Erstellung verfügt.

1. Wählen Sie die Option **Add** neben **Add Azure Virtual Machine Template** aus.
1. Geben Sie `defaulttemplate` als **Name** ein.
1. Geben Sie `ubuntu` als **Label** ein.
1. Wählen Sie im Kombinationsfeld die gewünschte [Azure-Region](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aus.
1. Wählen Sie aus der Dropdownliste unter **Virtual Machine Size** eine [VM-Größe](/azure/virtual-machines/linux/sizes) aus. Für dieses Tutorial ist eine `Standard_DS1_v2`-Größe für allgemeine Zwecke ausreichend.   
1. Behalten Sie den Wert `60` unter **Retention time** bei. Diese Einstellung legt die Anzahl der Minuten fest, die Jenkins warten kann, bevor die Zuordnung von Agents im Leerlauf aufgehoben wird. Geben Sie 0 an, wenn Sie nicht möchten, dass Agents im Leerlauf automatisch entfernt werden.

   ![Allgemeine VM-Konfiguration](./media/scale-deployments-using-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurieren von Betriebssystem und Tools des Agents

Wählen Sie im Abschnitt **Image Configuration** der Plug-In-Konfiguration die Option **Ubuntu 16.04 LTS** aus. Aktivieren Sie die Kontrollkästchen neben **Install Git (Latest)**, **Install Maven (V3.5.0)** und **Install Docker**, um diese Tools in neu erstellten Agents zu installieren.

![Konfigurieren von Betriebssystem und Tools für den virtuellen Computer](./media/scale-deployments-using-vm-agents/jenkins-os-config.png)

Wählen Sie die Option **Add** neben **Admin Credentials** aus, und wählen Sie dann **Jenkins**. Geben Sie einen Benutzernamen und ein Kennwort für die Anmeldung bei den Agents an. Stellen Sie sicher, dass diese Informationen die [Richtlinien für Benutzernamen und Kennwörter](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) für Verwaltungskonten auf virtuellen Azure-Computern erfüllen.

Wählen Sie **Verify Template**, um die Konfiguration zu überprüfen, und klicken Sie dann auf **Save**, um Ihre Änderungen zu speichern und zum Jenkins-Dashboard zurückzukehren.

## <a name="create-a-job-in-jenkins"></a>Erstellen eines Auftrags in Jenkins

1. Klicken Sie im Jenkins-Dashboard auf **New Item**. 
1. Geben Sie `demoproject1` als Namen ein, wählen Sie **Freestyle project**, und klicken Sie auf **OK**.
1. Wählen Sie auf der Registerkarte **General** die Option **Restrict where this project can be run** aus, und geben Sie `ubuntu` in **Label Expression** ein. In einer Meldung wird bestätigt, dass die Bezeichnung von der im vorherigen Schritt erstellten Cloudkonfiguration unterstützt wird. 
   ![Einrichten eines Auftrags](./media/scale-deployments-using-vm-agents/job-config.png)
1. Wählen Sie auf der Registerkarte **Source Code Management** die Option **Git** aus, und geben Sie im Feld **Repository URL** die folgende URL ein: `https://github.com/spring-projects/spring-petclinic.git`
1. Wählen Sie auf der Registerkarte **Build** die Option **Add build step** und dann **Invoke top-level Maven targets** aus. Geben Sie `package` in das Feld **Goals** ein.
1. Klicken Sie zum Speichern der Auftragsdefinition auf **Save**.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Erstellen des neuen Auftrags in einem Azure-VM-Agent

1. Wechseln Sie zurück zum Jenkins-Dashboard.
1. Wählen Sie den im vorherigen Schritt erstellten Auftrag aus, und klicken Sie auf **Build now**. Ein neuer Build wird in die Warteschlange eingereiht, jedoch erst gestartet, wenn in Ihrem Azure-Abonnement eine Agent-VM erstellt wurde.
1. Nachdem der Build abgeschlossen ist, wechseln Sie zu **Console output** (Konsolenausgabe). Sie sehen, dass der Build remote in einem Azure-Agent ausgeführt wurde.

![Konsolenausgabe](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Problembehandlung beim Jenkins-Plug-In

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Continuous Integration und Continuous Deployment in Azure App Service](deploy-from-github-to-azure-app-service.md)