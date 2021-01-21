---
title: Web-App-Automatisierungsaufgaben mit der Azure CLI
description: Die Automatisierung von Azure-Aufgaben ist eine gängige Anforderung für Continuous Deployment in Hostingumgebungen. Für JavaScript-Entwickler, die Aufgaben verwalten und die Bereitstellung an einem beliebigen Standort durchführen, wird die Azure CLI empfohlen.
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: c2df54b23baab71cbdedb03fe5990f53dd0c807a
ms.sourcegitcommit: 593d177cfb5f56f236ea59389e43a984da30f104
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2021
ms.locfileid: "98561136"
---
# <a name="automate-tasks-with-azure-cli"></a>Automatisieren von Aufgaben mit der Azure CLI

Die Automatisierung von Azure-Aufgaben ist eine gängige Anforderung für Continuous Deployment in Hostingumgebungen. Für JavaScript-Entwickler, die Aufgaben verwalten und die Bereitstellung an einem beliebigen Standort durchführen, wird die [Azure CLI](/cli/azure/) empfohlen.

Machen Sie sich mit den allgemeinen Aufgabenbefehlen für JavaScript-Entwickler vertraut. 

## <a name="automation-with-azure-cli"></a>Automatisierung mit der Azure CLI

Zum Automatisieren der Azure CLI muss die CLI in der Umgebung installiert sein. Zu den gängigen Methoden zählen folgende: 

* [Lokale Installation der Azure CLI](/cli/azure/install-azure-cli)
* [Befehlsausführung über den Docker-Container](/cli/azure/run-azure-cli-docker)

## <a name="using-the-example-commands"></a>Verwenden der Beispielbefehle 

1. Ersetzen Sie Variablen in Klammern (`<...>`) durch Ihre eigenen Werte. 
1. Der Wert Ihres GitHub-Repositorys für `<MY_GITHUB_DEFAULT_BRANCH_NAME>` ist spezifisch für das verwendete Repository. Derzeit sind die typischen Werte `main` oder `default`. Für ältere Repositorys wird unter Umständen `master` verwendet. 

## <a name="log-in-for-automated-tasks-with-azure-cli"></a>Anmelden für automatisierte Aufgaben mit der Azure CLI

Nach der Installation der Azure CLI müssen Sie sich anmelden, um die Azure CLI-Befehle weiterhin auszuführen. Für die Automatisierung können Sie sich bei der Azure CLI authentifizieren.

**Referenzdokumentation:** [az login](/cli/azure/reference-index#az-login)

Für die Authentifizierung wird die Option [Verwaltete Identität](/cli/azure/authenticate-azure-cli#sign-in-with-a-managed-identity) empfohlen.

```azurecli
az login --identity
```

Nach der [Erstellung des Dienstprinzipals](../core/node-sdk-azure-authenticate-principal.md#create-a-service-principal-using-the-azure-cli-20) müssen Sie sich [mit dem Dienstprinzipal eines Benutzers anmelden](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal). 

```dotnetcli
read -sp "Azure password: " AZ_PASS && echo && \ 
    az login --service-principal \
    -u <MY-SP-APP-URL> \
    -p $AZ_PASS \
    --tenant <MY-TENANT>
```


[Mit Benutzeranmeldeinformationen: Benutzername und Kennwort](/cli/azure/authenticate-azure-cli#sign-in-with-credentials-on-the-command-line)

```dotnetcli
az login -u <MY_AZURE_USERNAME> -p <MY_AZURE_PASSWORD>
```    

## <a name="create-resource-group-for-resources"></a>Erstellen einer Ressourcengruppe für Ressourcen

Eine Ressourcengruppe ist eine logische Sammlung Ihrer Azure-Ressourcen. Die logische Gruppierung basiert auf Diensten, die Sie in einer bestimmten Region für ein Projekt benötigen. Erfahren Sie mehr über [Namenskonventionen](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming).

**Referenzdokumentation:** [az group create](/cli/azure/group#az_group_create)

```azurecli
az group create \
    --name <MY-AZURE-RESOURCE_GROUP_NAME> \
    --location <AZURE_REGION_LOCATION>
```

## <a name="static-web-apps-with-azure-cli"></a>Statische Web-Apps mit der Azure CLI

Eine statische Web-App enthält Code für Folgendes:

* eine in einem GitHub-Repository enthaltene Front-End-Anwendung
* optional eine vorhandene Azure Functions-API im Verzeichnis `/API` ([weitere Informationen](/azure/static-web-apps/add-api#create-the-api))

Die App kann Azure-Funktionen für serverlose APIs verwenden, dies ist jedoch keine Voraussetzung für statische Web-Apps. 

**Referenzdokumentation:** [az staticwebapp](/cli/azure/staticwebapp)

### <a name="create-azure-static-web-app"></a>Erstellen der statischen Azure-Web-App 

**Referenzdokumentation:** [az staticwebapp create](/cli/azure/staticwebapp#az_staticwebapp_create)

```azurecli
az staticwebapp create \
    --name <MY_AZURE_WEB_APP_NAME> \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME> \
    --source https://github.com/<MY_GITHUB_ACCOUNT_NAME>/<MY_AZURE_WEB_APP_NAME> \
    --location <AZURE_REGION_LOCATION> \
    --branch <MY_GITHUB_DEFAULT_BRANCH_NAME> \
    --app-artifact-location "<MY_WEB_APP_BUILD_DIRECTORY_NAME>" \
    --token <MY_GITHUB_PERSONAL_ACCESS_TOKEN>
```

### <a name="deploy-azure-static-web-app"></a>Bereitstellen einer statischen Azure-Web-App 

Zum Bereitstellen der App führen Sie mit Git die Übertragung per Push an den Remote- und Branchsatz während der Ressourcenerstellung im vorherigen Satz aus. 

```bash
git push <REMOTE_NAME> <MY_GITHUB_DEFAULT_BRANCH_NAME>
```

Ein Beispiel für diesen Befehl ist:

```bash
git push origin main
```

### <a name="delete-static-web-app"></a>Löschen einer statischen Web-App 

**Referenzdokumentation:** [az staticwebapp delete](/cli/azure/staticwebapp#az_staticwebapp_delete)

```azurecli
az staticwebapp delete && \
    --name <MY_AZURE_WEB_APP_NAME> && \
    --resource-group <MY-AZURE-RESOURCE_GROUP_NAME>
```

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erstellen und Bereitstellen einer statischen Web-App in Azure](../tutorial/static-web-app/introduction.md)
