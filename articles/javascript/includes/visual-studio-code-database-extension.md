---
ms.custom: devx-track-js
ms.topic: include
ms.date: 02/08/2021
ms.openlocfilehash: 693e78bef335871dbee846666f889ad1cf2982b1
ms.sourcegitcommit: 98a7e855206ff463c1d95f93c23dd665b26a0aa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100004996"
---
## <a name="create-azure-database-with-visual-studio-code-extension"></a>Erstellen einer Azure-Datenbank mit Visual Studio Code-Erweiterung

Verwenden Sie dieses Verfahren für die folgenden Ressourcentypen:

* Azure Cosmos DB für MongoDB-API
* SQL
* Azure-Tabelle
* Gremlin
* [PostgreSQL](#create-a-postgresql-database) 

### <a name="create-a-postgresql-database"></a>Erstellen einer PostgreSQL-Datenbank

1. Installieren Sie die Erweiterung [Azure Databases](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) für Visual Studio Code.
1. Wählen Sie in Visual Studio Code auf der [Aktivitätsleiste](https://code.visualstudio.com/docs/getstarted/userinterface) die Option **Azure** und anschließend auf der [Seitenleiste](https://code.visualstudio.com/docs/getstarted/userinterface) die Option **Datenbanken** aus.
1. Klicken Sie mit der rechten Maustaste auf den Abonnementnamen, und wählen Sie dann **Server erstellen** aus.
1. Wählen Sie in der Liste **PostgreSQL** aus. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server.png" alt-text="Auswählen von „PostgreSQL“ in der Liste":::

1. Geben Sie einen Namen für den PostgreSQL-Server ein. Dieser Name wird als Teil der Verbindungszeichenfolge verwendet. 
1. Geben Sie einen Administratorbenutzernamen ein. 
1. Geben Sie ein Administratorkennwort ein. Geben Sie es anschließend zur Bestätigung auf dem nächsten Bildschirm ein zweites Mal ein. 
1. Wählen Sie Ihre aktuelle IP-Adresse aus, um sie als Firewallregel hinzuzufügen. 
1. Wählen Sie einen Ressourcengruppennamen aus, oder erstellen Sie einen neuen. 
1. Wählen Sie eine Azure-Region für Ihren Server aus. 
1. Im Benachrichtigungsfenster wird der Status angezeigt. 

    :::image type="content" source="../media/howto-visual-studio-code/create-azure-database-server-status.png" alt-text="Anzeigen des Status im Benachrichtigungsfenster":::