---
title: Hinzufügen eines benutzerdefinierten Domänennamens zur Web-App
description: Hinzufügen Ihres benutzerdefinierten Domänennamens zu Ihrer Azure-Web-App mithilfe der Azure CLI.
ms.topic: how-to
ms.date: 01/29/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 977376fda6e7c93390c45196ae5baae751f5ad64
ms.sourcegitcommit: 3f8aa923e4626b31cc533584fe3b66940d384351
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99230151"
---
# <a name="configuring-a-custom-domain-name"></a>Konfigurieren eines benutzerdefinierten Domänennamens

Hinzufügen Ihres benutzerdefinierten Domänennamens zu Ihrer Azure-Web-App mithilfe der Azure CLI. 

Ihr App Service verfügt über einen praktischen DNS-Namen, der sich für Tests in Form von `YOUR-RESOURCE-NAME.azurewebsites.net` hervorragend eignet. Zu einem gewissen Zeitpunkt kann es dann ratsam sein, Ihrer Web-App einen benutzerdefinierten Domänennamen hinzuzufügen. 

## <a name="purchase-a-domain-name-and-configure-dns-record"></a>Kaufen eines Domänennamens und Konfigurieren des DNS-Eintrags

1. Kaufen Sie einen Domänennamen bei einer Registrierungsstelle für Domänennamen. 
1. Fügen Sie für den DNS-Eintrag Ihrem DNS-Eintrag einen `A`-Eintrag hinzu, der auf die externe IP-Adresse Ihrer Web-App (wobei es sich eigentlich um einen Lastenausgleich handelt) verweist. Verwenden Sie das Verfahren im nächsten Abschnitt, um Ihre externe IP-Adresse abzurufen.

    Zusätzlich zu einem `A`-Eintrag müssen Sie Ihrer Domäne auch einen `TXT`-Eintrag hinzufügen, mit dem auf die bisher verwendete Domäne `*.azurewebsites.net` verwiesen wird. Anhand der Kombination der `A`- und `TXT`-Einträge kann Azure bestätigen, dass Sie der Besitzer der Domäne sind.

## <a name="get-web-app-external-ip"></a>Abrufen der externen IP-Adresse der Web-App

Sie können diese IP abrufen, indem Sie den folgenden Befehl ausführen:

```azurecli
az webapp config hostname get-external-ip --name
```

<a name="register-a-domain-name-with-your-azure-app"></a>

## <a name="configure-web-app-domain-name"></a>Konfigurieren des Web-App-Domainnamens 

Nachdem diese Einträge erstellt und die DNS-Änderungen weitergegeben wurden, können Sie die benutzerdefinierte Domäne bei Azure registrieren, damit alles richtig für den eingehenden Datenverkehr vorbereitet ist.

Verwenden Sie den Befehl [az webapp config hostname add](/cli/azure/webapp/config/hostname):

```azurecli
az webapp config hostname add \
    --hostname YOUR-DOMAIN-NAME
    --webapp-name YOUR-WEBAPP-NAME
    --resource-group YOUR-RESOURCE-GROUP-NAME
```

> [!NOTE]
> Der Befehl funktioniert erst, nachdem die DNS-Änderungen weitergegeben wurden.

Öffnen Sie einen Browser, und navigieren Sie zu Ihrer benutzerdefinierten Domäne, um zu prüfen, ob nun die Auflösung zu Ihrer bereitgestellten App in Azure durchgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Containerregistrierungsressource](create-container-registry-resource.md)