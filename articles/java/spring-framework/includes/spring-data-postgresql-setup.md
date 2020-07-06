---
author: judubois
ms.date: 05/18/2020
ms.author: judubois
ms.openlocfilehash: 32444e17932c2b86c58d1ac44ab95ce9bd1fa49c
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507496"
---
## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Richten Sie zunächst mithilfe der folgenden Befehle einige Umgebungsvariablen ein:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=spring
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name des PostgreSQL-Servers. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<YOUR_POSTGRESQL_PASSWORD>`: Das Kennwort Ihres PostgreSQL-Datenbankservers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Spring Boot-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie nun mithilfe des folgenden Befehls eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Wir verwenden das Hilfsprogramm `jq`, um JSON-Daten anzuzeigen und besser lesbar zu machen. Dieses Hilfsprogramm wird standardmäßig unter [Azure Cloud Shell](https://shell.azure.com/) installiert. Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Erstellen einer Azure Database for PostgreSQL-Instanz

Sie erstellen zuerst einen verwalteten PostgreSQL-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von PostgreSQL-Servern finden Sie unter [Erstellen eines Azure Database for PostgreSQL-Servers im Azure-Portal](/azure/postgresql/quickstart-create-server-database-portal).

Führen Sie in [Azure Cloud Shell](https://shell.azure.com/) den folgenden Befehl aus:

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
    | jq
```

Mit diesem Befehl wird ein kleiner PostgreSQL-Server erstellt.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>Konfigurieren einer Firewallregel für den PostgreSQL-Server

Azure Database for PostgreSQL-Instanzen sind standardmäßig gesichert. Sie besitzen eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie den folgenden Befehl ausführen:

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-postgresql-database"></a>Konfigurieren einer PostgreSQL-Datenbank

Der von Ihnen zuvor erstellte PostgreSQL-Server ist leer. Er verfügt über keine Datenbank, die Sie mit der Spring Boot-Anwendung verwenden können. Erstellen Sie mit dem folgenden Befehl eine neue Datenbank namens `demo`:

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
    | jq
```
