---
title: Erstellen eines virtuellen Linux-Computers
description: Hier erfahren Sie, wie Sie die Azure CLI verwenden, um Ihren virtuellen Computer zu erstellen und zu konfigurieren. An diesem Punkt des Tutorials sollten Sie über ein geöffnetes Terminalfenster verfügen und sich mit der Azure CLI bei der Azure-Cloud für das Abonnement angemeldet haben, in dem Sie den virtuellen Computer erstellen möchten.
ms.topic: tutorial
ms.date: 01/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: baed6874a89a8291e3a1b03c45db43565428ff20
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561666"
---
# <a name="3-create-linux-virtual-machine-using-azure-cli"></a>3. Erstellen eines virtuellen Linux-Computers mithilfe der Azure CLI

In diesem Abschnitt des Tutorials wird die Azure CLI verwendet, um einen virtuellen Computer zu erstellen und zu konfigurieren. An diesem Punkt des Tutorials sollten Sie über ein geöffnetes Terminalfenster verfügen und sich bei der Azure-Cloud für das Abonnement angemeldet haben, in dem Sie den virtuellen Computer erstellen möchten. 

Die Schritte mit der Azure CLI können alle in einer einzelnen Azure CLI-Instanz ausgeführt werden. Wenn Sie das Fenster schließen oder bei der Verwendung der Azure CLI zwischen verschiedenen Orten wechseln (beispielsweise zwischen Cloud Shell und Ihrem lokalen Terminal), müssen Sie sich erneut anmelden. 

## <a name="create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation"></a>Erstellen einer cloud-init-Datei, um die Erstellung des virtuellen Linux-Computers zu beschleunigen

In diesem Tutorial wird eine cloud-init-Konfigurationsdatei verwendet, um sowohl den NGINX-Reverseproxyserver als auch den Express.js-Server zu erstellen. NGINX wird verwendet, um den Express.js-Port (3000) an den öffentlichen Port (80) weiterzuleiten. 

`runcmd` hat verschiedene Aufgaben:
* Herunterladen und Installieren von Node.js
* Klonen des Express.js-Beispielrepositorys
* Installieren der Express.js-Abhängigkeiten
* Starten der Express.js-App mit PM2

1. Erstellen Sie eine lokale Datei namens `cloud-init-github.txt`, und speichern Sie den folgenden Inhalt in der Datei, oder speichern Sie [die Datei des Repositorys](https://github.com/Azure-Samples/js-e2e-vm/blob/main/cloud-init-github.txt) auf Ihrem lokalen Computer. Die formatierte Datei vom Typ [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html#yaml-examples) muss sich im gleichen Ordner befinden, der auch im Terminalpfad für Ihre Azure CLI-Befehle verwendet wird.

    :::code language="yaml" source="~/../js-e2e-vm/cloud-init-github.txt" :::

## <a name="create-a-virtual-machine-resource"></a>Erstellen einer VM-Ressource 

Geben Sie den [Azure CLI-Befehl](/cli/azure/vm#az_vm_create) an einem Terminal ein, um eine Azure-Ressource eines virtuellen Linux-Computers zu erstellen. Der Befehl erstellt den virtuellen Computer auf der Grundlage der cloud-init-Datei und generiert die SSH-Schlüssel für Sie. Der Speicherort der Schlüssel wird bei der Befehlsausführung angezeigt. 

```azurecli
az vm create \
  --resource-group rg-demo-vm-eastus \
  --name demo-vm \
  --location eastus \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys \
  --custom-data cloud-init-github.txt
```

Der Prozess kann einige Minuten dauern. Nach Abschluss des Prozesses werden von der Azure CLI Informationen zur neuen Ressource zurückgegeben. Notieren Sie sich den Wert für `publicIpAddress`. Er wird benötigt, um die Web-App in einem Browser anzuzeigen und eine Verbindung mit dem virtuellen Computer herzustellen. 
     

## <a name="open-port-for-virtual-machine"></a>Öffnen eines Ports für den virtuellen Computer

Nach der Erstellung verfügt der virtuelle Computer zunächst über _keine_ geöffneten Ports. Öffnen Sie den Port 80 mithilfe des folgenden [Azure CLI-Befehls](/cli/azure/vm#az_vm_open_port), um die Web-App öffentlich verfügbar zu machen:

```azurecli
az vm open-port \
  --port 80 \
  --resource-group rg-demo-vm-eastus \
  --name demo-vm
```

## <a name="browse-to-web-site"></a>Öffnen der Website im Browser

1. Verwenden Sie die öffentliche IP-Adresse in einem Webbrowser, um sich zu vergewissern, dass der virtuelle Computer verfügbar ist und ausgeführt wird. Ändern Sie die URL so, dass der Wert von `publicIpAddress`verwendet wird.

    ```HTTP
    http://YOUR-PUBLIC-IP-ADDRESS
    ```

    Die folgende Abbildung zeigt die Web-App. Ihre App besitzt allerdings eine andere IP-Adresse. Sollte für die Ressource ein Gatewayfehler auftreten, warten Sie eine Minute, und versuchen Sie es dann erneut. (Unter Umständen dauert es etwas, bis die Web-App gestartet wurde.) 

    :::image type="content" source="../../media/tutorial-vm/basic-web-app.png" alt-text="Einfache App, die über einen virtuellen Linux-Computer in Azure bereitgestellt wird":::

    Die Codedatei für die Web-App enthält zunächst eine einzelne Route, die Ihre Client-IP-Adresse anzeigt (weitergeleitet über den NGINX-Proxy). 

    :::code language="JavaScript" source="~/../js-e2e-vm/index.js" :::

1. Lassen Sie das Terminal geöffnet. Es wird während des gesamten Tutorials immer wieder benötigt.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Herstellen einer SSH-Verbindung mit einem virtuellen Linux-Computer](connect-linux-virtual-machine-ssh.md) 