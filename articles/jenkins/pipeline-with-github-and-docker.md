---
title: 'Tutorial: Erstellen einer Jenkins-Pipeline mit GitHub und Docker'
description: Es wird beschrieben, wie Sie in Azure einen virtuellen Jenkins-Computer erstellen können, der bei jedem Codecommit Pullvorgänge aus GitHub durchführt und einen neuen Docker-Container zum Ausführen Ihrer Anwendung erstellt.
keywords: Jenkins, Azure, DevOps, Pipeline, CI/CD, Docker
ms.topic: tutorial
ms.date: 03/27/2017
ms.custom: devx-track-jenkins
ms.openlocfilehash: 2a9bc23852a04b42b72628adda116585d354f860
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240702"
---
# <a name="tutorial-create-a-jenkins-pipeline-using-github-and-docker"></a>Tutorial: Erstellen einer Jenkins-Pipeline mit GitHub und Docker

Sie können zum Automatisieren der Erstellungs- und Testphase der Anwendungsentwicklung eine Pipeline für Continuous Integration und Deployment (CI/CD) verwenden. In diesem Tutorial erstellen Sie eine CI/CD-Pipeline auf einer Azure-VM und erfahren, wie Sie:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Jenkins installieren und konfigurieren können
> * eine Webhookintegrations zwischen GitHub und Jenkins erstellen können
> * Jenkins-Buildauftrag aus GitHub-Commits erstellen und auslösen können
> * ein Docker-Image für Ihre Anwendung erstellen können
> * überprüfen können, ob GitHub-Commits neue Docker-Images und -Updates für die ausgeführte Anwendung erstellen

Dieses Tutorial verwendet die CLI innerhalb des Diensts [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), der ständig auf die neueste Version aktualisiert wird. Wählen Sie zum Öffnen von Cloud Shell oben in einem Codeblock die Option **Ausprobieren** aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Erstellen einer Jenkins-Instanz
In einem vorherigen Tutorial zum [Anpassen eines virtuellen Linux-Computers beim ersten Start](/azure/virtual-machines/linux/tutorial-automate-vm-deployment) haben Sie erfahren, wie die Anpassung für virtuelle Computer mit cloud-init automatisiert wird. In diesem Tutorial wird eine cloud-init-Datei verwendet, um Jenkins und Docker auf einer VM zu installieren. Jenkins ist ein beliebter Open-Source-Automatisierungsserver, der sich problemlos in Azure integrieren lässt, um Continuous Integration (CI) und Continuous Delivery (CD) zu ermöglichen. Weitere Tutorials zur Verwendung von Jenkins finden Sie unter der [Jenkins in Azure-Hubs](https://docs.microsoft.com/azure/jenkins/).

Erstellen Sie in der aktuellen Shell eine Datei namens *cloud-init-jenkins.txt*, und fügen Sie die folgende Konfiguration ein. Erstellen Sie die Datei beispielsweise in Cloud Shell, nicht auf dem lokalen Computer. Geben Sie `sensible-editor cloud-init-jenkins.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. Stellen Sie sicher, dass die gesamte Datei „cloud-init“ ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Vor der Erstellung eines virtuellen Computers müssen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. In folgendem Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupJenkins* am Standort *eastus* erstellt:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Jetzt können Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer erstellen. Verwenden Sie den `--custom-data`-Parameter, um Ihre cloud-init-Konfigurationsdatei zu übergeben. Geben Sie den vollständigen Pfad von *cloud-init-jenkins.txt* an, wenn Sie die Datei außerhalb Ihres vorhandenen Arbeitsverzeichnisses gespeichert haben.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Die Erstellung und Konfigurieren der VM dauert einige Minuten.

Verwenden Sie [az vm open-port](/cli/azure/vm), um Port *8080* für den Jenkins-Datenverkehr zu öffnen, und verwenden Sie Port *1337* für die Anwendung „node.js“, die zum Ausführen einer Beispiel-App verwendet wird. Damit lassen Sie den Webdatenverkehr zu:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Konfigurieren von Jenkins
Rufen Sie die öffentliche IP-Adresse Ihrer VM ab, um auf die Jenkins-Instanz zuzugreifen:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Aus Sicherheitsgründen müssen Sie das ursprüngliche Administratorkennwort eingeben, dass in einer Textdatei auf Ihrer VM gespeichert ist, um mit der Installation von Jenkins beginnen zu können. Verwenden Sie die öffentliche IP-Adresse, die Sie im vorherigen Schritt abgerufen haben, um eine SSH-Verbindung mit Ihrer VM herzustellen:

```bash
ssh azureuser@<publicIps>
```

Vergewissern Sie sich mithilfe des Befehls `service`, dass Jenkins ausgeführt wird:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Zeigen Sie das `initialAdminPassword` für Ihre Jenkins-Installation an, und kopieren Sie es:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Sollte die Datei noch nicht verfügbar sein, warten Sie noch einige Minuten, bis die Installation von Jenkins und Docker abgeschlossen ist.

Öffnen Sie anschließend einen Webbrowser, und gehen Sie zu `http://<publicIps>:8080`. Schließen Sie das anfängliche Jenkins-Setup wie folgt ab:

- Wählen Sie **Select plugins to install** (Zu installierende Plug-Ins auswählen).
- Suchen Sie im Textfeld im oberen Bereich nach *GitHub*. Aktivieren Sie das Kontrollkästchen für *GitHub*, und klicken Sie anschließend auf **Installieren**.
- Erstellen Sie den ersten Administratorbenutzer. Geben Sie einen Benutzernamen (beispielsweise **admin**) und anschließend ein sicheres Kennwort ein. Geben Sie abschließend einen vollständigen Namen und eine E-Mail-Adresse an.
- Klicken Sie auf **Speichern und Beenden**.
- Klicken Sie auf **Start using Jenkins** (Jenkins verwenden), sobald Jenkins bereit ist.
  - Sollte in Ihrem Browser beim Start von Jenkins eine leere Seite angezeigt werden, starten Sie den Jenkins-Dienst neu. Geben Sie in Ihrer SSH-Sitzung `sudo service jenkins restart` ein, und aktualisieren Sie anschließend Ihren Webbrowser.
- Melden Sie sich bei Bedarf mit dem erstellten Benutzernamen und Kennwort bei Jenkins an.


## <a name="create-github-webhook"></a>Erstellen eines GitHub-Webhooks
Öffnen Sie die [node.js-Beispielapp „Hello World“](https://github.com/Azure-Samples/nodejs-docs-hello-world) aus dem Azure-Beispielrepository, um die Integration in GitHub zu konfigurieren. Wählen Sie die Schaltfläche **Fork** (Verzweigen) in der rechten oberen Ecke aus, um das Repository in Ihr GitHub-Konto zu verzweigen.

Erstellen eines Webhooks in der von Ihnen erstellten Verzweigung:

- Wählen Sie **Einstellungen** und anschließend **Webhooks** auf der linken Seite aus.
- Wählen Sie **Add webhook** (Webhook hinzufügen) aus, und geben Sie anschließend *Jenkins* in das Filterfeld ein.
- Geben Sie unter **Payload URL** (Nutzlast-URL) Folgendes ein: `http://<publicIps>:8080/github-webhook/`. Stellen Sie sicher, dass Sie den nachstehenden Schrägstrich (/) hinzufügen
- Wählen Sie unter **Content type** (Inhaltstyp) die Option *application/x-www-form-urlencoded* aus.
- Wählen Sie unter **Which events would you like to trigger this webhook?** (Welche Ereignisse sollen durch diesen Webhook ausgelöst werden?) die Option *Just the push event* (Nur das Push-Ereignis) aus.
- Aktivieren Sie das Kontrollkästchen **Active** (Aktiv).
- Klicken Sie auf **Add webhook** (Webhook hinzufügen).

![Hinzufügen eines GitHub-Webhooks in Ihr verzweigtes Repository](media/pipeline-with-github-and-docker/github-webhook.png)


## <a name="create-jenkins-job"></a>Erstellen eines Jenkins-Auftrags
Erstellen Sie einen Jenkins-Auftrag, damit Jenkins auf ein Ereignis in GitHub, wie z.B. das Entwickeln von Code, reagieren kann. Verwenden Sie die URLs für Ihre eigene GitHub-Verzweigung.

Klicken Sie auf Ihrer Jenkins-Website auf der Startseite auf **Create new jobs** (Neue Aufträge erstellen):

- Geben Sie *HelloWorld* als den Namen des Auftrags ein. Wählen Sie **Freestyle Project** und anschließend **OK** aus.
- Wählen Sie im Abschnitt **Allgemein** die Option für **GitHub-Projekt** aus, und geben Sie die URL Ihres verzweigten Repositorys ein (beispielsweise `https://github.com/cynthn/nodejs-docs-hello-world`).
- Wählen Sie im Bereich **Quellcodeverwaltung** die Option **Git** aus, und geben Sie die URL vom Typ *.git* Ihres verzweigten Repositorys ein (beispielsweise `https://github.com/cynthn/nodejs-docs-hello-world.git`).
- Wählen Sie im Bereich **Build Triggers** (Trigger erstellen) die Option **GitHub hook trigger for GITScm polling** (GitHub-Hooktrigger für GITScm-Abruf) aus.
- Wählen Sie im Abschnitt **Build** die Option **Buildschritt hinzufügen** aus. Wählen Sie **Execute shell** (Shell ausführen) aus, und geben Sie dann `echo "Test"` im Befehlsfenster ein.
- Wählen Sie unten auf der Auftragsseite **Speichern** aus.


## <a name="test-github-integration"></a>Testen der GitHub-Integration
Übernehmen Sie eine Änderung in Ihrer Verzweigung, um die GitHub-Integration mit Jenkins zu testen. 

Kehren Sie zur GitHub-Web-UI zurück, und wählen Sie Ihr verzweigtes Repository aus. Wählen Sie anschließend die Datei **index.js** aus. Wählen Sie das Stiftsymbol, um diese Datei zu bearbeiten, sodass in der sechsten Zeile Folgendes steht:

```javascript
response.end("Hello World!");
```

Wählen Sie die Schaltfläche **Commit Changes** (Änderungen übernehmen) am unteren Rand aus, um die Änderungen zu übernehmen.

In Jenkins können Sie mit einem neuen Build im Bereich **Build history** (Buildverlauf) in der unteren linken Ecke der Auftragsseite beginnen. Wählen Sie den Link der Buildnummer und dann auf der linken Seite **Konsolenausgabe** aus. Sie können sich die von Jenkins durchgeführten Schritte anschauen, während ein Pullvorgang für Ihren Code aus GitHub durchgeführt wird. Der Buildvorgang gibt die Meldung `Test` an die Konsole aus. Jedes Mal, wenn ein Commit in GitHub ausgeführt wird, wendet sich der Webhook an Jenkins und löst so einen neuen Build aus.


## <a name="define-docker-build-image"></a>Definieren eines Docker-Buildimages
Erstellen Sie ein Docker-Image zum Ausführen der Anwendung, um das auf Ihren GitHub-Commits basierte Ausführen der node.js-App zu sehen. Das Image wird aus einer Dockerfile-Datei erstellt, die die Konfiguration des Containers definiert, der die App ausführt. 

Wechseln Sie von der SSH-Verbindung mit Ihrer VM zum Jenkins-Arbeitsbereichsverzeichnis, das nach dem von Ihnen in einem vorherigen Schritt erstellten Auftrag benannt ist. In diesem Beispiel hieß es *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Erstellen Sie in diesem Arbeitsbereichsverzeichnis mithilfe von `sudo sensible-editor Dockerfile` eine Datei, und fügen Sie den folgenden Inhalt ein. Stellen Sie sicher, dass die gesamte Dockerfile-Datei ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Diese Dockerfile-Datei verwendet das node.js-Basisimage mithilfe von Alpine Linux, macht Port 1337 verfügbar, auf dem die Anwendung „Hello World“ ausgeführt wird, und kopiert anschließend die Anwendungsdateien und initialisiert sie.


## <a name="create-jenkins-build-rules"></a>Erstellen von Jenkins-Buildregeln
Sie haben in einem vorherigen Schritt eine einfache Jenkins-Buildregel erstellt, die eine Meldung an die Konsole ausgegeben hat. Erstellen Sie die Buildschritte, mit denen Sie die Dockerfile-Datei verwenden und die Anwendung ausführen können.

Kehren Sie zu Ihrer Jenkins-Instanz zurück, und wählen Sie den Auftrag aus, den Sie in einem vorherigen Schritt erstellt haben. Wählen Sie links **Configure** (Konfigurieren), und scrollen Sie nach unten zum Bereich **Build**:

- Entfernen Sie den vorhandenen Buildschritt `echo "Test"`. Wählen Sie dazu das rote Kreuz in der oberen rechten Ecke des Felds des vorhandenen Buildschritts aus.
- Wählen Sie **Add build step** (Buildschritt hinzufügen) und anschließend **Execute shell** (Shell ausführen) aus.
- Geben Sie im Feld **Befehl** die folgenden Docker-Befehle ein, und wählen Sie anschließend **Speichern** aus:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Die Docker-Buildschritte erstellen ein Image und markieren es mit der Jenkins-Buildnummer, damit Sie einen Verlauf der Images erhalten. Alle vorhandenen Container, die die Anwendung ausführen, werden beendet und entfernt. Ein neuer Container wird dann mithilfe des Images gestartet. Er führt die node.js-Anwendung auf Grundlage der letzten Commits in GitHub aus.


## <a name="test-your-pipeline"></a>Testen Ihrer Pipeline
Bearbeiten Sie erneut die Datei *index.js* in Ihrem verzweigten GitHub-Repository, und wählen Sie **Änderungen übernehmen** aus. In Jenkins wird ein neuer Auftrag auf Grundlage des Webhooks für GitHub gestartet. Das Erstellen des Docker-Images und das Starten Ihrer Anwendung in einem neuen Container nimmt einige Sekunden in Anspruch.

Falls dies vonnöten ist, können Sie die öffentliche IP-Adresse Ihrer VM erneut abrufen:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Öffnen Sie einen Webbrowser, und geben Sie `http://<publicIps>:1337` ein. Ihre node.js-Anwendung wird wie folgt mit den letzten Änderungen in Ihrer GitHub-Verzweigung angezeigt:

![Ausgeführte Node.js-App](media/pipeline-with-github-and-docker/running-nodejs-app.png)

Bearbeiten Sie anschließend die Datei *index.js* noch einmal in GitHub, und übernehmen Sie die Änderungen. Das Abschließen des Auftrags in Jenkins nimmt einige Sekunden in Anspruch. Aktualisieren Sie danach Ihren Webbrowser, um die aktualisierte Version Ihrer in einem neuen Container ausgeführten App wie folgt anzuzeigen:

![Ausführen der node.js-Anwendung nach einem weiteren GitHub-Commitvorgang](media/pipeline-with-github-and-docker/another-running-nodejs-app.png)


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie GitHub so konfiguriert, dass es einen Jenkins-Buildauftrag bei jedem Code-Commit ausführt und anschließend einen Docker-Container zum Testen der Anwendung bereitstellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * eine Jenkins-VM erstellen können
> * Jenkins installieren und konfigurieren können
> * eine Webhookintegrations zwischen GitHub und Jenkins erstellen können
> * Jenkins-Buildauftrag aus GitHub-Commits erstellen und auslösen können
> * ein Docker-Image für Ihre Anwendung erstellen können
> * überprüfen können, ob GitHub-Commits neue Docker-Images und -Updates für die ausgeführte Anwendung erstellen

Im nächsten Tutorial erhalten Sie Informationen zum Integrieren von Jenkins in Azure DevOps Services.

> [!div class="nextstepaction"]
> [Bereitstellen Ihrer App auf virtuellen Linux-Computern mithilfe von Jenkins und Azure DevOps Services](deploy-to-linux-vm-using-azure-devops-services.md)