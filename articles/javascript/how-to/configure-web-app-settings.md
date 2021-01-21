---
title: 'Hosten von Web-Apps: Konfigurationseinstellungen'
description: Hier erfahren Sie, wie Sie allgemeine Konfigurationen für Ihre Web-App festlegen.
ms.topic: conceptual
ms.date: 01/11/2021
ms.custom: devx-track-js
ms.openlocfilehash: ec183d08460b932cec1dea8d301f2877d7bc9ece
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561696"
---
# <a name="hosting-web-apps-on-azure"></a>Hosten von Web-Apps in Azure

Hier erfahren Sie, wie Sie allgemeine Konfigurationen für Ihre Web-App festlegen. Wenn eine gängige Einstellung fehlt, erstellen Sie im Feedback ein Problem, und informieren Sie uns darüber. 

Alle **erforderlichen Einstellungen** werden beim Erstellen der Ressource angefordert. Wird eine Einstellung nicht zu diesem Zeitpunkt angefordert, erhält sie einen Standardwert, den Sie nach der Erstellung der Ressource ändern können. 

## <a name="what-is-a-web-app"></a>Was ist eine Web-App?

Eine Web-App ist ein beliebiges Element, das über eine Internet-URL erreicht werden kann. Es gibt zahlreiche Azure-Dienste, die als Web-App angesehen werden können. Die wichtigsten Dienste, die normalerweise für eine Web-App verwendet werden:

* App-Dienste. Dazu gehört u. a. Folgendes:
    * [Statische Web-Apps](/azure/static-web-apps/)
    * [Funktionen](/azure/azure-functions/)
    * [Web-Apps](/azure/app-service/)
    * [Container](/azure/app-service/configure-custom-container?pivots=container-linux)
* Container: [Kubernetes](/azure/aks/)-Container und einzelne [Container](/azure/container-instances/)
* Virtuelle Computer: [Windows](/azure/virtual-machines/windows) und [Linux](/azure/virtual-machines/linux)

## <a name="how-to-configure-web-app-settings"></a>Konfigurieren von Einstellungen für Web-Apps

Die meisten Azure-Dienste bieten vier Möglichkeiten zum Konfigurieren von Einstellungen:

* [Azure portal](https://portal.azure.com)
* [Azure SDK](https://github.com/Azure/azure-sdk) für Wartungsaufgaben (in der Regel als Verwaltung gekennzeichnet)
* [Azure CLI](/cli/azure/)
* [Azure PowerShell](/powershell/azure/)

Viele Einstellungen können auch in Visual Studio Code mit [Erweiterungen](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) konfiguriert werden. 

## <a name="use-default-domain-name-provided-by-azure"></a>Verwenden des von Azure bereitgestellten Standarddomänennamens

Die meisten Azure-Dienste stellen eine URL für Ihre Ressource bereit. Der Dienstname bestimmt die Unterdomäne, und der Rest der Domäne stammt von Azure. 

Beispiel:

* Azure Functions: `https://my-function-app.azurewebsites.net`
* Azure-Web-App: `https://my-web-app.azurewebsites.net`
* Azure Storage Blob: `https://mystorage.blob.core.windows.net/`

Einige Dienste, etwa statische Web-Apps, stellen Ihnen eine relativ eindeutige Unterdomäne bereit, die Sie sofort in der Produktionsumgebung verwenden können:

* Statische Azure-Web-Apps: `https://gentle-tree-0b08aaf12.azurestaticapps.net`

## <a name="configure-custom-domain-name"></a>Konfigurieren eines benutzerdefinierten Domänennamens 

Jeder Dienst verfügt über einen eigenen Mechanismus zum Hinzufügen einer benutzerdefinierten Domäne. 

* [Statische Azure-Web-Apps](/azure/static-web-apps/custom-domain)
* [Azure Functions](/azure/app-service/app-service-web-tutorial-custom-domain) und [Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-domain): Funktionen basieren auf Web-Apps und nutzen daher denselben Mechanismus.
* [Azure-Speicherblobs](/azure/storage/blobs/storage-custom-domain-name?tabs=azure-portal)

## <a name="configure-port-forwarding"></a>Konfigurieren der Portweiterleitung

Sie müssen [die Portnummer der App zuordnen](/azure/app-service/configure-language-nodejs?pivots=platform-windows#get-port-number), wenn es sich dabei nicht um den Standardport `8080` handelt. Dadurch kann der App-Dienst Anforderungen an den richtigen Port weiterleiten. 

## <a name="configure-browser-for-cors-to-connect-with-server"></a>Konfigurieren des Browsers für CORS zum Herstellen einer Serververbindung

Wenn Sie eine Verbindung mit Ihrem eigenen Server herstellen und die CORS-Sicherheit beim lokalen Ausführen und Debuggen mit dem Client ignorieren müssen, empfiehlt es sich, diese Einstellung in der Visual Studio Code-Debugdatei `launch.json` zu konfigurieren, um Einstellungen an den Browser zu übergeben und die Sicherheit zu deaktivieren. 

Da diese Datei ausschließlich zum Starten einer Debugsitzung verwendet wird, kann sie gefahrlos in Ihre Quellcodeverwaltung eingecheckt werden. 

### <a name="configure-edge-browser-to-disable-cors-for-debugging"></a>Konfigurieren des Edge-Browsers zum Deaktivieren von CORS für das Debuggen

Mit der folgenden Datei vom Typ `launch.json` wird die Einstellung für den **Edge-Browser** konfiguriert, um die CORS-Sicherheit für die Debugsitzung zu deaktivieren: `--disable-web-security`. 

```json
{
    // Debug client, with requests to server, w/o 
    // changes to client or server
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch Edge against localhost",
            "request": "launch",
            "type": "pwa-msedge",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}",
            "runtimeArgs": [
                "--disable-web-security"
            ],
        },
    ]
}
```

### <a name="configure-chrome-browser-to-disable-cors-for-debugging"></a>Konfigurieren des Chrome-Browsers zum Deaktivieren von CORS für das Debuggen

Mit der folgenden Datei vom Typ `launch.json` wird die Einstellung für den **Chrome-Browser** konfiguriert, um die CORS-Sicherheit für die Debugsitzung zu deaktivieren: `--disable-web-security`. 

```json
{
    // Debug client, with requests to server, w/o 
    // changes to client or server
    "version": "0.2.0",
    "configurations": [
        {
            "type": "pwa-chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:3000",
            "webRoot": "${workspaceFolder}",
            "runtimeArgs": [
                "--disable-web-security"
            ],
        }
    ]
}
```


## <a name="configure-certificates"></a>Konfigurieren von Zertifikaten

Sollte Ihre App umgehend Zertifikate erfordern, haben Sie verschiedene Möglichkeiten, [Zertifikate bereitzustellen](/azure/app-service/configure-ssl-certificate#import-an-app-service-certificate):

* Hochladen eines eigenen Zertifikats
* Verwalten von Zertifikaten im App-Dienst
* Importieren des Zertifikats aus Azure Key Vault
* Bereitstellen des Zertifikats [im Code](/azure/app-service/configure-ssl-certificate-in-code)

## <a name="configure-secrets"></a>Konfigurieren von Geheimnissen

Geheimnisse werden in der Regel mit den folgenden Methoden bereitgestellt:

* Azure Key Vault: Erstellen Sie eine Ressource für diesen Dienst, der [App-Geheimnisse](/azure/app-service/app-service-key-vault-references) bereitstellt. 
* App-Einstellungen: Wenn Sie eine einfachere Lösung suchen, können Sie Geheimnisse als App-Einstellungen bereitstellen und darauf mithilfe der typischen [Umgebungsvariablen](/azure/app-service/configure-language-nodejs?pivots=platform-windows) `process.env.VARNAME` verweisen. 

## <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung umfasst Folgendes:

* Plattformprotokollierung: Was geschieht außerhalb der App?
* App-Protokollierung: Was geschieht innerhalb der App?

Plattformprotokolle werden Ihnen zu folgenden Zwecken bereitgestellt:
* Nachvollziehen der Umgebungsintegrität
* Skalieren auf einen anderen Tarif oder regionsübergreifendes Skalieren 

Es werden keine Anwendungsprotokolle für Sie bereitgestellt. Sie können eigene Protokollierung für das interne App-Verhalten hinzufügen:
* [Azure Monitor](/azure/azure-monitor/overview) stellt npm-Bibliotheken für [Application Insights](/azure/azure-monitor/app/app-insights-overview) zur Protokollierung sowie die Speicherressource zur Archivierung der Protokolle zur Verfügung. 

## <a name="configure-database-and-storage"></a>Konfigurieren der Datenbank und des Speichers

Normalerweise beginnt eine Verbindung mit einer Datenbank oder einem Datenspeicher mit einer Verbindungszeichenfolge. 

Überlegungen zu Datenverbindungen:
* Verwenden der aktuellen Verbindung
* Neuer Datenspeicher: Ist für Ihre App ein neuer Speichermechanismus erforderlich, bietet Azure [zahlreiche verschiedene Datenbankoptionen](integrate-database.md). Die Verbindung muss sicher gespeichert werden. 

## <a name="missing-something"></a>Fehlt etwas? 

Wenn in dieser Liste etwas fehlt, füllen Sie das Feedback aus, um uns darüber zu informieren. 

## <a name="next-steps"></a>Nächste Schritte

* Viele dieser Schritte können Sie sich in einem [End-to-End-Entwicklungsablauf für Node. js-Apps](./develop-nodejs-on-azure.md) ansehen.