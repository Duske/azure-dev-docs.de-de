---
title: SSH-Verbindung mit virtuellem Computer
description: Hier erfahren Sie, wie Sie eine SSH-Verbindung mit Ihrem virtuellen Linux-Computer herstellen.  Wenn Sie ein modernes Mac-, Windows- oder Linux-Betriebssystem verwenden, sollte terminalbasiertes Client-SSH bereits installiert sein.
ms.topic: tutorial
ms.date: 01/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: c4b9577c93e37c28145abe8e976a93cd6ff39197
ms.sourcegitcommit: 075f39972e390e79ed09a3fcfdbfc776727e08fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952492"
---
# <a name="4-connect-to-linux-virtual-machine-using-ssh"></a>4. Herstellen einer SSH-Verbindung mit einem virtuellen Linux-Computer

In diesem Abschnitt des Tutorials wird SSH in einem Terminal verwendet, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. [SSH](https://www.ssh.com/ssh/) ist ein gängiges Tool, das mit vielen modernen Shells bereitgestellt wird. Dazu zählt auch Azure Cloud Shell. 

## <a name="connect-with-ssh-and-change-web-app"></a>Herstellen einer SSH-Verbindung und Ändern der Web-App

Verwenden Sie das gleiche Terminal oder Shellfenster wie in den vorherigen Schritten. 

1. Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit Ihrem virtuellen Computer her. Bei diesem Prozess wird davon ausgegangen, dass der verwendete SSH-Client Ihre SSH-Schlüssel finden kann, die im Rahmen der VM-Erstellung erstellt und auf Ihrem lokalen Computer platziert wurden. Wenn Sie gefragt werden, ob Sie die Verbindungsherstellung fortsetzen möchten, antworten Sie mit `yes`. Nach Abschluss der Verbindungsherstellung ändert sich die Eingabeaufforderung des Terminals, um den virtuellen Remotecomputer anzugeben. 

    Ersetzen Sie `YOUR-PUBLIC-IP-ADDRESS` durch die öffentliche IP-Adresse Ihres eigenen virtuellen Computers. 

    ```console
    ssh azureuser@YOUR-PUBLIC-IP-ADDRESS
    ``` 

1. Verwenden Sie den folgenden Befehl, um zu prüfen, wo Sie sich auf dem virtuellen Computer befinden. Sie sollten sich im Stammverzeichnis von „azureuser“ befinden: `/home/azureuser`. 

    ```bash
    pwd
    ```

1. Ihre Web-App befindet sich im Unterverzeichnis `myapp`. Wechseln Sie zum Verzeichnis `myapp`, und listen Sie den Inhalt auf:

    ```bash
    cd myapp && ls -l
    ```

    Der Inhalt sollte in etwa wie folgt aussehen und das auf dem virtuellen Computer geklonte GitHub-Repository sowie die npm-Paketdateien darstellen:
    
    ```console
    -rw-r--r--   1 root root   891 Nov 11 20:23 cloud-init-github.txt
    -rw-r--r--   1 root root  1347 Nov 11 20:23 index-logging.js
    -rw-r--r--   1 root root   282 Nov 11 20:23 index.js
    drwxr-xr-x 190 root root  4096 Nov 11 20:23 node_modules
    -rw-r--r--   1 root root 84115 Nov 11 20:23 package-lock.json
    -rw-r--r--   1 root root   329 Nov 11 20:23 package.json
    -rw-r--r--   1 root root   697 Nov 11 20:23 readme.md
    ```

## <a name="install-monitoring-sdk"></a>Installieren eines SDK für die Überwachung

Installieren Sie die [Azure SDK-Clientbibliothek für Application Insights](https://www.npmjs.com/package/applicationinsights).

```bash
sudo npm install --save applicationinsights
```

## <a name="add-monitoring-instrumentation-key"></a>Hinzufügen des Instrumentierungsschlüssels für die Überwachung

1. Verwenden Sie den Editor [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics), um die Datei `package.json` zu ändern.

    ```bash
    sudo nano package.json
    ```

1. Bearbeiten Sie das Startskript der Datei, um eine Umgebungsvariable einzuschließen. Ersetzen Sie `REPLACE-WITH-YOUR-KEY` durch den Wert Ihres Instrumentierungsschlüssels.

    ```json
    "start": "APPINSIGHTS_INSTRUMENTATIONKEY=REPLACE-WITH-YOUR-KEY pm2 start index.js --watch --log /var/log/pm2.log"
    ```

1. Verwenden Sie die folgenden Befehle, um PM2 zu beenden und neu zu starten:

    ```bash
    sudo npm run-script stop && sudo npm start
    ```

    Die Azure-Clientbibliothek befindet sich nun im Verzeichnis _node_modules_, und der Schlüssel wird als Umgebungsvariable an die App übergeben. Im nächsten Schritt wird der erforderliche Code zu `index.js` hinzugefügt. 

1. Lassen Sie das Terminal und die Verbindung mit Ihrem virtuellen Computer für den nächsten Schritt geöffnet.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Installieren der Azure SDK-Clientbibliothek zum Überwachen der Web-App](azure-monitor-application-insights-nodejs-expressjs-code.md) 