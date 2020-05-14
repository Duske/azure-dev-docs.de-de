---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: bdd2d2f1eacbe3dd5f12a236f691d32e7a29fb68
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369908"
---
## <a name="prepare-the-working-environment"></a>Vorbereiten der Arbeitsumgebung

Richten Sie zunächst mithilfe der folgenden Befehle einige Umgebungsvariablen ein:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=spring
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Ersetzen Sie die Platzhalter durch die folgenden Werte, die in diesem Artikel verwendet werden:

- `<YOUR_DATABASE_NAME>`: Der Name Ihres MySQL-Servers. Er muss innerhalb von Azure eindeutig sein.
- `<YOUR_AZURE_REGION>`: Die von Ihnen verwendete Azure-Region. Sie können standardmäßig `eastus` verwenden, wir empfehlen aber, eine Region zu konfigurieren, die näher an Ihrem Standort liegt. Die vollständige Liste der verfügbaren Regionen wird angezeigt, wenn Sie `az account list-locations` eingeben.
- `<YOUR_MYSQL_PASSWORD>`: Das Kennwort Ihres MySQL-Datenbankservers. Dieses Kennwort sollte mindestens acht Zeichen lang sein. Es muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Die IP-Adresse Ihres lokalen Computers, auf dem Sie die Spring Boot-Anwendung ausführen. Sie können sie ganz einfach ermitteln, indem Sie im Browser zu [whatismyip.akamai.com](http://whatismyip.akamai.com/) navigieren.

Erstellen Sie als Nächstes eine Ressourcengruppe:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Wir verwenden das in [Azure Cloud Shell](https://shell.azure.com/) standardmäßig installierte Dienstprogramm `jq`, um JSON-Daten anzuzeigen und ihre Lesbarkeit zu erhöhen.
> Wenn Ihnen dieses Hilfsprogramm nicht gefällt, können Sie den Teil `| jq` aller verwendeten Befehle einfach entfernen.

## <a name="create-an-azure-database-for-mysql-instance"></a>Erstellen einer Instanz von Azure-Datenbank für MySQL

Das erste, was wir erstellen, ist ein verwalteter MySQL-Server.

> [!NOTE]
> Ausführlichere Informationen zum Erstellen von MySQL-Servern finden Sie unter [Erstellen eines Azure Database for MySQL-Servers im Azure-Portal](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

Führen Sie in der [Azure Cloud Shell](https://shell.azure.com/) das folgende Skript aus:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    | jq
```

Mit diesem Befehl wird ein kleiner MySQL-Server erstellt.

### <a name="configure-a-firewall-rule-for-your-mysql-server"></a>Konfigurieren einer Firewallregel für den MySQL-Server

Azure Database for MySQL-Instanzen sind standardmäßig gesichert. Sie besitzen eine Firewall, die keine eingehenden Verbindungen zulässt. Damit Sie die Datenbank verwenden können, müssen Sie eine Firewallregel hinzufügen, die den Zugriff der lokalen IP-Adresse auf den Datenbankserver zulässt.

Da Sie die lokale IP-Adresse zu Beginn dieses Artikels konfiguriert haben, können Sie die Firewall des Servers öffnen, indem Sie Folgendes ausführen:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-mysql-database"></a>Konfigurieren einer MySQL-Datenbank

Der MySQL-Server, den Sie zuvor erstellt haben, ist leer. Er verfügt über keine Datenbank, die Sie mit der Spring Boot-Anwendung verwenden können. Erstellen Sie eine neue Datenbank mit dem Namen `demo`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
    | jq
```
