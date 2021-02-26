---
title: Wichtigste Aufgaben für JavaScript-Entwickler
description: Hier finden Sie ein Beispiel für Ihre aktuellen Aufgaben.
ms.topic: reference
ms.date: 01/20/2021
ms.custom: devx-track-js
ms.openlocfilehash: 9da8ede8dfbf7ffc54fce9531f4b490ae9db1942
ms.sourcegitcommit: 7287dff6bf4b30c2033924702c941bf520403e07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99589436"
---
# <a name="top-tasks-for-javascript-developers"></a>Wichtigste Aufgaben für JavaScript-Entwickler

Hier finden Sie ein Beispiel für Ihre aktuelle Aufgabe. Sollten Sie eine Aufgabe nicht finden, hinterlassen Sie Feedback, um Ihre Aufgabe anzufordern. 

## <a name="app-registration"></a>App-Registrierung

[Dokumentation zur App-Registrierung](/azure/active-directory/develop/quickstart-register-app)

|Aufgabe|using|
|--|--|
|Erstellen der App-Registrierung|[Portal](../tutorial/single-page-application-azure-login-button-sdk-msal.md#3-create-app-registration-for-authentication)|

## <a name="application-settings"></a>Anwendungseinstellungen

|Aufgabe|using|
|--|--|
|Festlegen von lokalen Umgebungsvariablen|[Bash](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#add-environment-variables-to-your-local-environment)|
|Erstellen von SAS-Token (Shared Access Signature) für Storage-Container|[Portal](../tutorial/browser-file-upload-azure-storage-blob.md#5-generate-your-shared-access-signature-sas-token)|
|Festlegen des SAS-Tokens im Code|[TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#set-sas-token-in-code-file)|
|Konfigurieren von CORS für Storage|[Portal](../tutorial/browser-file-upload-azure-storage-blob.md#6-configure-cors-for-azure-storage-resource)|

## <a name="authentication"></a>Authentifizierung

|Aufgabe|using|
|--|--|
|Schaltfläche zum Anmelden/Abmelden bei Microsoft mit `@azure/msal-browser`|[React/TypeScript](../tutorial/single-page-application-azure-login-button-sdk-msal.md#5-add-login-and-logoff-buttons)|
|Widerrufen der AAD-Berechtigung|[https://myapplications.microsoft.com/](https://myapplications.microsoft.com/)|
|Widerrufen der Consumerberechtigung|[https://account.live.com/consent/manage](https://account.live.com/consent/manage)|

## <a name="azure-login"></a>Azure-Anmeldung

|Aufgabe|using|
|--|--|
|Anmelden|[Azure-Befehlszeilenschnittstelle](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#2-sign-in-to-azure-cli)<br>[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#sign-in-to-azure)|


## <a name="azure-resource-groups"></a>Azure-Ressourcengruppen

|Aufgabe|using|
|--|--|
|Ressourcengruppe erstellen|[Azure-Befehlszeilenschnittstelle](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Ressourcengruppe löschen|[Azure-Befehlszeilenschnittstelle](../tutorial/static-web-app/clean-up-resources.md#remove-all-the-resources-by-removing-resource-group)|

## <a name="apps"></a>Apps-

### <a name="static-web-apps"></a>Statische Web-Apps

[Dienstdokumentation](/azure/static-web-apps/)

|Aufgabe|using|
|--|--|
|Erstellen einer Angular-App|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=angular)|
|Erstellen einer Deno-App|[Bash](../tutorial/deploy-deno-app-azure-app-service-azure-cli.md#3-create-local-deno-api-app)|
|Erstellen einer React-App für die JavaScript-Sprache|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=react)|
|Erstellen einer React-App für die TypeScript-Sprache|[Bash](../tutorial/single-page-application-azure-login-button-sdk-msal.md#4-create-react-single-page-application-for-typescript)|
|Erstellen einer Vue-App|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=vue)|
|Erstellen einer Svelte-App|[Bash](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-02.md?tabs=svelte)|
|Erstellen einer statischen Web-App|[Visual Studio Code-Erweiterung](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|Erstellen einer statischen, von Storage gehosteten App|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|Bereitstellen einer statischen, von Storage gehosteten App|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|
|Browsen zur Website|[Visual Studio Code-Erweiterung](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-azure-static-web-site-in-browser)|

### <a name="function-serverless-apps"></a>Funktions-App (serverlos)

[Dienstdokumentation](/azure/azure-functions/)

|Aufgabe|using|
|--|--|
|Lokales Erstellen einer Functions-App|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-serverless-node-create-local.md)|
|HTTP-Triggercode|[JavaScript](../tutorial/tutorial-vscode-serverless-node-create-local.md#http-function-javascript-template-code)|
|Lokales Debuggen/Testen einer Funktion|[Visual Studio Code](../tutorial/tutorial-vscode-serverless-node-test-local.md)|
|Bereitstellen einer Funktion in der Azure-Cloud|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md)|
|Überprüfen der Verfügbarkeit einer Funktion über eine öffentliche URL|[Visual Studio Code-Erweiterung/Browser](../tutorial/tutorial-vscode-serverless-node-deploy-hosting.md#verify-functions-app-is-publicly-available-with-browser)|
|Entfernen einer Funktions-App-Ressource|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-serverless-node-remove-resource.md)|

### <a name="app-service---full-stack-server-only-or-client-only-apps"></a>App Service: Full-Stack-App, reine Server-App oder reine Client-App

[Dienstdokumentation](/azure/app-service/)

|Aufgabe|using|
|--|--|
|Erstellen einer lokalen Express.js-App|[Bash](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#3-create-a-local-expressjs-app)|
|Erstellen einer App-Ressource (Bereitstellen einer Express.js-App und Streamen von Protokollen)|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#create-web-app-resource-and-deploy-expressjs-app)|
|Erstellen einer App-Ressource (Bereitstellen einer Express.js-App, Konfigurieren von App-Einstellungen, Ausführen von „npm install“ und Besuchen der bereitgestellten Website)|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#6-create-app-service-resource-in-visual-studio-code)|
|Erstellen einer App-Ressource|[Azure-Befehlszeilenschnittstelle](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|Erstellen einer App, Bereitstellen, Browser-App, Anzeigen von Protokollen|[Azure-Befehlszeilenschnittstelle](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-03.md)|
|Konfigurieren der Web-App für die Verwendung einer Datenbank-Verbindungszeichenfolge|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-mongodb-cosmosdb.md#configure-your-azure-web-app-with-the-connection-string)|
|Konfigurieren der Web-App für die Verwendung eines Containers|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-container-registry-resource.md#configure-web-app-to-use-container)|
|Konfigurieren des benutzerdefinierten Web-App-Domainnamens|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/configure-app-service-custom-domain-name.md#register-a-domain-name-with-your-azure-app)|
|Löschen einer App-Ressource|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#clean-up-resources)<br>[Azure-Befehlszeilenschnittstelle](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-07.md)|
|Bereitstellen oder erneutes Bereitstellen einer App|[Visual Studio Code-Erweiterung](deploy-web-app.md#deploy-or-redeploy-to-app-service-with-visual-studio-code)|
|Abrufen der externen IP-Adresse der Web-App|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/configure-app-service-custom-domain-name.md#register-a-domain-name-with-your-azure-app)|
|Kaufen eines Domänennamens und Konfigurieren des DNS-Eintrags|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/configure-app-service-custom-domain-name.md#purchase-a-domain-name-and-configure-dns-record)|
|Streamen von Remoteprotokollen|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#7-stream-remote-service-logs-in-visual-studio-code)<br>[Azure-Befehlszeilenschnittstelle](../tutorial/tutorial-vscode-azure-cli-node/tutorial-vscode-azure-cli-node-05.md)|

## <a name="cognitive-services"></a>Cognitive Services

[Dokumentation zu Dienstgruppen](/azure/cognitive-services/)

|Aufgabe|using|
|--|--|
|Erstellen einer Cognitive Services-_ComputerVision_-Ressource|[Azure-Befehlszeilenschnittstelle](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Abrufen einer Cognitive Services-_ComputerVision_-Ressource|[Azure-Befehlszeilenschnittstelle](../tutorial/static-web-app/create-computer-vision-resource-use-in-code.md#create-azure-resources)|
|Installieren des Azure SDK|[Bash](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-to-local-react-app)|
|Analysieren von Bildern mit [`@azure/cognitiveservices-computervision`](https://www.npmjs.com/package/@azure/cognitiveservices-computervision)|[Visual Studio Code](../tutorial/static-web-app/add-computer-vision-react-app.md#add-computer-vision-code-as-separate-module)|

## <a name="containers-including-docker-tasks"></a>Container umfassen Docker-Aufgaben

|Aufgabe|using|
|--|--|
|Hinzufügen von Docker-Dateien zu einem lokalen Projekt|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#add-docker-files)|
|Erstellen eines Docker-Images auf der Grundlage eines lokalen Projekts|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#build-a-docker-image)|
|Erstellen eines Containerimages aus Ihrem lokalen JavaScript-Projekt|[Visual Studio Code](./with-visual-studio-code/containerize-local-project.md#create-a-container)|
|Erstellen einer Containerregistrierungsressource|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-02.md#create-an-azure-container-registry)<br>[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-container-registry-resource.md#create-a-container-registry)|
|Erstelle das Dockerfile.|[Visual Studio Code-Erweiterung](./with-visual-studio-code/containerize-local-project.md#create-a-dockerfile-in-your-project)|
|Bereitstellen eines Images in App Service|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#deploy-image)|
|Aktivieren von Administratorzugriff auf die Registrierung|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-05.md#enable-admin-access-on-the-registry)|
|Abrufen der Anmeldeinformationen für die Azure-Container Registry|[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-container-registry-resource.md#get-container-registry-credentials)|
|Anmelden bei der Containerregistrierung|[BASH – Docker CLI](./with-azure-cli/create-container-registry-resource.md#login-to-container-registry-with-docker-cli)|
|Pushen eines Images in die Docker-Registrierungsressource|[Visual Studio Code-Erweiterung](./with-visual-studio-code/containerize-local-project.md#push-local-container-image-to-dockerhub)|
|Puschen eines Images an die Azure Container Registry-Ressource|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-04.md#push-the-image-to-a-registry)<BR>[BASH – Docker CLI](./with-azure-cli/create-container-registry-resource.md#push-your-local-image-to-your-container-registry)|
|Ausführen eines lokalen Containers|[Visual Studio Code-Erweiterung](with-visual-studio-code/containerize-local-project.md#build-image-from-your-project)|
|Kennzeichnen Ihres lokalen Images|[BASH – Docker CLI](./with-azure-cli/create-container-registry-resource.md#tag-your-local-image)|
|Überprüfen der Docker-Version|[Bash](../tutorial/tutorial-vscode-docker-node/tutorial-vscode-docker-node-01.md#verify-docker-install)|

## <a name="databases"></a>Datenbanken

### <a name="mariadb"></a>MariaDB

[Dienstdokumentation](/azure/mariadb/)

|Aufgabe|Verwenden|
|--|--|
|Erstellen einer MariaDB-Ressource|[Azure portal](https://ms.portal.azure.com/#create/Microsoft.MariaDBServer)<br>[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-mariadb.md#create-a-mariadb-resource-with-azure-cli)<br>[@azure/arm-mariadb](https://www.npmjs.com/package/@azure/arm-mariadb)|
|Erstellen einer MariaDB-Datenbank für eine Ressource|[Azure CLI](./with-azure-cli/create-mariadb.md#create-a-mariadb-resource-with-azure-cli)|
|Abrufen der Verbindungszeichenfolge|[Azure CLI](./with-azure-cli/create-mariadb.md#get-the-mariadb-connection-string-with-azure-cli)|
|Verwenden und Anzeigen der Datenbank|_mysql_-Befehlszeilenschnittstelle von [Azure Cloud Shell](https://shell.azure.com/)<br>[MySQL Workbench](https://www.mysql.com/products/workbench/)<br>[Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools-driver-mysql)<br>[npm mariadb](https://www.npmjs.com/package/mariadb)<br>[JavaScript](./with-database/use-mariadb.md#use-mariadb-sdk-to-connect-to-mariadb-on-azure)|

### <a name="mongodb-on-cosmos-db"></a>MongoDB in Cosmos DB

[Dienstdokumentation](/azure/cosmos-db/)

|Aufgabe|using|
|--|--|
|Erstellen einer Cosmos DB MongoDB-Ressource|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md)<br>[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-mongodb-cosmosdb.md#create-a-cosmos-db-resource-for-mongodb)|
|Abrufen der CosmosDB-Verbindungszeichenfolge|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md#get-cosmosdb-connection-string)<br>[Azure-Befehlszeilenschnittstelle](./with-azure-cli/create-mongodb-cosmosdb.md#get-the-mongodb-connection-string-for-your-resource)|
|Anzeigen von Cosmos DB|[Cosmos DB-Explorer](https://cosmos.azure.com/)|
|Verwenden der Mongoose-API für mongoDB in Cosmos DB|[JavaScript](./with-database/use-mongodb-as-cosmosdb.md#use-mongoose-sdk-to-connect-to-mongodb-on-azure)



## <a name="git"></a>Git

|Aufgabe|using|
|--|--|
|Initialisieren eines lokalen Git-Repositorys|[Visual Studio Code-Erweiterung](../tutorial/deploy-nodejs-azure-app-service-with-visual-studio-code.md?tabs=bash#5-initialize-git-in-visual-studio-code-for-current-app)|
|Erstellen eines lokalen Branch|[Visual Studio Code mit Befehlspalette](./with-visual-studio-code/clone-github-repository.md#create-a-branch-for-changes-with-git-cl)<br>[Visual Studio Code mit Statusleiste](./with-visual-studio-code/clone-github-repository.md#create-a-branch-from-status-bar)|
|Klonen eines Projekts von GitHub auf den lokalen Computer|[Visual Studio Code](with-visual-studio-code/install-run-debug-nodejs.md#clone-sample-project-to-local-computer)|
|Pushen eines lokalen Branch an ein Remoterepository|[Visual Studio Code mit Statusleiste](./with-visual-studio-code/clone-github-repository.md#push-a-local-branch-to-remote-from-status-bar)<br>[Visual Studio Code mit der Quellcodeverwaltungs-Erweiterung](./with-visual-studio-code/clone-github-repository.md#push-a-local-branch-to-remote-from-the-source-control-extension)|

## <a name="github"></a>GitHub 

### <a name="actions"></a>Aktionen 

|Aufgabe|using|
|--|--|
|Hinzufügen von Geheimnissen|[Visual Studio Code](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#create-a-static-web-app-resource)|
|Anzeigen des Buildprozesses|[GitHub-Website](../tutorial/static-web-app/create-static-web-app-visual-studio-code-extension.md#view-the-github-action-build-process)|


## <a name="monitoring"></a>Überwachung

|Aufgabe|using|
|--|--|
|Erstellen von Ressourcen|[Azure-Befehlszeilenschnittstelle](../tutorial/nodejs-virtual-machine-vm/create-azure-monitoring-application-insights-web-resource.md#create-azure-monitor-resource-with-azure-cli)|



## <a name="storage"></a>Storage

[Dienstdokumentation](/azure/storage/)

|Aufgabe|using|
|--|--|
|Erstellen von Ressourcen|[Visual Studio Code-Erweiterung](../tutorial/browser-file-upload-azure-storage-blob.md#3-create-storage-resource-with-visual-studio-extension)|
|Ressource löschen|[Visual Studio Code-Erweiterung](../tutorial/browser-file-upload-azure-storage-blob.md#clean-up-resources)|
|Erstellen einer statischen, von Storage gehosteten App|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-03.md)|
|Bereitstellen einer statischen, von Storage gehosteten App|[Visual Studio Code-Erweiterung](../tutorial/tutorial-vscode-static-website-node/tutorial-vscode-static-website-node-04.md)|

### <a name="blobs"></a>BLOBs

|Aufgabe|using|
|--|--|
|Erstellen eines Containers im Speicher mit [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#create-storage-client-and-manage-steps)|
|Hochladen der Datei in den Speicher mit [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#upload-button-functionality)|
|Auflisten von Dateien im Storage-Container mit [`@azure/storage-blob`](https://www.npmjs.com/package/@azure/storage-blob)|[React/TypeScript](../tutorial/browser-file-upload-azure-storage-blob.md#get-list-of-blobs)|

## <a name="terminal-usage"></a>Verwendung des Terminals

|Aufgabe|using|
|--|--|
|Integriertes Terminal|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#use-the-integrated-bash-terminal-to-install-dependencies)|

## <a name="virtual-machines"></a>Virtuelle Computer

[Dienstdokumentation](/azure/virtual-machines/)

|Aufgabe|using|
|--|--|
|Herstellen einer SSH-Verbindung mit dem virtuellen Computer|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#connect-with-ssh-and-change-web-app)|
|Installieren eines SDK für die Überwachung|[Bash](../tutorial/nodejs-virtual-machine-vm/connect-linux-virtual-machine-ssh.md#install-monitoring-sdk)|
|Hinzufügen von Überwachungscode zu einer Express.js-App|[JavaScript](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#edit-indexjs-for-logging-with-azure-monitor-application-insights)|
|Erstellen einer Datei vom Typ „cloud-init“|[YAML](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-cloud-init-file-to-expedite-linux-virtual-machine-creation)|
|Erstellen einer Linux-VM-Ressource|[Azure-Befehlszeilenschnittstelle](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#create-a-virtual-machine-resource)|
|Öffnen eines Ports eines virtuellen Linux-Computers|[Azure-Befehlszeilenschnittstelle](../tutorial/nodejs-virtual-machine-vm/create-linux-virtual-machine-azure-cli.md#open-port-for-virtual-machine)|
|Anzeigen von Protokollen|[Azure-Befehlszeilenschnittstelle](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-nodejs-expressjs-code.md#viewing-the-vm-logs-for-nginx-and-pm2)<br>[Portal](../tutorial/nodejs-virtual-machine-vm/azure-monitor-application-insights-logs.md#view-application-traces-in-azure-portal)|


## <a name="visual-studio-code-develop-and-debug-javascript-apps"></a>Visual Studio Code: Entwickeln und Debuggen von JavaScript-Apps 

[Tooldokumentation](https://code.visualstudio.com/docs)

|Aufgabe|using|
|--|--|
|Codevervollständigung|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#use-visual-studio-code-autocompletion-with-mongodb)|
|Debuggen der lokalen Node.js-App|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#debugging-the-local-nodejs-app)|
|Lokales Debuggen des vollständigen Stapels|[Visual Studio Code](with-visual-studio-code/install-run-debug-nodejs.md#local-full-stack-debugging-in-visual-studio-code)|
|Navigieren in den Projektdateien und dem Code|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#navigate-the-project-files-and-code)|
|Ausführen der lokalen Node.js-App|[Visual Studio Code](./with-visual-studio-code/install-run-debug-nodejs.md#running-the-local-nodejs-app)|

## <a name="samples-supporting-these-tasks"></a>Beispiele, in denen diese Aufgaben unterstützt werden

|Name | Beschreibung|
|--|--|
|React-App mit Cognitive Services|Hier erfahren Sie, wie Sie eine React-/TypeScript-Clientanwendung lokal erstellen und mit einer GitHub-Aktion für eine statische Azure-Web-App bereitstellen.<br>[Tutorial](../tutorial/static-web-app/introduction.md) - [Beispielcode](https://github.com/Azure-Samples/js-e2e-client-cognitive-services)|
|React-App zum Hochladen einer Datei in Azure Storage-Blobs|Bei diesem Beispielprojekt handelt es sich um eine Client-App für das TypeScript-/React-Framework (create-react-app) mit einem HTML-Formular zum Auswählen einer Datei, die in Azure Storage Blob-Instanzen hochgeladen werden soll.<br>[Tutorial](../tutorial/browser-file-upload-azure-storage-blob.md) - [Beispielcode](https://github.com/Azure-Samples/js-e2e-browser-file-upload-storage-blob)|
|React-App mit Anmeldeschaltfläche|Die in diesem Tutorial erstellte SPA ist eine React-App (create-react-app) mit den folgenden Vorgängen:<br>* Anmelden mit einem von Microsoft unterstützten Anmeldenamen, z. B. „Office 365“ oder „Outlook.com“<br>* Abmelden von der Anwendung<br>[Tutorial](../tutorial/single-page-application-azure-login-button-sdk-msal.md) - [Beispielcode](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)|
|Express.js-App mit MongoDB-Datenbank|In diesem Tutorial erfahren Sie, wie Sie das Projekt lokal mit VSCode unter Verwendung von Erweiterungen laden und ausführen und wie Sie den Code remote in einem App-Dienst ausführen. Das Tutorial umfasst das Erstellen einer CosmosDB-Ressource für die Mongo-API, das Abrufen der Verbindungsinformationen und das Festlegen dieser Informationen in der Konfiguration des App-Diensts, um eine Verbindung mit einer Clouddatenbank herzustellen.<br>[Tutorial](../tutorial/deploy-nodejs-mongodb-app-service-from-visual-studio-code.md) - [Beispielcode](https://github.com/Azure-Samples/js-e2e-express-mongo)|
|Mit Datei „cloud-init“ auf virtuellem Computer bereitgestellte Express.js-App|Erstellen Sie einen virtuellen Linux-Computer (virtual machine, VM) für eine Express.js-App. Der virtuelle Computer wird mit einer cloud-init-Konfigurationsdatei konfiguriert und enthält NGINX sowie ein GitHub-Repository für eine Express.js-App. Wenn der virtuelle Computer ausgeführt wird, können Sie eine SSH-Verbindung mit dem virtuellen Computer herstellen, die Web-App ändern, um sie mit Ablaufverfolgungsprotokollierung zu versehen, und die öffentliche Express.js-Server-App in einem Webbrowser anzeigen.<br>[Tutorial](../tutorial/nodejs-virtual-machine-vm/introduction.md) - [Beispielcode](https://github.com/Azure-Samples/js-e2e-express-mongo)|

Über den [Browser für Azure-Beispiele](/samples/browse/?languages=javascript%2cnodejs%2ctypescript) können Sie nach weiteren Beispielen für Ihren jeweiligen Anwendungsfall suchen. 

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Web-App](deploy-web-app.md)