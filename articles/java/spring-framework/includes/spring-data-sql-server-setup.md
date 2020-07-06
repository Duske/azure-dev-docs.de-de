---
author: judubois
ms.date: 05/18/2020
ms.author: judubois
ms.openlocfilehash: b1dd65024158f622d6b202dc2ad83a31f8d98296
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507702"
---
## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Richten Sie zunächst mithilfe der folgenden Befehle einige Umgebungsvariablen ein:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name für den Azure SQL-Datenbank-Server. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<AZ_SQL_SERVER_PASSWORD>`: Das Kennwort Ihres Azure SQL-Datenbank-Servers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Spring Boot-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie dann mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Wir verwenden das Hilfsprogramm `jq`, um JSON-Daten anzuzeigen und besser lesbar zu machen. Dieses Hilfsprogramm wird standardmäßig unter [Azure Cloud Shell](https://shell.azure.com/) installiert. Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-sql-database-instance"></a>Erstellen einer Azure SQL-Datenbankinstanz

Sie erstellen zuerst einen verwalteten Azure SQL-Datenbank-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von Azure SQL-Datenbank-Servern finden Sie unter [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](/azure/sql-database/sql-database-single-database-get-started).

Führen Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl aus:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

Mit diesem Befehl wird ein Azure SQL-Datenbank-Server erstellt.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Konfigurieren einer Firewallregel für den Azure SQL-Datenbank-Server

Azure SQL-Datenbankinstanzen sind standardmäßig gesichert. Sie besitzen eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie den folgenden Befehl ausführen:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Konfigurieren einer Azure SQL-Datenbank

Der von Ihnen zuvor erstellte Azure SQL-Datenbank-Server ist leer. Er verfügt über keine Datenbank, die Sie mit der Spring Boot-Anwendung verwenden können. Erstellen Sie durch Ausführen des folgenden Befehls eine neue Datenbank namens `demo`:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```
