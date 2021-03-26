---
ms.custom: devx-track-js
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 6121b17a6155799c5a6d4d43e5870f7613ab2f0b
ms.sourcegitcommit: 737d95fe31e9db55c2d42a93f194a3f3e4bd3c7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622286"
---
## <a name="install-mongoose-sdk"></a>Installieren des mongoose SDK 

Gehen Sie wie folgt vor, um mit JavaScript und mongoose eine Verbindung mit Ihrer MongoDB-Instanz in Azure Cosmos DB herzustellen und diese zu verwenden:

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir DataDemo && \
        cd DataDemo && \
        npm init -y && \
        npm install mongoose &&
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `DataDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Installieren des SDK
    * Öffnen des Projekts in Visual Studio Code

## <a name="use-mongoose-sdk-to-connect-to-mongodb-on-azure"></a>Verwenden des mongoose SDK zum Herstellen einer Verbindung mit MongoDB in Azure

1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:
1. 
    :::code language="javascript" source="~/../js-e2e//database/mongodb/index_mongoose.js" :::
 
1. Ersetzen Sie `YOUR-CONNECTION-STRING` im Skript durch die Verbindungszeichenfolge Ihrer Ressource. 
1. Führen Sie das Skript aus.

    ```bash
    node index.js
    ```

    Die Ergebnisse sind:

    ```console
    find all
    loop {"_id":"6019a68a6ecddc35d536c92c","name":"Joan Smith","job":"Developer","__v":0}
    loop {"_id":"6019a68e6ecddc35d536c92d","name":"Bob Jones","job":"Quality Assurance","__v":0}
    loop {"_id":"6019a6916ecddc35d536c92e","name":"Michelle Roberts","job":"Program Manager","__v":0}
    succeeded
    ```