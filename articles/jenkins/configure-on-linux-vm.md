---
title: 'Schnellstart: Konfigurieren von Jenkins über die Azure-Befehlszeilenschnittstelle'
description: Es wird beschrieben, wie Sie Jenkins auf einem virtuellen Azure-Linux-Computer installieren und eine Java-Beispielanwendung erstellen.
keywords: Jenkins, Azure, DevOps, Portal, Linux, virtueller Computer
ms.topic: quickstart
ms.date: 08/21/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: 4e2250e6ca76c804f08a4f6ab4715ae4fc094570
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725214"
---
# <a name="quickstart-configure-jenkins-using-azure-cli"></a>Schnellstart: Konfigurieren von Jenkins über die Azure-Befehlszeilenschnittstelle

In dieser Schnellstartanleitung erfahren Sie, wie Sie [Jenkins](https://jenkins.io) auf einem virtuellen Computer unter Ubuntu Linux mit den Tools und Plug-Ins installieren, die für die Arbeit mit Azure konfiguriert sind.

In diesem Schnellstart führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen Sie eine Setupdatei, die Jenkins herunterlädt und installiert.
> * Erstellen einer Ressourcengruppe
> * Erstellen eines virtuellen Computers mit der Setupdatei
> * Öffnen von Port 8080 für den Zugriff auf Jenkins auf dem virtuellen Computer
> * Herstellen einer Verbindung mit dem virtuellen Computer über SSH
> * Konfigurieren eines Jenkins-Beispielauftrags basierend auf einer Java-Beispiel-App in GitHub
> * Erstellen des Jenkins-Beispielauftrags

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Konfigurieren von Jenkins Probleme auftreten, finden Sie auf der [Cloudbees-Seite zur Jenkins-Installation](https://www.jenkins.io/doc/book/installing/) die neuesten Anweisungen und bekannten Probleme.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview), und wechseln Sie, falls noch nicht geschehen, zu **Bash**.

1. Erstellen Sie eine Datei mit dem Namen `cloud-init-jenkins.txt`.

    ```bash
    code cloud-init-jenkins.txt
    ```

1. Fügen Sie den folgenden Code in die neue Datei ein:

    ```json
    #cloud-config
    package_upgrade: true
    runcmd:
      - apt install openjdk-8-jdk -y
      - wget -qO - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
      - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
      - apt-get update && apt-get install jenkins -y
      - service jenkins restart
    ```

1. Speichern Sie die Datei ( **&lt;STRG+S**), und beenden Sie den Editor ( **&lt;STRG+Q**).

1. Erstellen Sie mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. Möglicherweise müssen Sie den Parameter `--location` durch den entsprechenden Wert für Ihre Umgebung ersetzen.

    ```azurecli
    az group create \
    --name QuickstartJenkins-rg \
    --location eastus
    ```

1. Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer.

    ```azurecli
    az vm create \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm \
    --image UbuntuLTS \
    --admin-username "azureuser" \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
    ```

1. Überprüfen Sie die Erstellung (und den Zustand) des neuen virtuellen Computers mit [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartJenkins-vm']"
    ```

1. Jenkins wird standardmäßig an Port 8080 ausgeführt. Öffnen Sie daher mit dem Befehl [az vm open](/cli/azure/vm#az-vm-open-port) Port 8080 auf dem neuen virtuellen Computer.

    ```azurecli
    az vm open-port \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm  \
    --port 8080 --priority 1010
    ```

## <a name="configure-jenkins"></a>Konfigurieren von Jenkins

1. Rufen Sie mit dem Befehl [az vm show](/cli/azure/vm#az-vm-show) die öffentliche IP-Adresse für den virtuellen Beispielcomputer ab.

    ```azurecli
    az vm show \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm -d \
    --query [publicIps] \
    --output tsv
    ```

    **Hinweise**:

    - Der Parameter `--query` schränkt die Ausgabe auf die öffentlichen IP-Adressen für den virtuellen Computer ein.

1. Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen. Sie müssen die Verbindungsanforderung bestätigen.

    ```azurecli
    ssh azureuser@<ip_address>
    ```

    **Hinweise**:

    - Nach erfolgreicher Verbindung enthält die Cloud Shell-Aufforderung den Benutzernamen und den Namen des virtuellen Computers: `azureuser@QuickstartJenkins-vm`.

1. Überprüfen Sie, ob Jenkins ausgeführt wird, indem Sie den Status des Jenkins-Diensts abrufen.

    ```bash
    service jenkins status
    ```

1. Rufen Sie automatisch generierte Jenkins-Kennwort ab.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Öffnen Sie die folgende URL in einem Browser mit der IP-Adresse: `http://<ip_address>:8080`

1. Geben Sie das zuvor abgerufene Kennwort ein, und wählen Sie **Weiter** aus.

    ![Startseite zum Entsperren von Jenkins](./media/configure-on-linux-vm/unlock-jenkins.png)

1. Wählen Sie **Zu installierende Plug-Ins auswählen** aus.

    ![Wählen Sie die Option zum Installieren der ausgewählten Plug-Ins aus.](./media/configure-on-linux-vm/select-plugins.png)

1. Geben Sie im Filterfeld oben auf der Seite `github` ein. Wählen Sie das GitHub-Plug-In und dann **Installieren** aus.

    ![Installieren der GitHub-Plug-Ins](./media/configure-on-linux-vm/install-github-plugin.png)

1. Geben Sie die Informationen für den ersten Administratorbenutzer ein, und wählen Sie **Speichern und weiter** aus.

    ![Informationen für den ersten Administratorbenutzer eingeben](./media/configure-on-linux-vm/create-first-user.png)

1. Wählen Sie auf der Seite **Instanzkonfiguration** die Option **Speichern und beenden** aus.

    ![Bestätigungsseite für Instanzkonfiguration](./media/configure-on-linux-vm/instance-configuration.png)

1. Wählen Sie **Jenkins verwenden** aus.

    ![Jenkins ist bereit!](./media/configure-on-linux-vm/start-using-jenkins.png)

## <a name="create-your-first-job"></a>Erstellen Ihres ersten Auftrags

1. Wählen Sie auf der Jenkins-Homepage die Option **Auftrag erstellen** aus.

    ![Homepage der Jenkins-Konsole](./media/configure-on-linux-vm/jenkins-home-page.png)

1. Geben Sie den Auftragsnamen `mySampleApp` ein, wählen Sie **Freies Projekt** aus, und wählen Sie dann **OK** aus.

    ![Erstellung neuer Aufträge](./media/configure-on-linux-vm/new-job.png)

1. Wählen Sie die Registerkarte **Quellcodeverwaltung** aus. Aktivieren Sie **Git**, und geben Sie die folgende URL als Wert für **Repository-URL** ein: `https://github.com/spring-guides/gs-spring-boot.git`

    ![Definieren des Git-Repositorys](./media/configure-on-linux-vm/source-code-management.png)

1. Wählen Sie auf der Registerkarte **Build** die Option **Buildschritt hinzufügen** aus.

    ![Hinzufügen eines neuen Buildschritts](./media/configure-on-linux-vm/add-build-step.png)

1. Wählen Sie im Dropdownmenü die Option **Gradle-Skript aufrufen** aus.

    ![Auswählen der Option „Gradle-Skript“](./media/configure-on-linux-vm/invoke-gradle-script-option.png)

1. Wählen Sie **Use Gradle Wrapper** (Gradle-Wrapper verwenden) aus, und geben Sie dann im Feld **Wrapper location** (Wrapperspeicherort) die Zeichenfolge `complete` und im Feld **Tasks** (Aufgaben) die Zeichenfolge `build` ein.

    ![Gradle-Skriptoptionen](./media/configure-on-linux-vm/gradle-script-options.png)

1. Wählen Sie **Erweitert** aus, und geben Sie dann im Feld **Stammerstellungsskript** die Zeichenfolge `complete` ein.

    ![Erweiterte Gradle-Skriptoptionen](./media/configure-on-linux-vm/root-build-script.png)

1. Scrollen Sie auf der Seite nach unten, und wählen Sie **Speichern** aus.

## <a name="build-the-sample-java-app"></a>Erstellen der Java-Beispiel-App

1. Wenn die Homepage für das Projekt angezeigt wird, wählen Sie **Jetzt erstellen** aus, um den Code zu kompilieren und die Beispiel-App zu packen.

    ![Homepage des Projekts](./media/configure-on-linux-vm/project-home-page.png)

1. Eine Grafik unter der Überschrift **Buildverlauf** gibt an, dass der Auftrag erstellt wird.

    ![Auftragsbuild in Arbeit](./media/configure-on-linux-vm/job-currently-building.png)

1. Wählen Sie nach Abschluss des Buildvorgangs den Link **Arbeitsbereich** aus.

    ![Link „Arbeitsbereich“ auswählen](./media/configure-on-linux-vm/job-workspace.png)

1. Navigieren Sie zu `complete/build/libs`, um zu überprüfen, ob die `.jar` Datei erfolgreich erstellt wurde.

    ![Die Zielbibliothek überprüft, ob der Build erfolgreich war.](./media/configure-on-linux-vm/successful-build.png)

1. Der Jenkins-Server ist nun für die Erstellung Ihrer eigenen Projekte in Azure bereit.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Jenkins in Azure](/azure/developer/jenkins)