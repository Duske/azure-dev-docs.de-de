---
title: Erstellen einer benutzerdefinierten Containerregistrierung
description: Eine Containerregistrierung eignet sich ideal für Containerimages, die Sie in Azure bereitstellen möchten. Die Registrierung erlaubt es Ihnen, Containerrepositorys und Versionen zu verwalten.
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: d60ea6d907a628d9b68c4c44ae72c1adca65ba29
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230141"
---
# <a name="create-and-use-container-registry"></a>Erstellen und Verwenden einer Containerregistrierung

Eine Containerregistrierung eignet sich ideal für Containerimages, die Sie in Azure bereitstellen möchten.

Die Registrierung erlaubt es Ihnen, Containerrepositorys und Versionen zu verwalten.  

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Erstellen Sie eine Registrierung mit einem Ressourcennamen. Der Ressourcenname wird Teil des Anmeldeservernamens für Ihre Ressource. 

Verwenden Sie den Befehl [az acr create](/cli/azure/acr#az_acr_create), um eine Registrierung zu erstellen. 

```azurecli
az acr create \
    --resource-group YOUR-RESOURCE-GROUP
    --name YOUR-REGISTRY-NAME 
    --location westus 
    --admin-enabled
    --sku Basic
    --public-network-enabled false
```

## <a name="get-container-registry-credentials"></a>Abrufen von Anmeldeinformationen für die Containerregistrierung

Führen Sie zum Abrufen von Anmeldeinformationen den Befehl [az acr credential show](/cli/azure/acr/credential#az_acr_credential_show) aus, und notieren Sie sich den angezeigten Benutzernamen und das Kennwort:

```azurecli
az acr credential show \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-REGISTRY-NAME
```

## <a name="login-to-container-registry-with-docker-cli"></a>Anmelden bei der Containerregistrierung mit Docker CLI

Mit den Anmeldeinformationen aus dem vorherigen Schritt und Ihrem individuellen Anmeldeserver können Sie sich an der Registrierung anmelden, indem Sie den Docker CLI-Standardworkflow verwenden.

```bash
docker login YOUR-LOGIN_SERVER \
    --username USERNAME
    --password PASSWORD
```

## <a name="tag-your-local-image"></a>Kennzeichnen Ihres lokalen Images

Sie können Ihren Docker-Container jetzt mit einem Tag versehen, um anzugeben, dass er Ihrer privaten Registrierung zugeordnet ist. Verwenden Sie hierfür den folgenden Befehl, und ersetzen Sie `YOURALIAS/IMAGENAME` durch den Namen, den Sie dem Containerimage gegeben haben.

```bash
docker tag YOURALIAS/IMAGENAME \
    YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="push-your-local-image-to-your-container-registry"></a>Pushen Ihres lokalen Images in Ihre Containerregistrierung

```bash
docker push YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1
```

## <a name="configure-web-app-to-use-container"></a>Konfigurieren der Web-App für die Verwendung eines Containers 

Wenn Sie die App Service Web-App zum Abrufen des Images aus Ihrer Registrierung konfigurieren, führen Sie den folgenden [az appservice web config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)-Befehl aus:

```azurecli
az appservice web config container set \
    --resource-group YOUR-RESOURCE-GROUP \
    --name YOUR-WEBAPP-NAME
    --docker-registry-server-url YOUR-LOGIN_SERVER \
    --docker-custom-image-name YOUR-LOGIN_SERVER/YOURALIAS/IMAGENAME:v1 \
    -u USERNAME \
    -p PASSWORD
```

Fügen Sie am Anfang der Option `--docker-registry-server-url` das Präfix `https://` hinzu. Achten Sie aber darauf, dass Sie das Präfix nicht dem Namen des Containerimage hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer CosmosDB-Ressource für mongoDB](create-mongodb-cosmosdb.md)