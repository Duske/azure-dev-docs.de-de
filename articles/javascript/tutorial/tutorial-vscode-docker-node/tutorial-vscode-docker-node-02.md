---
title: Verwenden einer Containerregistrierung in Visual Studio Code
description: 'Teil 2 des Docker-Tutorials: Verwenden einer Containerregistrierung'
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: devx-track-js
ms.openlocfilehash: d790e36163a7e654e32c8d5a675d3f7abfad8a99
ms.sourcegitcommit: f723980ade4cbc13548a5d8ac3f3fa681b8a2dbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97609230"
---
# <a name="use-a-container-registry"></a>Verwenden einer Containerregistrierung

[Vorheriger Schritt: Einführung und Voraussetzungen](tutorial-vscode-docker-node-01.md)

In diesem Schritt richten Sie eine geeignete Containerregistrierung für Ihr App-Image ein. Containerfähige Hostdienste wie Azure App Service können dann Images per Pull aus der Registrierung abrufen.

In diesem Tutorial wird [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) verwendet, eine private, sichere, gehostete Registrierung für Ihre Images. Die hier gezeigten Tools und Prozesse funktionieren jedoch auch mit anderen Registrierungen wie [Docker Hub](https://hub.docker.com/).

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

Erstellen Sie eine [Ressource im Free-Tarif](../../core/what-is-azure-for-javascript-development.md#free-tier-resources).

1. Drücken Sie in Visual Studio Code **F1** oder **STRG+UMSCHALT+P**, um die Befehlspalette zu öffnen.

1. Geben Sie im Suchfeld **Registrierung** ein. Wählen Sie in den Ergebnissen **Azure Container Registry: Registrierung erstellen** aus.

   ![Der Docker-Explorer in Visual Studio Code](../../media/deploy-containers/docker-create-registry.jpg)

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

    - Geben Sie in **Registrierungsname** einen Namen ein, der in Azure eindeutig ist und von 5 bis 50 alphanumerische Zeichen enthält.
    - Wählen Sie in **SKU** **Basic** aus.
    - Geben Sie als **Ressourcengruppe** einen Wert ein, der innerhalb Ihres Abonnements eindeutig ist.
    - Wählen Sie unter **Standort** eine Region in Ihrer Nähe aus.

    Visual Studio Code beginnt mit dem Erstellungsprozess der Registrierung in Azure. Nach dem Abschluss des Prozesses wird eine Benachrichtigung wie die folgende angezeigt. Diese Benachrichtigung bestätigt, dass die Registrierung erfolgreich erstellt wurde.

   ![Bestätigung in Visual Studio Code, dass die Registrierung erstellt wurde](../../media/deploy-containers/registry-created.jpg)

1. Öffnen Sie den **Docker**-Explorer. Vergewissern Sie sich, dass der Registrierungsendpunkt, den Sie soeben eingerichtet haben, unter **Registrierungen** angezeigt wird.

   ![Überprüfung, ob die Registrierung im Docker-Explorer angezeigt wird](../../media/deploy-containers/docker-explorer-registry.jpg)

## <a name="sign-in-to-azure-container-registry"></a>Anmelden bei Azure Container Registry

Ihre Azure-Registrierungen werden zwar in der Docker-Erweiterung angezeigt, Images können jedoch erst nach der Anmeldung bei Container Registry dorthin gepusht werden.

1. Wählen Sie in Visual Studio **STRG**+ **`** aus, um das integrierte Terminal zu öffnen.

1. Führen Sie den folgenden Befehl an der Azure-Befehlszeilenschnittstelle aus, um sich bei Container Registry anzumelden. Ersetzen Sie `<your-registry-name>` durch den Namen der Registrierung, die Sie zuvor erstellt haben.

    ```bash
    az acr login --name <your-registry-name>
    ```

> [!div class="nextstepaction"]
> [Ich habe eine Registrierung erstellt](tutorial-vscode-docker-node-03.md) [Es ist ein Problem aufgetreten](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=create-registry)
 
