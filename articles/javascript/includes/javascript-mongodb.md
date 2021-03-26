---
ms.custom: devx-track-js
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: a16cdd0846485622d6f61a049c3dfc2d848812f5
ms.sourcegitcommit: 737d95fe31e9db55c2d42a93f194a3f3e4bd3c7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622288"
---
## <a name="install-mongodb-sdk"></a>Installieren des MongoDB SDK 

Gehen Sie wie unten angegeben vor, um mit JavaScript und MongoDB eine Verbindung mit Ihrer MongoDB-Instanz in Azure Cosmos DB herzustellen und diese zu verwenden.

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir DataDemo && \
        cd DataDemo && \
        npm init -y && \
        npm install mongodb &&
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `DataDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Installieren des SDK
    * Öffnen des Projekts in Visual Studio Code

## <a name="create-javascript-file-to-bulk-insert-data-into-mongodb-database"></a>Erstellen einer JavaScript-Datei zum Masseneinfügen von Daten in eine MongoDB-Datenbank

1. Erstellen Sie in Visual Studio Code die Datei `bulk_insert.js`.

1. Laden Sie die Datei [MOCK_DATA.csv](https://github.com/Azure-Samples/js-e2e/blob/main/database/redis/MOCK_DATA.csv) herunter, und fügen Sie sie in dasselbe Verzeichnis wie die Datei `bulk_insert.js` ein.

1. Kopieren Sie den folgenden JavaScript-Code in `bulk_insert.js`:

    :::code language="javascript" source="~/../js-e2e//database/mongodb/bulk_insert_mongodb.js" :::

1. Ersetzen Sie im Skript Folgendes durch Ihre Ressourceninformationen:

    * YOUR_RESOURCE_PRIMARY_CONNECTION_STRING

1. Führen Sie das Skript aus.

    ```bash
    node bulk_insert.js
    ```

## <a name="create-javascript-code-to-use-mongodb"></a>Erstellen von JavaScript-Code für die Verwendung von MongoDB

1. Erstellen Sie in Visual Studio Code die Datei `index.js`.

1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:

    :::code language="javascript" source="~/../js-e2e//database/mongodb/index_mongodb.js" :::

1. Ersetzen Sie im Skript Folgendes durch Ihre Ressourceninformationen:

    * YOUR_RESOURCE_PRIMARY_CONNECTION_STRING

1. Führen Sie das Skript aus.

    ```bash
    node index.js
    ```