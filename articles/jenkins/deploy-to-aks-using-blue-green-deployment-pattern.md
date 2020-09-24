---
title: 'Tutorial: Durchführen der Bereitstellung in Azure Kubernetes Service mit dem Blau/Grün-Bereitstellungsmuster'
description: Hier erfahren Sie, wie Sie Bereitstellungen in Azure Kubernetes Service (AKS) mithilfe von Jenkins und dem Blau/Grün-Bereitstellungsmuster ausführen.
keywords: Jenkins, Azure, DevOps, Kubernetes, K8s, AKS, Blau/Grün-Bereitstellung, Continuous Delivery, CD
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: devx-track-jenkins
ms.openlocfilehash: 6a33227f7a5a0aa3e82e48d821e9f37bdfeadd6e
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832046"
---
# <a name="tutorial-deploy-to-azure-kubernetes-service-aks-using-the-bluegreen-deployment-pattern"></a>Tutorial: Durchführen der Bereitstellung in Azure Kubernetes Service (AKS) mit dem Blau/Grün-Bereitstellungsmuster

Azure Kubernetes Service (AKS) verwaltet Ihre gehostete Kubernetes-Umgebung und ermöglicht so die schnelle und einfache Bereitstellung und Verwaltung von Containeranwendungen. Sie benötigen keine Kenntnisse im Zusammenhang mit Containerorchestrierung. Darüber hinaus übernimmt der Dienst laufende Vorgänge und Wartungsaufgaben, indem er Ressourcen nach Bedarf bereitstellt, aktualisiert und skaliert. Sie müssen Ihre Anwendungen dafür nicht offline schalten. Weitere Informationen zu AKS finden Sie in der [AKS-Dokumentation](/azure/aks/).

Die Blau/Grün-Bereitstellung ist ein Azure DevOps-Muster für Continuous Delivery, bei dem eine vorhandene Version (blau) live bleibt, während eine neue Version (grün) bereitgestellt wird. In der Regel nutzt dieses Muster einen Lastenausgleich, um zunehmendes Datenverkehrsvolumen an die grüne Bereitstellung zu leiten. Wenn die Überwachung einen Incident entdeckt, kann der Datenverkehr an die blaue Bereitstellung umgeleitet werden, die weiterhin ausgeführt wird. Weitere Informationen zu Continuous Delivery finden Sie unter [Was ist Continuous Delivery?](/azure/devops/what-is-continuous-delivery).

Dieses Tutorial umfasst die folgenden Aspekte:

> [!div class="checklist"]
> * Sie erfahren alles Wissenswerte über das Blau/Grün-Bereitstellungsmuster.
> * Sie erstellen einen verwalteten Kubernetes-Cluster.
> * Sie führen ein Beispielskript aus, um einen Kubernetes-Cluster zu konfigurieren.
> * Sie konfigurieren manuell einen Kubernetes-Cluster.
> * Sie erstellen einen Jenkins-Auftrag und führen ihn aus.

## <a name="prerequisites"></a>Voraussetzungen
- [GitHub-Konto](https://github.com): Sie benötigen ein GitHub-Konto, um das Beispielrepository zu klonen.
- [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest): Sie erstellen mit der Azure CLI 2.0 den Kubernetes-Cluster.
- [Chocolatey](https://chocolatey.org): Mit diesem Paket-Manager installieren Sie kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Mit dieser Befehlszeilenschnittstelle führen Sie Befehle für Kubernetes-Cluster aus.
- [jq](https://stedolan.github.io/jq/download/): Dies ist ein schlanker befehlszeilenbasierter JSON-Prozessor.

## <a name="clone-the-sample-app-from-github"></a>Klonen der Beispiel-App von GitHub herunter

Im Microsoft-Repository in GitHub finden Sie eine Beispiel-App, die die Bereitstellung in AKS mithilfe von Jenkins und dem Blau/Grün-Bereitstellungsmuster veranschaulicht. In diesem Abschnitt erstellen Sie einen Fork dieses Repositorys in Ihrer GitHub-Instanz, und klonen die App in Ihrem lokalen System.

1. Navigieren Sie zum GitHub-Repository für die Beispiel-App [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Screenshot von der Beispiel-App im Microsoft-Repository in GitHub](./media/deploy-to-aks-using-blue-green-deployment-pattern/github-sample-msft.png)

1. Erstellen Sie einen Fork des Repositorys, indem Sie oben rechts auf der Seite **Fork** auswählen und den Anweisungen zum Forken des Repositorys in Ihr GitHub-Konto folgen.

    ![Screenshot von der GitHub-Option zum Forken](./media/deploy-to-aks-using-blue-green-deployment-pattern/github-sample-msft-fork.png)

1. Nachdem Sie einen Fork des Repositorys erstellt haben, ändert sich der Kontoname in den Namen Ihres Kontos, und ein Hinweis gibt an, von wo aus der Fork erstellt wurde (Microsoft).

    ![Screenshot vom GitHub-Kontonamen und vom Hinweis](./media/deploy-to-aks-using-blue-green-deployment-pattern/github-sample-msft-forked.png)

1. Wählen Sie **Clone or download**aus.

    ![Screenshot von der GitHub-Option zum Klonen oder Herunterladen eines Repositorys](./media/deploy-to-aks-using-blue-green-deployment-pattern/github-sample-clone.png)

1. Wählen Sie im Fenster **Über HTTPS klonen** das Symbol **Kopieren** aus.

    ![Screenshot von der GitHub-Option zum Kopieren der Klon-URL in die Zwischenablage](./media/deploy-to-aks-using-blue-green-deployment-pattern/github-sample-copy.png)

1. Öffnen Sie ein Terminal- oder Git Bash-Fenster.

1. Wechseln Sie in das Verzeichnis, in dem Sie die lokale Kopie (den Klon) des Repositorys speichern möchten.

1. Klonen Sie mithilfe des Befehls `git clone` die URL, die Sie zuvor kopiert haben.

    ![Screenshot vom Git Bash-Befehl Git Clone](./media/deploy-to-aks-using-blue-green-deployment-pattern/git-clone-command.png)

1. Drücken Sie die EINGABETASTE, um den Klonvorgang zu starten.

    ![Screenshot von Git Bash-Befehlsergebnissen für Git Clone](./media/deploy-to-aks-using-blue-green-deployment-pattern/git-clone-results.png)

1. Wechseln Sie in das neu erstellte Verzeichnis, das den Klon der App-Quelle enthält.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Erstellen und Konfigurieren eines verwalteten Kubernetes-Clusters

In diesem Abschnitt führen Sie die folgenden Schritte aus:

- Sie verwenden die Azure CLI 2.0, um einen verwalteten Kubernetes-Cluster zu erstellen.
- Sie richten einen Cluster manuell oder mithilfe des Setupskripts ein.
- Sie erstellen eine Instanz des Azure Container Registry-Diensts.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Verwenden der Azure CLI 2.0, um einen verwalteten Kubernetes-Cluster zu erstellen
Um einen verwalteten Kubernetes-Cluster mit der [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) zu erstellen, müssen Sie die Azure CLI-Version 2.0.25 oder höher verwenden.

1. Melden Sie sich bei Ihrem Azure-Konto an. Nachdem Sie den folgenden Befehl eingegeben haben, erhalten Sie Anweisungen zum Abschließen der Anmeldung. 
    
    ```azurecli
    az login
    ```

1. Wenn Sie den Befehl `az login` im vorherigen Schritt ausführen, wird eine Liste mit allen Ihren Azure-Abonnements (und den zugehörigen Abonnement-IDs) angezeigt. In diesem Schritt legen Sie das Azure-Standardabonnement fest. Ersetzen Sie den Platzhalter &lt;ID-Ihres-Abonnements> durch die ID des gewünschten Azure-Abonnements. 

    ```azurecli
    az account set -s <your-subscription-id>
    ```

1. Erstellen Sie eine Ressourcengruppe. Ersetzen Sie den Platzhalter &lt;Name-Ihrer-Ressourcengruppe> durch den Namen Ihrer neuen Ressourcengruppe, und ersetzen Sie den Platzhalter &lt;Ihr-Speicherort> durch den entsprechenden Speicherort. Der Befehl `az account list-locations` listet alle Azure-Speicherorte auf. Während der AKS-Vorschau sind nicht alle Speicherorte verfügbar. Wenn Sie einen Speicherort eingeben, der zu diesem Zeitpunkt nicht gültig ist, listet die Fehlermeldung die verfügbaren Speicherorte auf.

    ```azurecli
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Erstellen Sie den Kubernetes-Cluster. Ersetzen Sie &lt;Name-Ihrer-Ressourcengruppe> durch den Namen der Ressourcengruppe, die im vorherigen Schritt erstellt wurde, und ersetzen Sie &lt;Name-Ihres-Kubernetes-Clusters> durch den Namen Ihres neues Clusters. (Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Einrichten des Kubernetes-Clusters

Sie können eine Blau/Grün-Bereitstellung manuell oder mit einem Setupskript (im zuvor geklonten Beispiel enthalten) ausführen. In diesem Abschnitt werden beide Möglichkeiten erläutert.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Einrichten des Kubernetes-Clusters über das Beispielsetupskript
1. Bearbeiten Sie die Datei **deploy/aks/setup/setup.sh**, und ersetzen Sie die folgenden Platzhalter durch die entsprechenden Werte für Ihre Umgebung: 

   - **&lt;Name-Ihrer-Ressourcengruppe**
   - **&lt;Name-Ihres-Kubernetes-Clusters>**
   - **&lt;Ihr-Speicherort>**
   - **&lt;Ihr-DNS-Namenssuffix>**

     ![Screenshot vom „setup.sh“-Skript in Bash mit mehreren hervorgehobenen Platzhaltern](./media/deploy-to-aks-using-blue-green-deployment-pattern/edit-setup-script.png)

1. Führen Sie das Setupskript aus.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Manuelles Einrichten eines Kubernetes-Clusters 
1. Laden Sie die Kubernetes-Konfiguration in Ihren Profilordner herunter.

    ```azurecli
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Ändern Sie in das Verzeichnis in das Verzeichnis **deploy/aks/setup**. 

1. Führen Sie die folgenden **kubectl**-Befehle aus, um die Dienste für den öffentlichen Endpunkt und die beiden Testendpunkte einzurichten.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Aktualisieren Sie den DNS-Namen für den öffentlichen Endpunkt und die Testendpunkte. Beim Erstellen eines Kubernetes-Clusters wird eine [zusätzliche Ressourcengruppe](https://github.com/Azure/AKS/issues/3) mit dem Benennungsmuster **MC_&lt;Name-Ihrer-Ressourcengruppe> _&lt;Name-Ihres-Kubernetes-Clusters>_ &lt;Ihr-Speicherort>** erstellt.

    Ermitteln Sie die öffentlichen IP-Adressen in der Ressourcengruppe.

    ![Screenshot von den öffentlichen IP-Adressen in der Ressourcengruppe](./media/deploy-to-aks-using-blue-green-deployment-pattern/publicip.png)

    Suchen Sie die externe IP-Adresse für jeden Dienst, indem Sie den folgenden Befehl ausführen:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Aktualisieren Sie mit dem folgenden Befehl den DNS-Namen für die entsprechende IP-Adresse:

    ```azurecli
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Wiederholen Sie den Aufruf für `todoapp-test-blue` und `todoapp-test-green`:

    ```azurecli
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Der DNS-Name muss in Ihrem Abonnement eindeutig sein. Sie können `<your-dns-name-suffix>` verwenden, um die Eindeutigkeit zu überprüfen.

### <a name="create-an-instance-of-container-registry"></a>Erstellen einer Container Registry-Instanz

1. Führen Sie den Befehl `az acr create` aus, um eine Container Registry-Instanz zu erstellen. Im nächsten Abschnitt können Sie `login server` als URL der Docker-Registrierung verwenden.

    ```azurecli
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Führen Sie den Befehl `az acr credential` aus, um Ihre Container Registry-Anmeldeinformationen anzuzeigen. Notieren Sie den Benutzername und das Kennwort für die Docker-Registrierung, da diese im nächsten Abschnitt benötigt werden.

    ```azurecli
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Vorbereiten des Jenkins-Servers

In diesem Abschnitt erfahren Sie, wie Sie den Jenkins-Server für die Ausführung eines Builds vorbereiten – dies ist zu Testzwecken ausreichend. Sie sollten jedoch einen [Azure-VM-Agent](https://plugins.jenkins.io/azure-vm-agents) oder den [Azure Container-Agent](https://plugins.jenkins.io/azure-container-agents) verwenden, um einen Agenten zum Ausführen Ihrer Builds in Azure zu starten. Weitere Informationen finden Sie im Jenkins-Artikel [Sicherheitsaspekte beim Erstellen auf Masterknoten](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) (in englischer Sprache).

1. Stellen Sie einen [Jenkins-Master in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.production-jenkins) bereit.

1. Stellen Sie über SSH eine Verbindung mit dem Server her, und installieren Sie die Buildtools auf dem Server, auf dem der Build ausgeführt werden soll.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Installieren Sie Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Überprüfen Sie, ob der Benutzer `jenkins` berechtigt ist, die `docker`-Befehle auszuführen.

1. [Installieren Sie kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Laden Sie jq herunter](https://stedolan.github.io/jq/download/).

1. Installieren Sie jq mithilfe des folgenden Befehls:

   ```bash
   sudo apt-get install jq
   ```
   
1. Installieren Sie die Plug-Ins in Jenkins, indem Sie auf dem Jenkins-Dashboard die folgenden Schritte ausführen:

    1. Wählen Sie **Manage Jenkins > Manage Plugins > Available** (Jenkins verwalten > Plug-Ins verwalten >Verfügbar) aus.
    1. Suchen Sie das Azure Container Service-Plug-In, und installieren Sie es.

1. Fügen Sie Anmeldeinformationen hinzu, um Ressourcen in Azure zu verwalten. Installieren Sie das **Azure-Anmeldeinformationen**-Plug-In (falls noch nicht vorhanden).

1. Fügen Sie die Anmeldeinformationen für Ihren Azure-Dienstprinzipal als Typ **Microsoft Azure Service Principal** hinzu.

1. Fügen Sie Ihren Benutzernamen und Ihr Kennwort für die Azure Docker-Registrierung (aus dem Abschnitt „Erstellen einer Container Registry-Instanz“) als Typ **Benutzername mit Kennwort** hinzu.

## <a name="edit-the-jenkinsfile"></a>Bearbeiten der Jenkinsfile

1. Navigieren Sie in Ihrem eigenen Repository zu `/deploy/aks/`, und öffnen Sie `Jenkinsfile`.

2. Aktualisieren Sie die Datei wie folgt:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Aktualisieren Sie die Container Registry-Anmeldeinformationen-ID:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Erstellen des Auftrags
1. Fügen Sie einen neuen Auftrag mit Typ **Pipeline** hinzu.

1. Wählen Sie **Pipeline** > **Definition** > **Pipelineskript von SCM** aus.

1. Geben Sie die URL des SCM-Repositorys mit &lt;Ihr-geforktes-Repository> an.

1. Geben Sie den Skriptpfad als `deploy/aks/Jenkinsfile` ein.

## <a name="run-the-job"></a>Ausführung des Auftrags.

1. Überprüfen Sie, ob Sie Ihr Projekt erfolgreich in Ihrer lokalen Umgebung ausführen können. Gehen Sie dabei folgendermaßen vor: [Führen Sie das Projekt auf einem lokalen Computer aus](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Führen Sie den Jenkins-Auftrag aus. Wenn Sie den Auftrag zum ersten Mal ausführen, stellt Jenkins die ToDo-App in der blauen Umgebung bereit, die die standardmäßige inaktive Umgebung ist. 

1. Rufen Sie diese URLs auf, um zu überprüfen, ob der Auftrag ausgeführt wurde:
    - Öffentlicher Endpunkt: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Blauer Endpunkt: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Grüner Endpunkt: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Der öffentliche und der blaue Testendpunkt verfügen über das gleiche Update. Der grüne Endpunkt dagegen weist das Tomcat-Standardimage auf. 

Wenn Sie den Build mehrmals ausführen, durchläuft er die blaue und die grüne Bereitstellung. Das heißt, wenn die aktuelle Umgebung blau ist, wird der Auftrag in der grünen Umgebung bereitgestellt und getestet. Wenn die Tests geeignet sind, aktualisiert der Auftrag den öffentlichen Endpunkt der Anwendung, um den Datenverkehr in die grüne Umgebung weiterzuleiten.

## <a name="additional-information"></a>Zusätzliche Informationen

Weitere Informationen zu Bereitstellungen ohne Downtime finden Sie in dieser [Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcen nicht mehr benötigen, können Sie sie löschen.

```azurecli
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Problembehandlung

Wenn bei den Jenkins-Plug-Ins Fehler auftreten, melden Sie das Problem auf der [Jenkins-JIRA-Seite](https://issues.jenkins-ci.org/) für die jeweilige Komponente.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie Bereitstellung in AKS mithilfe von Jenkins und dem Blau/Grün-Bereitstellungsmuster durchführen können. Weitere Informationen zum Azure-Jenkins-Anbieter finden Sie auf der Website „Jenkins in Azure“.

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/jenkins/)