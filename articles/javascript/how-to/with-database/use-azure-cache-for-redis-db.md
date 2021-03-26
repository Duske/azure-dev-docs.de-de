---
title: Verwenden von JavaScript mit Redis in Azure
description: Sie benötigen eine Azure Cache for Redis-Ressource, um eine Redis-Datenbank zu erstellen oder nach Azure zu verschieben.
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: cae27bea4326cb43e2f4c65590f9f2de46f02f7e
ms.sourcegitcommit: 737d95fe31e9db55c2d42a93f194a3f3e4bd3c7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622268"
---
# <a name="develop-a-javascript-application-with-azure-cache-for-redis"></a>Entwickeln einer JavaScript-Anwendung mit Azure Cache for Redis


Sie benötigen eine **Azure Cache for Redis**-Ressource, um eine Redis-Datenbank zu erstellen, zu verwenden oder nach Azure zu verschieben. Hier erfahren Sie, wie Sie die Ressource erstellen und Ihre Datenbank verwenden.

## <a name="create-a-resource-for-a-redis-database"></a>Erstellen einer Ressource für eine Redis-Datenbank

Zum Erstellen einer Ressource können Sie Folgendes verwenden:

* Azure CLI
* [Azure portal](https://ms.portal.azure.com/#create/Microsoft.Cache)

[!INCLUDE [Azure CLI commands](../../includes/azure-cli-cache-for-redis-db.md)]

## <a name="view-and-use-your-redis-database"></a>Anzeigen und Verwenden Ihrer Redis-Datenbank

Verwenden Sie bei der Entwicklung Ihrer Redis-Datenbank mit JavaScript die [Redis-Konsole](/azure/azure-cache-for-redis/cache-configure#redis-console) im Azure-Portal, um Ihre Datenbank zu nutzen.

:::image type="content" source="../../media/howto-database/azure-cache-for-redis-console-button.png" alt-text="Verwenden Sie bei der Entwicklung Ihrer Redis-Datenbank mit JavaScript die Redis-Konsole im Azure-Portal, um Ihre Datenbank zu nutzen.":::

Über diese Konsole ist die [Redis CLI](https://redis.io/topics/rediscli)-Funktionalität verfügbar. Beachten Sie hierbei, dass [einige Befehle nicht unterstützt werden](/azure/azure-cache-for-redis/cache-configure#redis-commands-not-supported-in-azure-cache-for-redis).

Führen Sie nach der Erstellung Ihrer Ressource über das Azure-Portal den [Importvorgang für Ihre Daten](/azure/azure-cache-for-redis/cache-how-to-import-export-data) in Ihre Redis-Ressource aus Azure Storage durch. 

## <a name="use-native-sdk-packages-to-connect-to-redis-on-azure"></a>Verwenden von nativen SDK-Paketen zum Herstellen einer Verbindung mit Redis in Azure

Für die Redis-Datenbank werden npm-Pakete verwendet, z. B.:

* [redis](https://www.npmjs.com/package/redis)
* [ioredis](https://www.npmjs.com/package/ioredis)

## <a name="install-ioredis-sdk"></a>Installieren des ioredis SDK 

Verwenden Sie das folgende Verfahren, um das Paket `ioredis` zu installieren und Ihr Projekt zu initialisieren.

1. Stellen Sie sicher, dass Node.js und npm installiert sind.
1. Erstellen Sie ein Node.js-Projekt in einem neuen Ordner:

    ```bash
    mkdir DataDemo && \
        cd DataDemo && \
        npm init -y && \
        npm install ioredis \
        code .
    ```

    Mit diesem Befehl werden folgende Schritte ausgeführt:
    * Erstellen eines Projektordners mit dem Namen `DataDemo`
    * Ändern des Bash-Terminals in diesen Ordner
    * Initialisieren des Projekts, wodurch die Datei `package.json` erstellt wird
    * Hinzufügen des ioredis npm SDK zum Projekt
    * Öffnen des Projekts in Visual Studio Code

## <a name="create-javascript-file-to-bulk-insert-data-into-redis"></a>Erstellen einer JavaScript-Datei zum Masseneinfügen von Daten in Redis

1. Erstellen Sie in Visual Studio Code die Datei `bulk_insert.js`.

1. Laden Sie die Datei [MOCK_DATA.csv](https://github.com/Azure-Samples/js-e2e/blob/main/database/redis/MOCK_DATA.csv) herunter, und fügen Sie sie in dasselbe Verzeichnis wie die Datei `bulk_insert.js` ein.

1. Kopieren Sie den folgenden JavaScript-Code in `bulk_insert.js`:

    ```nodejs
    const Redis = require('ioredis');
    const fs = require('fs');
    const parse = require('csv-parser')
    const { finished } = require('stream/promises');
    
    const config = {
        "HOST": "YOUR-RESOURCE-NAME.redis.cache.windows.net",
        "KEY": "YOUR-RESOURCE-PASSWORD",
    }
    
    // Create Redis config object
    const configuration = {
        host: config.HOST,
        port: 6380,
        password: config.KEY,
        tls: {
            servername: config.HOST
        },
        database: 0,
        keyPrefix: config.prefix
    }
    var redis = new Redis(configuration);
    
    // insert each row into Redis
    async function insertData(readable) {
        for await (const row of readable) {
            await redis.set(`bar2:${row.id}`, JSON.stringify(row))
        }
    }
    
    // read file, parse CSV, each row is a chunk
    const readable = fs
        .createReadStream('./MOCK_DATA.csv')
        .pipe(parse());
    
    // Pipe rows to insert function
    insertData(readable)
    .then(() => {
        console.log('succeeded');
        redis.disconnect();
    })
    .catch(console.error);
    ```

1. Ersetzen Sie im Skript Folgendes durch die Informationen für Ihre Redis-Ressource:

    * YOUR-RESOURCE-NAME
    * YOUR-AZURE-REDIS-RESOURCE-KEY

1. Führen Sie das Skript aus.

    ```bash
    node bulk_insert.js
    ```
    
## <a name="create-javascript-code-to-use-redis"></a>Erstellen von JavaScript-Code für die Verwendung von Redis

1. Erstellen Sie in Visual Studio Code die Datei `index.js`.


1. Kopieren Sie den folgenden JavaScript-Code in `index.js`:

    ```nodejs
    const redis = require('ioredis');
    
    const config = {
        "HOST": "YOUR-RESOURCE-NAME.redis.cache.windows.net",
        "KEY": "YOUR-RESOURCE-PASSWORD",
        "TIMEOUT": 300,
        "KEY_PREFIX": "demoExample:"
    }
    
    // Create Redis config object
    const configuration = {
        host: config.HOST,
        port: 6380,
        password: config.KEY,
        timeout: config.TIMEOUT,
        tls: {
            servername: config.HOST
        },
        database: 0,
        keyPrefix: config.KEY_PREFIX
    }
    
    const connect = () => {
        return redis.createClient(configuration);
    }
    
    const set = async (client, key, expiresInSeconds=configuration.timeout, stringify=true, data) => {
        return await client.setex(key, expiresInSeconds, stringify? JSON.stringify(data): data);
    }
    
    const get = async (client, key, stringParse=true) => {
        const value = await client.get(key);
        return stringParse ? JSON.parse(value) : value;
    }
    
    const remove = async (client, key) => {
          return await client.del(key);
    }
    
    const disconnect = (client) => {
        client.disconnect();
    }
    
    const test = async () => {
        
        // connect
        const dbConnection = await connect();
        
        // set
        const setResult1 = await set(dbConnection, "r1", "1000000", false, "record 1");
        const setResult2 = await set(dbConnection, "r2", "1000000", false, "record 2");
        const setResult3 = await set(dbConnection, "r3", "1000000", false, "record 3");
    
        // get
        const val2 = await get(dbConnection, "r2", false);
        console.log(val2);
        
        // delete
        const remove2 = await remove(dbConnection, "r2");
        
        // get again = won't be there
        const val2Again = await get(dbConnection, "r2", false);
        console.log(val2Again);
        
        // done
        disconnect(dbConnection)
    }
    
    test()
    .then(() => console.log("done"))
    .catch(err => console.log(err))
    ```
 
1. Ersetzen Sie im Skript Folgendes durch die Informationen für Ihre Redis-Ressource:

    * YOUR-RESOURCE-NAME
    * YOUR-RESOURCE-PASSWORD

1. Führen Sie das Skript aus.

    ```bash
    node index.js
    ```
    
    Vom Skript werden drei Schlüssel eingefügt, und dann wird der mittlere Schlüssel gelöscht. Die Konsolenergebnisse lauten wie folgt:

    ```console
    record 2
    null
    done
    ```

## <a name="use-redis-console-in-azure-portal-to-view-data"></a>Verwenden der Redis-Konsole im Azure-Portal zum Anzeigen von Daten

Zeigen Sie im Azure-Portal die Konsole für Ihre Ressource mit dem folgenden Befehl an: `SCAN 0 COUNT 1000 MATCH *`. 

:::image type="content" source="../../media/howto-database/azure-cache-for-redis-azure-portal-console-scan.png" alt-text="Zeigen Sie im Azure-Portal die Konsole für Ihre Ressource mit dem folgenden Befehl an: SCAN 0 COUNT 1000 MATCH *":::

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer JavaScript-Web-App](../deploy-web-app.md)
* [Dokumentation zu Azure Cache for Redis](/azure/azure-cache-for-redis)
* [Schnellstart: Verwenden von Azure Cache for Redis mit Node.js](/azure/azure-cache-for-redis/cache-nodejs-get-started)
* [Azure Architecture Center: Bewährte Methoden für Caching](/azure/architecture/best-practices/caching)
* [Bewährte Methoden für Azure Cache for Redis](/azure/azure-cache-for-redis/cache-best-practices#client-library-specific-guidance)