---
title: 'Tutorial: Verwenden von Azure Container Instances als Jenkins-Build-Agent'
description: Hier erfahren Sie, wie Sie einen Jenkins-Server zum Ausführen von Buildaufträgen in Azure Container Instances konfigurieren.
keywords: Jenkins, Azure, DevOps, Container Instances, Build-Agent
ms.topic: article
ms.date: 01/08/2021
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: cc0e38dbad8056f8c511f2c76713891d842dddb8
ms.sourcegitcommit: 737d95fe31e9db55c2d42a93f194a3f3e4bd3c7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622308"
---
# <a name="tutorial-use-azure-container-instances-as-a-jenkins-build-agent"></a>Tutorial: Verwenden von Azure Container Instances als Jenkins-Build-Agent

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

Mit Azure Container Instances (ACI) steht Ihnen eine bedarfsgesteuerte, burstfähige und isolierte Umgebung zum Ausführen von containerbasierten Workloads zur Verfügung. Aufgrund dieser Attribute ist ACI eine hervorragende Plattform für die Ausführung von Jenkins-Buildaufträgen in großem Umfang. In diesem Artikel erfahren Sie, wie Sie eine ACI-Instanz bereitstellen und als dauerhaften Build-Agent für einen Jenkins-Controller hinzufügen.

Weitere Informationen zu Azure Container Instances finden Sie unter [Azure Container Instances](/azure/container-instances/container-instances-overview).

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.
- **Jenkins-Server**: Falls Sie keinen Jenkins-Server installiert haben, [erstellen Sie einen Jenkins-Servers in Azure](./configure-on-linux-vm.md).

## <a name="prepare-the-jenkins-controller"></a>Vorbereiten des Jenkins-Controllers

1. Navigieren Sie zu Ihrem Jenkins-Portal.

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **System Configuration** (Systemkonfiguration) die Option **Configure System** (System konfigurieren) aus.

1. Vergewissern Sie sich, dass die **Jenkins-URL** auf die HTTP-Adresse Ihrer Jenkins-Installation festgelegt ist: `http://<your_host>.<your_domain>:8080/`.

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **Security** (Sicherheit) die Option **Configure Global Security** (Globale Sicherheit konfigurieren) aus.

1. Geben Sie unter **Agents** den Port **Fixed** (Fest) an, und geben Sie die entsprechende Portnummer für Ihre Umgebung ein.

    Konfigurationsbeispiel:  ![Konfigurieren des TCP-Ports](./media/azure-container-instances-as-jenkins-build-agent/agent-port.png)

1. Wählen Sie **Speichern** aus.

## <a name="create-jenkins-work-agent"></a>Erstellen eines Jenkins-Arbeits-Agents

1. Navigieren Sie zu Ihrem Jenkins-Portal.

1. Wählen Sie im Menü die Option **Manage Jenkins** (Jenkins verwalten) aus.

1. Wählen Sie unter **System Configuration** (Systemkonfiguration) die Option **Manage Nodes and Clouds** (Knoten und Clouds verwalten) aus.

1. Wählen Sie im Menü die Option **New Node** (Neuer Knoten) aus.

1. Geben Sie unter **Node Name** (Knotenname) einen Wert ein.

1. Wählen Sie **Permanent Agent** (Dauerhafter Agent) aus.

1. Klicken Sie auf **OK**.

1. Geben Sie einen Wert für **Remote root directory** (Remotestammverzeichnis)ein. Beispiel: `/home/jenkins/work`

1. Hinzufügen einer <abbr title="Bezeichnungen werden verwendet, um mehrere Agents in einer logischen Gruppe zusammenzufassen. Ein Beispiel für eine Bezeichnung wäre `linux` zum Gruppieren Ihrer Linux-Agents.">**Label**</abbr> mit dem Wert `linux`.

1. Legen Sie **Launch method** (Startmethode) auf **Launch agent by connecting to the master** (Agent durch Verbindungsherstellung mit dem Master starten) fest.

1. Vergewissern Sie sich, dass alle erforderlichen Felder ausgefüllt sind:

    ![Beispielkonfiguration für den Jenkins-Agent](./media/azure-container-instances-as-jenkins-build-agent/agent-config.png)

1. Wählen Sie **Speichern** aus.

1. Auf der Seite mit dem Agent-Status sollten das Jenkins-Geheimnis (`JENKINS_SECRET`) und der Agent-Name (`AGENT_NAME`) angezeigt werden. Der folgende Screenshot zeigt, wo Sie diese Werte finden. Beide Werte werden beim Erstellen der Azure Container Instances-Instanz benötigt.

    ![Das Geheimnis für den Build-Agent wird nach dessen erfolgreicher Erstellung angezeigt.](./media/azure-container-instances-as-jenkins-build-agent/jenkins-secret.png)

## <a name="create-azure-container-instance-with-cli"></a>Erstellen der Azure Container Instances-Instanz mithilfe der CLI

1. Verwenden Sie [az group create](/cli/azure/group?#az_group_create), um eine Azure-Ressourcengruppe zu erstellen.

      ```azurecli
      az group create --name my-resourcegroup --location westus
      ```

1. Verwenden Sie [az container create](/cli/azure/container#az_container_create), um eine Azure Container Instances-Instanz zu erstellen. Ersetzen Sie die Platzhalter durch die Werte, die Sie beim Erstellen des Arbeits-Agents erhalten haben.

    ```azurecli
    az container create \
      --name my-dock \
      --resource-group my-resourcegroup \
      --ip-address Public --image jenkins/inbound-agent:latest \
      --os-type linux \
      --ports 80 \
      --command-line "jenkins-agent -url http://jenkinsserver:port <JENKINS_SECRET> <AGENT_NAME>"
    ```

    Ersetzen Sie `http://jenkinsserver:port`, `<JENKINS_SECRET>` und `<AGENT_NAME>` durch die Informationen für Ihre Jenkins-Controller und -Agents. Nach dem Start des Containers wird von diesem automatisch eine Verbindung mit dem Jenkins-Controllerserver hergestellt.

1. Kehren Sie zurück zum Jenkins-Dashboard, und überprüfen Sie den Agent-Status.

    ![Erfolgreich gestarteter Agent](./media/azure-container-instances-as-jenkins-build-agent/agent-start.png)

    > [!NOTE]
    > Jenkins-Agents stellen über den Port `5000` eine Verbindung mit dem Controller her. Stellen Sie sicher, dass an diesem Port eingehender Datenverkehr für den Jenkins-Controller zugelassen wird.

## <a name="create-a-build-job"></a>Erstellen eines Buildauftrags

Nun wird ein Jenkins-Buildauftrag erstellt, um Jenkins-Builds auf einer Azure-Containerinstanz zu veranschaulichen.

1. Klicken Sie auf **Neues Element**, geben Sie dem Buildprojekt einen Namen wie **aci-demo**, wählen Sie **Freestyle project** (Freestyleprojekt) aus, und klicken Sie dann auf **OK**.

   ![Das Feld für den Namen des Buildauftrags und die Liste der Projekttypen](./media/azure-container-instances-as-jenkins-build-agent/jenkins-new-job.png)

1. Vergewissern Sie sich, dass unter **General** (Allgemein) die Option **Restrict where this project can be run** (Ausführungsort dieses Projekts beschränken) aktiviert ist. Geben Sie als Bezeichnungsausdruck **linux** ein. Mit dieser Konfiguration wird sichergestellt, dass dieser Buildauftrag in der ACI-Cloud ausgeführt wird.

   ![Die Registerkarte „Allgemein“ mit Konfigurationsdetails](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-01.png)

1. Klicken Sie unter **Build** auf **Buildschritt hinzufügen**, und klicken Sie auf **Execute Shell** (Shell ausführen). Geben Sie den Befehl `echo "aci-demo"` ein.

   ![Die Registerkarte „Build“ mit der Auswahl für den Buildschritt](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-02.png)

1. Wählen Sie **Speichern** aus.

## <a name="run-the-build-job"></a>Ausführen des Buildauftrags

Starten Sie einen Build manuell, um den Buildauftrag zu testen und sich mit Azure Container Instances vertraut zu machen.

1. Klicken Sie auf **Build Now** (Jetzt erstellen), um einen Buildauftrag zu starten. Nach dem Starten des Auftrags wird ein Status ähnlich der folgenden Abbildung angezeigt:

   ![Informationen mit dem Auftragsstatus „Buildverlauf“](./media/azure-container-instances-as-jenkins-build-agent/jenkins-job-status.png)

1. Klicken Sie unter **Buildverlauf** auf **#1**.

    ![Konsolenausgabe: Anzeigen der Buildausgabe der Konsole unter „Buildverlauf“](./media/azure-container-instances-as-jenkins-build-agent/build-history.png)

1. Wählen Sie **Konsolenausgabe** aus, um die Ausgabe der Builds anzuzeigen.

    ![Konsolenausgabe: Anzeigen der Buildausgabe über die Konsole](./media/azure-container-instances-as-jenkins-build-agent/build-console-output.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Continuous Integration und Continuous Deployment in Azure App Service](/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service)