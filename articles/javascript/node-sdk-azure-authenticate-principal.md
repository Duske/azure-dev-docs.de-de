---
title: Erstellen eines Azure-Dienstprinzipals mit Node.js
description: Hier erfahren Sie, wie Sie die Dienstprinzipalauthentifizierung mit Node.js und JavaScript verwenden.
ms.topic: how-to
ms.date: 06/17/2017
ms.custom: devx-track-js
ms.openlocfilehash: 40992b00ff9c0e04bf2b475fadf2d65dd3bd29d5
ms.sourcegitcommit: 717e32b68fc5f4c986f16b2790f4211967c0524b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91586116"
---
# <a name="create-an-azure-service-principal-for-nodejs"></a>Erstellen eines Azure-Dienstprinzipals für Node.js

Wenn eine App Zugriff auf Ressourcen benötigt, können Sie eine Identität für die App einrichten und sie mit ihren eigenen Anmeldeinformationen authentifizieren. Diese Identität wird als *Dienstprinzipal* bezeichnet. Im Grunde erstellen Sie für Ihr Azure Active Directory-Konto Schlüssel, die Sie an das SDK übergeben, um eine Authentifizierung ohne Benutzereingriff oder die Eingabe von Benutzer/Kennwort zu ermöglichen.

Der Dienstprinzipal ermöglicht Folgendes:
- Sie können der App-Identität Berechtigungen zuweisen, die sich von Ihren eigenen Berechtigungen unterscheiden. In der Regel sind diese Berechtigungen genau auf die Aufgaben der App beschränkt.
- Sie können ein Zertifikat für die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts verwenden.

In diesem Thema werden drei Methoden zum Erstellen eines Dienstprinzipals erläutert.

- Azure-Portal
- Azure CLI 2.0

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="create-a-service-principal-using-the-azure-portal"></a>Erstellen eines Dienstprinzipals mit dem Azure-Portal

Führen Sie die im Thema [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](/azure/active-directory/develop/howto-create-service-principal-portal) beschriebenen Schritte aus, um den Dienstprinzipal zu erstellen.

## <a name="create-a-service-principal-using-the-azure-cli-20"></a>Erstellen eines Dienstprinzipals mit der Azure CLI 2.0

Bei der Erstellung eines Dienstprinzipals mithilfe der [Azure CLI 2.0](/cli/azure/install-az-cli2) werden die folgenden Schritte ausgeführt:

1. Laden Sie die [Azure CLI 2.0](/cli/azure/install-az-cli2) herunter.

2. Öffnen Sie ein Terminalfenster, und geben Sie den Befehl `az login` ein, um den Anmeldeprozess zu starten.

3. Durch den Aufruf von `az login` werden eine URL und ein Code zurückgegeben. Navigieren Sie zur angegebenen URL, geben Sie den Code ein, und melden Sie sich mit Ihrer Azure-Identität an. (Dies geschieht unter Umständen automatisch, wenn Sie bereits angemeldet sind.) Sie können dann über die CLI auf Ihr Konto zugreifen.

4. Rufen Sie mithilfe des Befehls `az account list` Ihre Abonnement- und Ihre Mandanten-ID ab. Diese Werte werden bei der Arbeit mit den Azure-Paketen benötigt. Im Anschluss finden Sie eine Beispielausgabe dieses Befehls:

    ```shell
    {
    "cloudName": "AzureCloud",
    "id": "<subscriptionId>",
    "isDefault": true,
    "name": "<subscriptionName>",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<tenantId>",
        "user": {
            "name": "hello@example.com",
            "type": "user"
        }
    }
    ```

5. Führen Sie die im Thema [Erstellen eines Azure-Dienstprinzipals mit der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli) beschriebenen Schritte aus, um den Dienstprinzipal zu generieren. Das JSON-Objekt in der Ausgabe enthält die Informationen, die für die Authentifizierung mit Azure erforderlich sind.


## <a name="using-the-service-principal"></a>Verwenden des Dienstprinzipals

Wenn Sie über einen Dienstprinzipal verfügen, können Sie mithilfe der Schritte des Themas [Authentifizieren mit den Azure-Verwaltungsmodulen für JavaScript](./node-sdk-azure-authenticate.md) ein Anmeldeinformationsobjekt erstellen, das für die Authentifizierung Ihres Clients mit Azure Active Directory verwendet werden kann.
