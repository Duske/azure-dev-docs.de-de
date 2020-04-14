---
title: Verwenden einer Containerregistrierung in Visual Studio Code
description: 'Teil 2 des Tutorials: Verwenden einer Containerregistrierung'
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e6dde135a2e6482284488fb83d9f811b02249c4d
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740549"
---
# <a name="use-a-container-registry"></a>Verwenden einer Containerregistrierung

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-docker-node-01.md)

In diesem Schritt richten Sie eine geeignete Containerregistrierung für Ihr App-Image ein. Containerfähige Hostingdienste wie Azure App Service pullen dann die Images aus der Registrierung.

In diesem Tutorial wird [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR) verwendet, eine private, sichere, gehostete Registrierung für Ihre Images. Die hier gezeigten Tools und Prozesse funktionieren jedoch auch mit anderen Registrierungen wie [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

1. Drücken Sie in Visual Studio Code <kbd>F1</kbd>, um die **Befehlspalette** zu öffnen.

1. Geben Sie „registry“ in das Suchfeld ein, und wählen Sie **Azure Container Registry: Registrierung erstellen** aus.

   ![Docker-Explorer in VS Code](media/deploy-containers/docker-create-registry.jpg)

1. Geben Sie für die Eingabeaufforderungen die folgenden Werte ein:

    - Der **Registrierungsname** muss innerhalb von Azure eindeutig sein und aus 5 bis 50 alphanumerischen Zeichen bestehen.
    - Wählen Sie unter **SKU** die Option **Basic** aus.
    - Die **Ressourcengruppe** muss nur innerhalb Ihres Abonnements eindeutig sein.
    - Wählen Sie unter **Standort** eine Region in Ihrer Nähe aus.

    Visual Studio Code beginnt mit der Registrierungserstellung in Azure. Nach Abschluss der Erstellung wird eine Benachrichtigung wie die folgende angezeigt, um die erfolgreiche Erstellung der Registrierung zu bestätigen:

   ![Bestätigung der Registrierungserstellung in Visual Studio Code](media/deploy-containers/registry-created.jpg)

1. Öffnen Sie den **Docker-Explorer**, und vergewissern Sie sich, dass der Registrierungsendpunkt, den Sie gerade eingerichtet haben, unter **Registrierungen** angezeigt wird:

   ![Überprüfen, ob die Registrierung im Docker-Explorer angezeigt wird](media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Anmelden bei Azure Container Registry

Ihre Azure-Registrierungen werden zwar in der Docker-Erweiterung angezeigt, Images können jedoch erst nach der Anmeldung bei Azure Container Registry (ACR) dorthin gepusht werden.

1. Drücken Sie <kbd>STRG+`</kbd>, um das **integrierte Terminal** in VS Code zu öffnen.

1. Führen Sie den folgenden Azure CLI-Befehl aus, um sich bei ACR anzumelden. Ersetzen Sie „<your-registry-name>“ durch den Namen der Registrierung, die Sie eben erstellt haben.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Ich habe eine Registrierung erstellt.](tutorial-vscode-docker-node-03.md) [Es ist ein Problem aufgetreten.](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
