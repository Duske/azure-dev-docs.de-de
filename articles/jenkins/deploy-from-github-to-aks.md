---
title: 'Tutorial: Bereitstellen über GitHub in Azure Kubernetes Service mit Jenkins'
description: Es wird beschrieben, wie Sie Jenkins für Continuous Integration (CI) über GitHub und Continuous Deployment (CD) in Azure Kubernetes Service (AKS) konfigurieren.
keywords: Jenkins, Azure, DevOps, AKS, Azure Kubernetes Service, GitHub
ms.topic: article
ms.date: 02/05/2021
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: d6a45e227436970fa34505ccfbf28dc6e8c2a5d8
ms.sourcegitcommit: 5f4a041aede2a0b7e2cf479185f62885209eaf9f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517174"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-using-jenkins"></a>Tutorial: Bereitstellen über GitHub in Azure Kubernetes Service mit Jenkins

[!INCLUDE [jenkins-integration-with-azure.md](includes/jenkins-integration-with-azure.md)]

In diesem Tutorial werden Continuous Integration (CI) und Continuous Deployment (CD) in Jenkins eingerichtet, um eine Beispiel-App aus GitHub in einem [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes)-Cluster bereitzustellen.

Dieses Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Bereitstellen einer Azure-Beispielabstimmungs-App in einem AKS-Cluster.
> * Erstellen eines einfachen Jenkins-Projekts.
> * Einrichten von Jenkins-Anmeldeinformationen für die Interaktion mit ACR.
> * Erstellen eines Jenkins-Buildauftrags und GitHub-Webhooks für automatisierte Builds.
> * Testen der CI/CD-Pipeline zum Aktualisieren einer Anwendung in AKS basierend auf GitHub-Codecommits.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Grundkenntnisse zu Kubernetes, Git, CI/CD und Containerimages.

- Einen [AKS-Cluster](/azure/aks/kubernetes-walkthrough) und `kubectl`, konfiguriert mit den [AKS-Clusteranmeldeinformationen](/cli/azure/aks#az-aks-get-credentials).

- Eine [ACR-Registrierung (Azure Container Registry)](/azure/container-registry/container-registry-get-started-azure-cli), den Namen des ACR-Anmeldeservers und den für die [Authentifizierung mit der ACR-Registrierung](/azure/aks/cluster-container-registry-integration) konfigurierten AKS-Cluster.

- Eine Bereitstellung eines [Jenkins-Controllers](https://docs.microsoft.com/azure/developer/jenkins/configure-on-linux-vm) auf einem virtuellen Azure-Computer.

- Version 2.0.46 oder höher der Azure-Befehlszeilenschnittstelle installiert und konfiguriert. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

- Eine [Docker-Installation](https://docs.docker.com/install/) auf Ihrem Entwicklungssystem.

- Ein GitHub-Konto, ein [persönliches Zugriffstoken für GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) und eine Installation des Git-Clients auf Ihrem Entwicklungssystem.

- Wenn Sie eine eigene Jenkins-Instanz bereitstellen, anstatt dieses Beispielskript zu verwenden, muss für Ihre Jenkins-Instanz [Docker installiert und konfiguriert](https://docs.docker.com/install/) und [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) vorhanden sein.

## <a name="prepare-your-app"></a>Vorbereiten Ihrer App

In diesem Artikel verwenden Sie als Beispiel eine Azure-Abstimmungsanwendung, die eine Weboberfläche und eine Redis-Instanz für die vorübergehende Speicherung der Daten enthält.

Bevor Sie Jenkins und AKS für automatisierte Bereitstellungen integrieren, müssen Sie zuerst manuell die Azure-Abstimmungsanwendung in Ihrem AKS-Cluster vorbereiten und bereitstellen. Bei dieser manuellen Bereitstellung können Sie die Anwendung in Aktion sehen.

> [!NOTE]
> Die Azure-Beispielabstimmungsanwendung verwendet einen Linux-Pod, der zur Ausführung auf einem Linux-Knoten geplant wird. Der in diesem Artikel beschriebene Flow funktioniert auch für einen Windows Server-Pod, der für einen Windows Server-Knoten geplant ist.

Forken Sie das folgende GitHub-Repository für die Beispielanwendung: [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Wählen Sie die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke aus, um das Repository in Ihr GitHub-Konto zu verzweigen.

Klonen Sie den Fork in Ihrem Entwicklungssystem. Stellen Sie sicher, dass Sie beim Klonen dieses Repositorys die URL Ihres Forks verwenden:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Wechseln Sie zum Verzeichnis Ihres geklonten Forks:

```console
cd azure-voting-app-redis
```

Um die für die Beispielanwendung erforderlichen Containerimages zu erstellen, verwenden Sie die Datei *docker-compose.yaml* mit `docker-compose`:

```console
docker-compose up -d
```

Die erforderlichen Basisimages werden gepullt, und die Anwendungscontainer werden erstellt. Das erstellte Image kann anschließend mithilfe des Befehls [docker-images](https://docs.docker.com/engine/reference/commandline/images/) angezeigt werden. Drei Images wurden heruntergeladen oder erstellt. Das `azure-vote-front`-Image enthält die Anwendung und verwendet das `nginx-flask`-Image als Grundlage. Das `redis`-Image wird zum Starten einer Redis-Instanz verwendet:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Melden Sie sich bei Ihrer Azure-Containerinstanz an.

```azurecli
az login -n <acrLoginServer>
```

Ersetzen Sie `<acrLoginServer>` durch Ihren ACR-Anmeldeserver.

Verwenden Sie den Befehl [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), um das Image mit dem ACR-Anmeldeservernamen und der Versionsnummer `v1` zu versehen. Verwenden Sie Ihren eigenen `<acrLoginServer>`-Namen aus dem vorherigen Schritt:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Pushen Sie das Image *azure-vote-front* schließlich in Ihre ACR-Registrierung. Ersetzen Sie erneut `<acrLoginServer>` durch den Anmeldeservernamen Ihrer eigenen ACR, z.B. `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Bereitstellen der Beispielanwendung in AKS

Zum Bereitstellen der Beispielanwendung in Ihrem AKS-Cluster können Sie die Kubernetes-Manifestdatei im Stammverzeichnis des Repositorys der Azure-Abstimmungsanwendung verwenden. Öffnen Sie die Manifestdatei `azure-vote-all-in-one-redis.yaml` in einem Editor wie `vi`. Ersetzen Sie `microsoft` durch Ihren ACR-Anmeldeservernamen. Dieser Wert befindet sich in Zeile **60** der Manifestdatei:

```yaml
containers:
- name: azure-vote-front
  image: azuredocs/azure-vote-front
```

Stellen Sie als Nächstes die Anwendung mithilfe des Befehls [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) in Ihrem AKS-Cluster bereit:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Es wird ein Kubernetes-Lastenausgleichsdienst erstellt, um die Anwendung im Internet verfügbar zu machen. Dieser Vorgang kann einige Minuten dauern. Verwenden Sie den Befehl [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) mit dem Argument `--watch`, um den Fortschritt der Bereitstellung des Lastenausgleichs zu überwachen. Sobald die *externe IP-Adresse* nicht mehr *ausstehend* ist, sondern eine *IP-Adresse* angezeigt wird, verwenden Sie `Control + C`, um die kubectl-Überwachung zu beenden.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Öffnen Sie die externe IP-Adresse Ihres Diensts in einem Webbrowser, um die Anwendung in Aktion zu sehen. Ihre Azure-Abstimmungsanwendung wird wie im folgenden Beispiel angezeigt:

![Beispiel für eine in AKS ausgeführte Azure-Abstimmungsanwendung](media/deploy-from-github-to-aks/azure-vote.png)

## <a name="configure-jenkins-controller"></a>Konfigurieren des Jenkins-Controllers

Nehmen Sie die folgenden Änderungen vor, um AKS-Bereitstellungen über den Jenkins-Controller zu ermöglichen:

Öffnen Sie Port `80` in eingehender Richtung.

```azurecli
az vm open-port \
--resource-group <Resource_Group_name> \
--name <Jenkins_Controller_VM>  \
--port 80 --priority 1020
```

Ersetzen Sie `<Resource_Group_name>` und `<Jenkins_Controller_VM>` durch die entsprechenden Werte.

Herstellen einer SSH-Verbindung mit dem Jenkins-Controller

```azurecli
ssh azureuser@<PublicIPAddres>
```

Ersetzen Sie `<PublicIPAddress>` durch die IP-Adresse des Jenkins-Controllers.

### <a name="install--log-into-azcli"></a>Installieren der Azure CLI und Durchführen der Anmeldung

```azurecli
curl -L https://aka.ms/InstallAzureCli | bash
``````

```azurecli
az login
```

   > [!NOTE]
   > Befolgen Sie diese [Anleitung](https://docs.microsoft.com/cli/azure/install-azure-cli), um die Azure CLI manuell zu installieren.

### <a name="install-docker"></a>Installieren von Docker

```bash
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y;
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -;
sudo apt-key fingerprint 0EBFCD88;
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable";
sudo apt-get update;
sudo apt-get install docker-ce -y;
```

### <a name="install-kubectl-and-connect-to-aks"></a>Installieren von kubectl und Herstellen einer Verbindung mit AKS

```azurecli
sudo az aks install-cli
sudo az aks get-credentials --resource-group <Resource_Group> --name <AKS_Name>
```

Ersetzen Sie `<Resource_Group>` und `<AKS_Name>` durch die entsprechenden Werte.

### <a name="configure-access"></a>Konfigurieren des Zugriffs

```bash
sudo usermod -aG docker jenkins;
sudo usermod -aG docker azureuser;
sudo touch /var/lib/jenkins/jenkins.install.InstallUtil.lastExecVersion;
sudo service jenkins restart;
sudo cp ~/.kube/config /var/lib/jenkins/.kube/
sudo chmod 777 /var/lib/jenkins/
sudo chmod 777 /var/lib/jenkins/config
```

## <a name="create-a-jenkins-environment-variable"></a>Erstellen einer Jenkins-Umgebungsvariable

Eine Jenkins-Umgebungsvariable wird verwendet, um den Namen des ACR-Anmeldeservers zu speichern. Auf diese Variable wird während der Ausführung eines Jenkins-Buildauftrags verwiesen. Erstellen Sie diese Umgebungsvariable anhand der folgenden Schritte:

- Wählen Sie auf der linken Seite des Jenkins-Portals **Manage Jenkins (Jenkins verwalten)**  > **Configure System (System konfigurieren)** aus.
- Wählen Sie unter **Global Properties** (Globale Eigenschaften) die Option **Environment variables** (Umgebungsvariablen) aus. Fügen Sie eine Variable mit dem Namen `ACR_LOGINSERVER` und Ihrem ACR-Anmeldeserver als Wert hinzu.

    ![Jenkins-Umgebungsvariablen](media/deploy-from-github-to-aks/env-variables.png)

- Wählen Sie nach Abschluss des Vorgangs unten auf der Seite die Option **Speichern** aus.

## <a name="create-a-jenkins-credential-for-acr"></a>Erstellen von Jenkins-Anmeldeinformationen für ACR

Während des CI/CD-Vorgangs erstellt Jenkins neue Containerimages basierend auf Anwendungsupdates. Diese müssen dann in die ACR-Registrierung *gepusht* werden.

Um für Jenkins das Pushen von aktualisierten Containerimages an ACR zu ermöglichen, müssen Sie Anmeldeinformationen für ACR angeben. 

Konfigurieren Sie in Ihrer ACR-Registrierung für die Trennung von Rollen und Berechtigungen einen Dienstprinzipal für Jenkins, der über Berechtigungen vom Typ *Mitwirkender* verfügt.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Erstellen eines Dienstprinzipals für die Verwendung von ACR durch Jenkins

Erstellen Sie als Erstes mithilfe des Befehls [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) einen Dienstprinzipal:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```bash
{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Notieren Sie sich die App-ID (*appId*) und das Kennwort (*password*). Diese Werte werden in den folgenden Schritten zum Konfigurieren der Anmeldeinformationsressource in Jenkins verwendet.

Rufen Sie mithilfe des Befehls [az acr show](/cli/azure/acr#az-acr-show) die Ressourcen-ID Ihrer ACR-Registrierung ab, und speichern Sie sie als Variable.

```azurecli
ACR_ID=$(az acr show --resource-group <Resource_Group> --name <acrLoginServer> --query "id" --output tsv)
```

Ersetzen Sie `<Resource_Group>` und `<acrLoginServer>` durch die entsprechenden Werte.

Erstellen Sie eine Rollenzuweisung, um für den Dienstprinzipal Berechtigungen vom Typ *Mitwirkender* für die ACR-Registrierung zuzuweisen.

```azurecli
az role assignment create --assignee <appID> --role Contributor --scope $ACR_ID
```

Ersetzen Sie `<appId>` durch den Wert aus der Ausgabe des vorherigen Befehls, mit dem der Dienstprinzipal erstellt wurde.

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Erstellen einer Anmeldeinformationsressource in Jenkins für den ACR-Dienstprinzipal

Nachdem die Rollenzuweisung in Azure erstellt wurde, speichern Sie nun Ihre ACR-Anmeldeinformationen in einem Jenkins-Anmeldeinformationsobjekt. Auf diese Anmeldeinformationen wird während eines Jenkins-Erstellungsauftrags verwiesen.

Wählen Sie im Jenkins-Portal auf der linken Seite **Manage Jenkins** > **Manage Credentials** > **Jenkins Store** > **Global credentials (unrestricted)**  > **Add Credentials** (Jenkins verwalten > Anmeldeinformationen verwalten > Jenkins Store > Globale Anmeldeinformationen (uneingeschränkt) > Anmeldeinformationen hinzufügen) aus.

Stellen Sie sicher, dass als Typ für die Anmeldeinformationen **Username with password** ausgewählt ist, und geben Sie Folgendes ein:

- **Username** (Benutzername): *appId* des für die Authentifizierung bei Ihrer ACR-Registrierung erstellten Dienstprinzipals.
- **Password** (Kennwort): *password* des für die Authentifizierung bei Ihrer ACR-Registrierung erstellten Dienstprinzipals.
- **ID:** Bezeichner für die Anmeldeinformationen, z.B. *acr-credentials*

Nach Abschluss des Vorgangs ähnelt das Formular für die Anmeldeinformationen etwa dem folgenden Beispiel:

![Erstellen eines Jenkins-Anmeldeinformationsobjekts mit den Dienstprinzipalinformationen](media/deploy-from-github-to-aks/acr-credentials.png)

Wählen Sie **OK** aus, und wechseln Sie zurück zum Jenkins-Portal.

## <a name="create-a-jenkins-project"></a>Erstellen eines Jenkins-Projekts

Wählen Sie auf der Startseite des im Jenkins-Portal links **New item** (Neues Element) aus:

1. Geben Sie als Auftragsnamen *azure-vote* ein. Wählen Sie **Freestyle Project** und anschließend **OK** aus.
1. Wählen Sie im Abschnitt **Allgemein** die Option **GitHub-Projekt** aus, und geben Sie die URL Ihres geforkten Repositorys ein, z. B. *https:\//github.com/\<your-github-account\>/azure-voting-app-redis*.
1. Wählen Sie im Abschnitt **Quellcodeverwaltung** die Option **Git** aus, und geben Sie die `.git`-URL Ihres geforkten Repositorys ein, z. B. *https:\//github.com/\<your-github-account\>/azure-voting-app-redis.git*.

1. Wählen Sie im Bereich **Build Triggers** (Buildtrigger) die Option **GitHub hook trigger for GITscm polling** (GitHub-Hooktrigger für GITscm-Abruf) aus.
1. Wählen Sie unter **Build Environment** (Buildumgebung) die Option **Use secret texts or files** (Geheime Texte oder Dateien verwenden) aus.
1. Wählen Sie unter **Bindings** (Bindungen) die Option **Add (Hinzufügen)**  > **Username and password (separated) (Benutzername und Kennwort (getrennt))** aus.
   - Geben Sie `ACR_ID` unter **Username Variable (Variable für Benutzernamen)** und `ACR_PASSWORD` unter **Password Variable (Variable für Kennwort)** ein.

     ![Jenkins-Bindungen](media/deploy-from-github-to-aks/bindings.png)

1. Fügen Sie einen **Build Step** (Buildschritt) vom Typ **Execute shell** (Shell ausführen) hinzu, und verwenden Sie den folgenden Text. Mit diesem Skript wird ein neues Containerimage erstellt und per Pushvorgang in Ihre ACR übertragen.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Fügen Sie einen weiteren **Build Step** vom Typ **Execute shell** hinzu, und verwenden Sie den folgenden Text. Dieses Skript aktualisiert die Anwendungsbereitstellung in AKS mit dem neuen Containerimage aus ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME
    ```

1. Klicken Sie nach Abschluss des Vorgangs auf **Save**.

## <a name="test-the-jenkins-build"></a>Testen des Jenkins-Builds

Bevor Sie den Auftrag basierend auf GitHub-Commits automatisieren, sollten Sie den Jenkins-Build zunächst manuell testen.

Bei diesem Buildvorgang wird überprüft, ob der Auftrag richtig konfiguriert wurde. Es wird überprüft, ob die richtige Kubernetes-Authentifizierungsdatei vorhanden ist und ob die Authentifizierung für ACR funktioniert.

Wählen Sie im Menü auf der linken Seite des Projekts **Build Now** (Jetzt erstellen) aus.

![Jenkins-Testbuild](media/deploy-from-github-to-aks/test-build.png)

Der erste Buildvorgang dauert einen Moment, während die Docker-Imageebenen auf den Jenkins-Server gepullt werden.

Bei den Buildvorgängen werden die folgenden Aufgaben durchgeführt:

1. Klonen des GitHub-Repositorys
1. Erstellen eines neuen Containerimages
1. Pushen des Containerimages in die ACR-Registrierung
1. Aktualisieren des für die AKS-Bereitstellung verwendeten Images

Da am Anwendungscode keine Änderungen vorgenommen wurden, bleibt die Webbenutzeroberfläche unverändert.

Wählen Sie nach Abschluss des Buildauftrags im Buildverlauf die Option für **Build 1** aus. Wählen Sie **Console Output** (Konsolenausgabe) aus, und zeigen Sie die Ausgabe des Buildvorgangs an. In der letzten Zeile sollte die erfolgreiche Durchführung des Builds angezeigt werden.

## <a name="create-a-github-webhook"></a>Erstellen eines GitHub-Webhooks

Nachdem die manuelle Erstellung abgeschlossen ist, integrieren Sie nun GitHub in den Jenkins-Build. Verwenden Sie einen Webhook, um den Jenkins-Buildauftrag jeweils auszuführen, wenn Code für GitHub committet wird.

Führen Sie die folgenden Schritte aus, um den GitHub-Webhook zu erstellen:

1. Navigieren Sie in einem Webbrowser zu Ihrem geforkten GitHub-Repository.
1. Wählen Sie **Einstellungen** und anschließend **Webhooks** auf der linken Seite aus.
1. Wählen Sie **Webhook hinzufügen** aus. Geben Sie als *Payload URL* (Nutzlast-URL) `http://<publicIp:8080>/github-webhook/` ein, wobei `<publicIp>` die IP-Adresse des Jenkins-Servers ist. Stellen Sie sicher, dass Sie den nachgestellten Schrägstrich (`/`) hinzufügen. Übernehmen Sie die anderen Standardwerte für den Inhaltstyp, damit *push*-Ereignisse ausgelöst werden.
1. Wählen Sie **Webhook hinzufügen** aus.

    ![Erstellen eines GitHub-Webhooks für Jenkins](media/deploy-from-github-to-aks/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testen der gesamten CI/CD-Pipeline

Nun können Sie die gesamte CI/CD-Pipeline testen. Wenn Sie einen Codecommit nach GitHub pushen, werden die folgenden Schritte ausgeführt:

1. Der GitHub-Webhook benachrichtigt Jenkins.
1. Jenkins startet den Buildauftrag und pullt den letzten Codecommit aus GitHub.
1. Ein Docker-Build wird mit dem aktualisierten Code gestartet, und das neue Containerimage wird mit der letzten Buildnummer markiert.
1. Dieses neue Containerimage wird an Azure Container Registry gepusht.
1. Ihre Anwendung, die unter Azure Kubernetes Service ausgeführt wird, wird mit dem aktuellen Image aus Azure Container Registry aktualisiert.

Öffnen Sie auf dem Entwicklungscomputer die geklonte Anwendung mit einem Code-Editor. Öffnen Sie im Verzeichnis */azure-vote/azure-vote* die Datei mit dem Namen **config_file.cfg**. Aktualisieren Sie die vote-Werte in dieser Datei auf andere Werte als „cats“ und „dogs“, wie im folgenden Beispiel gezeigt:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Nach der Aktualisierung können Sie die Datei speichern und die Änderungen dann committen und an Ihren Fork des GitHub-Repositorys pushen. Der GitHub-Webhook löst in Jenkins einen neuen Buildauftrag aus. Überwachen Sie den Buildprozess auf dem Jenkins-Webdashboard. Es dauert einige Sekunden, den aktuellen Code zu pullen, das aktualisierte Image zu erstellen und zu pushen und die aktualisierte Anwendung in AKS bereitzustellen.

Wenn der Build abgeschlossen ist, aktualisieren Sie Ihren Webbrowser mit der Azure-Beispielabstimmungsanwendung. Ihre Änderungen werden wie im folgenden Beispiel angezeigt:

![Azure-Beispielabstimmungsanwendung in AKS durch den Jenkins-Buildauftrag aktualisiert](media/deploy-from-github-to-aks/azure-vote-updated.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/jenkins)
