---
title: Installieren der Application Insights-Clientbibliothek
description: Hier erfahren Sie, wie Sie dem Code auf dem virtuellen Computer die Azure SDK-Clientbibliothek hinzufügen, um mit dem Sammeln von App-Protokollen in der Azure-Cloud zu beginnen.
ms.topic: tutorial
ms.date: 11/13/2020
ms.custom: devx-track-js
ms.openlocfilehash: a6fb29dd059922ead67b4cef5107c9407f40e294
ms.sourcegitcommit: ed749b136f0d6b876fd5866ba4a151c73af5b71f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674710"
---
# <a name="5-install-azure-sdk-client-library-to-monitor-web-app"></a>5. Installieren der Azure SDK-Clientbibliothek zum Überwachen der Web-App

In diesem Schritt wird dem Code auf dem virtuellen Computer die Azure SDK-Clientbibliothek hinzugefügt, um mit dem Sammeln von App-Protokollen in der Azure-Cloud zu beginnen.

## <a name="edit-indexjs-for-logging-with-azure-monitor-application-insights"></a>Bearbeiten von „index.js“ für die Protokollierung mit Azure Monitor Application Insights

1. Verwenden Sie den auf dem virtuellen Computer bereitgestellten Text-Editor [Nano](https://www.nano-editor.org/dist/latest/nano.html#Editor-Basics), um `index.js` zu bearbeiten. 

    ```bash
    sudo nano index.js
    ```

1. Bearbeiten Sie die Datei `index.js`, um die Clientbibliothek und den Protokollierungscode hinzuzufügen, wie im Anschluss hervorgehoben. Bei vielen Bash-Shells können Daten kopiert und direkt in Nano eingefügt werden. 

    :::code language="JavaScript" source="~/../js-e2e-vm/index-logging.js" highlight="5-28" :::

1. Drücken Sie als Nächstes `Control+x` zum Verlassen und anschließend `y`, um die Änderungen zu speichern. Änderungen an der Web-App werden von PM2 überwacht. Durch diese Änderung wurde die App neu gestartet, ohne den virtuellen Computer neu starten zu müssen. 

1. Testen Sie die App mit der neuen Route `trace` in einem Webbrowser:

    ```http
    http://REPLACE-WITH-YOUR-IP/trace
    ```

    Im Browser wird die Antwort `tracing...` mit Ihrer IP-Adresse angezeigt.

## <a name="viewing-the-vm-logs-for-nginx-and-pm2"></a>Anzeigen der VM-Protokolle für NGINX und PM2

Die durch den virtuellen Computer gesammelten Protokolle für NGINX und PM2 können angezeigt werden.

| Dienst | Speicherort des Protokolls|
|--|--|
|NGINX| /var/log/nginx/access.log|
|PM2| /var/log/pm2.log|

1. Zeigen Sie das VM-Protokoll für den NGINX-Proxydienst an. Verwenden Sie in der gleichen Bash-Shell den folgenden Befehl, um das Protokoll anzuzeigen:

    ```bash
     cat /var/log/nginx/access.log
    ```

    Das Protokoll enthält den Aufruf Ihres lokalen Computers. 

    ```console
     "GET /trace HTTP/1.1" 200 10 "-"
    ```

1. Zeigen Sie das VM-Protokoll für den PM2-Dienst an. Verwenden Sie in der gleichen Bash-Shell den folgenden Befehl, um das Protokoll anzuzeigen:

    ```bash
     cat /var/log/pm2.log
    ```

    Das Protokoll enthält den Aufruf Ihres lokalen Computers. 

    ```console
    Hello world app listening on port 3000!
    testing from trace route 76.22.73.183
    ```

1. In dem Tutorial wird keine Verbindung mit dem virtuellen Computer mehr hergestellt. Beenden Sie die SSH-Verbindung mithilfe des folgenden Befehls in der Bash-Shell: 

    ```bash
    exit
    ```

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Anzeigen von Protokollen im Azure-Portal](azure-monitor-application-insights-logs.md) 