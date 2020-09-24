---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: f2efcb620b570c57c3907ba2a682c65078e84790
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738514"
---
### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Erstellen und Pushen des Docker-Images für Azure Container Registry

Nachdem Sie das Dockerfile erstellt haben, müssen Sie das Docker-Image erstellen und in Ihrer Azure Container Registry-Instanz veröffentlichen.

Wenn Sie unser [GitHub-Schnellstartrepository für WildFly-Container](https://github.com/Azure/wildfly-container-quickstart) verwendet haben, entspricht der Prozess zum Erstellen und Pushen Ihres Images für Ihre Azure Container Registry-Instanz dem Aufrufen der folgenden drei Befehle.

In diesen Beispielen enthält die Umgebungsvariable `MY_ACR` den Namen Ihrer Azure Container Registry-Instanz und die Variable `MY_APP_NAME` den Namen der Webanwendung, die Sie in Ihrer Azure Container Registry-Instanz verwenden möchten.

Erstellen der WAR-Datei:

```shell
mvn package
```

Anmelden bei Ihrer Azure Container Registry-Instanz:

```shell
az acr login -n ${MY_ACR}
```

Erstellen und Pushen des Images:

```shell
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} -f src/main/docker/Dockerfile .
```

Alternativ können Sie die Docker CLI verwenden, um das Image zuerst lokal zu erstellen und zu testen. Dies ist in den folgenden Befehlen veranschaulicht. Dieser Ansatz kann das Testen und Optimieren des Images vor der ersten Bereitstellung unter ACR vereinfachen. Hierfür ist es aber erforderlich, die Docker CLI zu installieren und sicherzustellen, dass der Docker-Daemon ausgeführt wird.

Erstellen des Images:

```shell
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Lokales Ausführen des Images:

```shell
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Sie können jetzt unter `http://localhost:8080` auf Ihre Anwendung zugreifen.

Anmelden bei Ihrer Azure Container Registry-Instanz:

```shell
az acr login -n ${MY_ACR}
```

Pushen des Images an Ihre Azure Container Registry-Instanz:

```shell
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Ausführlichere Informationen zum Erstellen und Speichern von Containerimages in Azure finden Sie im Lernmodul [Erstellen und Speichern von Containerimages mit Azure Container Registry](/learn/modules/build-and-store-container-images/).
