---
title: 'Tutorial: Skalieren von Jenkins-Bereitstellungen mit in Azure ausgeführtem virtuellem Computer'
description: Hier erfahren Sie, wie Sie Ihren Jenkins-Pipelines mithilfe virtueller Azure-Computer zusätzliche Kapazität hinzufügen.
keywords: Jenkins, Azure, DevOps, virtueller Computer, Agents
ms.topic: tutorial
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-jenkins
ms.openlocfilehash: c498a43d5a8d57a75a5592de279b79a75b8e6360
ms.sourcegitcommit: 347bfa3b6c34579c567d1324efc63c1d6672a75b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109092"
---
# <a name="tutorial-scale-jenkins-deployments-with-vm-running-in-azure"></a>Tutorial: Skalieren von Jenkins-Bereitstellungen mit in Azure ausgeführtem virtuellem Computer

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Linux-Computer in Azure erstellen und ihn Jenkins als Arbeitsknoten hinzufügen.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen des Agent-Computers
> * Hinzufügen des Agents zu Jenkins
> * Erstellen eines neuen Jenkins-Freestyle-Auftrags
> * Ausführen des Auftrags in einem Azure-VM-Agent

## <a name="prerequisites"></a>Voraussetzungen

- **Jenkins-Installation**: [Konfigurieren Sie Jenkins mit der Azure CLI](configure-on-linux-vm.md), falls Sie keinen Zugang zu einer Jenkins-Installation haben.

## <a name="configure-agent-virtual-machine"></a>Konfigurieren des virtuellen Agent-Computers

1. Verwenden Sie [az group create](/cli/azure/group?#az_group_create), um eine Azure-Ressourcengruppe zu erstellen.

    ```azurecli
    az group create --name <resource_group> --location <location>
    ```

1. Verwenden Sie [az vm create](/cli/azure/vm#az_vm_create), um einen virtuellen Computer zu erstellen.

    ```azurecli
    az vm create --resource-group <resource-group> --name <vm_name> --image UbuntuLTS --admin-username azureuser --admin-password "<password>"
    ```

    **Hinweise**:

    - Mit dem folgenden Befehl können Sie auch Ihren SSH-Schlüssel hochladen: `--ssh-key-value <ssh_path>`.

1. Installieren Sie das JDK.  

    #### <a name="linux"></a>[Linux](#tab/linux)
    
    1. Melden Sie sich über ein SSH-Tool bei dem virtuellen Computer an.
    
        ```bash
        ssh username@123.123.123.123
        ```
        
    1. Installieren Sie das JDK mit apt. Für die Installation können auch andere Paket-Manager wie etwa yum oder pacman verwendet werden.
    
        ```bash
        sudo apt-get install -y default-jdk
        ```
    
    1. Führen Sie nach Abschluss der Installation `java -version` aus, um die Java-Umgebung zu überprüfen. Die Ausgabe enthält die Versionsnummern diverser Komponenten des JDK.
    
    #### <a name="windows"></a>[Windows](#tab/windows)
    
    1. Melden Sie sich über ein SSH-Tool oder per Remotedesktopverbindung bei dem virtuellen Computer an.
    
    1. [Laden Sie das für Ihre Umgebung geeignete JDK herunter.](https://www.oracle.com/java/technologies/javase-downloads.html)
    
    1. Installieren des JDK
    
## <a name="configure-jenkins-url"></a>Konfigurieren der Jenkins-URL

Bei Verwendung von JNLP muss die Jenkins-URL konfiguriert werden.

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **System Configuration** (Systemkonfiguration) die Option **Configure System** (System konfigurieren) aus.

1. Vergewissern Sie sich, dass die **Jenkins-URL** auf die HTTP-Adresse Ihrer Jenkins-Installation festgelegt ist: `http://<your_host>.<your_domain>:8080/`.

1. Wählen Sie **Speichern** aus.

## <a name="add-agent-to-jenkins"></a>Hinzufügen des Agents zu Jenkins

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **System Configuration** (Systemkonfiguration) die Option **Manage Nodes and Clouds** (Knoten und Clouds verwalten) aus.

1. Wählen Sie im Menü die Option **New Node** (Neuer Knoten) aus.

1. Geben Sie unter **Node Name** (Knotenname) einen Wert ein.

1. Wählen Sie **Permanent Agent** (Dauerhafter Agent) aus.

1. Klicken Sie auf **OK**.

1. Geben Sie Werte für die folgenden Felder an:

    - **Name**: Geben Sie einen eindeutigen Namen an, der einen Agent innerhalb der neuen Jenkins-Installation identifiziert. Dieser Wert kann sich vom Hostnamen des Agents unterscheiden. Es empfiehlt sich jedoch, für beides den gleichen Wert zu verwenden. Der Namenswert kann beliebige Sonderzeichen aus der folgenden Liste enthalten: `?*/\%!@#$^&|<>[]:;`.

    - **Remote root directory** (Remotestammverzeichnis): Ein Agent muss über ein dediziertes Verzeichnis für Jenkins verfügen. Geben Sie den Pfad zu diesem Verzeichnis auf dem Agent an. Es empfiehlt sich, einen absoluten Pfad wie `/home/azureuser/work` oder `c:\jenkins` zu verwenden. Hierbei sollte es sich um einen lokalen Pfad für den Agent-Computer handeln. Der Pfad muss vom Master aus nicht sichtbar sein. Bei Verwendung eines relativen Pfads wie „./jenkins-agent“ ist der Pfad relativ zu dem Arbeitsverzeichnis, das durch die Startmethode bereitgestellt wird.

    - **Bezeichnungen:** Bezeichnungen werden verwendet, um semantisch verwandte Agents in einer logischen Gruppe zusammenzufassen. Beispielsweise können Sie die Bezeichnung `UBUNTU` für alle Ihre Agents definieren, auf denen die Ubuntu-Distribution von Linux ausgeführt wird.

    - **Launch method** (Startmethode): Für den Jenkins-Remoteknoten stehen zwei Startoptionen zur Verfügung: **Launch agents via SSH** (Agents per SSH starten) und **Launch agent via execution of command on the master** (Agent per Befehlsausführung auf dem Master starten):

        - **Launch agents via SSH** (Agents per SSH starten): Geben Sie Werte für die folgenden Felder an:

            - **Host**: Öffentliche IP-Adresse oder Domänenname des virtuellen Computers. Beispiel: `123.123.123.123` oder `example.com`

            - **Anmeldeinformationen**: Wählen Sie Anmeldeinformationen aus, die für die Anmeldung beim Remotehost verwendet werden sollen. Sie können auch die Schaltfläche **Add** (Hinzufügen) auswählen, neue Anmeldeinformationen definieren und dann die neu erstellten Anmeldeinformationen auswählen.

            - **Host Key Verification Strategy** (Überprüfungsstrategie für den Hostschlüssel): Steuert, wie Jenkins den SSH-Schlüssel überprüft, der vom Remotehost bei der Anmeldung übermittelt wird.

            ![Exemplarische Knotenkonfiguration mit der Startmethode „Launch agents via SSH“ (Agents per SSH starten)](./media/scale-deployments-using-vm-agents/ssh2.png)

        - **Launch agent via execution of command on the master** (Agent per Befehlsausführung auf dem Master starten):

            - Laden Sie unter `https://<your_jenkins_host_name>/jnlpJars/agent.jar` die Datei `agent.jar` herunter. Beispielsweise `https://localhost:8443/jnlpJars/agent.jar`.

            - Laden Sie `agent.jar` auf Ihren virtuellen Computer hoch.

            - Starten Sie Jenkins mithilfe des Befehls `ssh <node_host> java -jar <remote_agentjar_path>`. Beispielsweise `ssh azureuser@99.99.999.9 java -jar /home/azureuser/agent.jar`.

            ![Exemplarische Knotenkonfiguration mit der Startmethode „Launch agent via execution of command on the master“ (Agent per Befehlsausführung auf dem Master starten)](./media/scale-deployments-using-vm-agents/config.png)

1. Wählen Sie **Speichern** aus.

Nach Abschluss der Konfiguration wird der virtuelle Computer von Jenkins als neuer Arbeitsknoten hinzugefügt.

![Beispiel für einen virtuellen Computer als neuer Arbeitsknoten](./media/scale-deployments-using-vm-agents/commandstart.png)

## <a name="create-a-job-in-jenkins"></a>Erstellen eines Auftrags in Jenkins

1. Wählen Sie im Menü die Option **New Item** (Neues Element) aus.

1. Geben Sie `demoproject1` als Namen ein.

1. Klicken Sie auf **Freestyle Project** (Freestyle-Projekt).

1. Klicken Sie auf **OK**.

1. Wählen Sie auf der Registerkarte **General** die Option **Restrict where this project can be run** aus, und geben Sie `ubuntu` in **Label Expression** ein. In einer Meldung wird bestätigt, dass die Bezeichnung von der im vorherigen Schritt erstellten Cloudkonfiguration unterstützt wird.

   ![Einrichten eines neuen Jenkins-Auftrags](./media/scale-deployments-using-vm-agents/job-config.png)

1. Wählen Sie auf der Registerkarte **Source Code Management** die Option **Git** aus, und geben Sie im Feld **Repository URL** die folgende URL ein: `https://github.com/spring-projects/spring-petclinic.git`

1. Wählen Sie auf der Registerkarte **Build** die Option **Add build step** und dann **Invoke top-level Maven targets** aus. Geben Sie `package` in das Feld **Goals** ein.

1. Wählen Sie **Speichern** aus.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Erstellen des neuen Auftrags in einem Azure-VM-Agent

1. Wählen Sie den im vorherigen Schritt erstellten Auftrag aus.

1. Wählen Sie **Build now** aus. Daraufhin wird ein neuer Buildvorgang in die Warteschlange eingereiht. Er wird jedoch erst gestartet, wenn in Ihrem Azure-Abonnement ein virtueller Agent-Computer erstellt wird.

1. Nachdem der Build abgeschlossen ist, wechseln Sie zu **Console output** (Konsolenausgabe). Sie sehen, dass der Build remote in einem Azure-Agent ausgeführt wurde.

    ![Konsolenausgabe](./media/scale-deployments-using-vm-agents/console-output.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Continuous Integration und Continuous Deployment in Azure App Service](deploy-from-github-to-azure-app-service.md)