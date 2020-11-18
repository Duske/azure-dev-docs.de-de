---
title: Erstellen eines Azure-Dienstprinzipals mit Node.js
description: Hier erfahren Sie, wie Sie die Dienstprinzipalauthentifizierung mit Node.js und JavaScript verwenden.
ms.topic: how-to
ms.date: 11/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: e7f885b0af5e7a25d0e706c235a1521bb44c4b36
ms.sourcegitcommit: 12f80b1e0fe08db707c198271d0c399c3aba343a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515171"
---
# <a name="create-an-azure-service-principal-for-nodejs"></a>Erstellen eines Azure-Dienstprinzipals für Node.js

Wenn eine App Zugriff auf Ressourcen benötigt, können Sie eine Identität für die App einrichten und sie mit ihren eigenen Anmeldeinformationen authentifizieren. Diese Identität wird als *Dienstprinzipal* bezeichnet. Im Grunde erstellen Sie für Ihr Azure Active Directory-Konto Schlüssel, die Sie an das SDK übergeben, um eine Authentifizierung ohne Benutzereingriff oder die Eingabe von Benutzer/Kennwort zu ermöglichen.

Der Dienstprinzipal ermöglicht Folgendes:
- Sie können der App-Identität Berechtigungen zuweisen, die sich von Ihren eigenen Berechtigungen unterscheiden. In der Regel sind diese Berechtigungen genau auf die Aufgaben der App beschränkt.
- Sie können ein Zertifikat für die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts verwenden.

In diesem Thema werden drei Methoden zum Erstellen eines Dienstprinzipals erläutert.

- Azure-Portal
- Azure CLI 2.0

[!INCLUDE [chrome-note](../includes/chrome-note.md)]

## <a name="create-a-service-principal-using-the-azure-cli-20"></a>Erstellen eines Dienstprinzipals mit der Azure CLI 2.0

Um die folgenden Schritte ausführen zu können, [installieren Sie die Azure CLI](/cli/azure/install-azure-cli), und [melden Sie sich bei Azure an](/cli/azure/authenticate-azure-cli). 

1. Rufen Sie mithilfe des Befehls `az account list` Ihre Abonnement- und Ihre Mandanten-ID ab. Diese Werte werden bei der Arbeit mit den Azure-Paketen benötigt. Im Anschluss finden Sie eine Beispielausgabe dieses Befehls:

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

1. Führen Sie die im Thema [Erstellen eines Azure-Dienstprinzipals mit der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli) beschriebenen Schritte aus, um den Dienstprinzipal zu generieren. Das JSON-Objekt in der Ausgabe enthält die Informationen, die für die Authentifizierung mit Azure erforderlich sind.

## <a name="using-the-service-principal"></a>Verwenden des Dienstprinzipals

Wenn Sie über einen Dienstprinzipal verfügen, können Sie folgende Schritte ausführen:

1. Authentifizieren Sie sich mit dem Dienstprinzipal und mit einem Zertifikat, mit Umgebungsvariablen oder mit einer Datei vom Typ `.json` programmgesteuert bei Azure. 
1. Erstellen Sie mit dem Dienstprinzipal Azure-Ressourcen, und verwenden Sie den Dienst.

Führen Sie die Schritte des Themas [Authentifizieren mit den Azure-Verwaltungsmodulen für JavaScript](./node-sdk-azure-authenticate.md) aus, um ein Anmeldeinformationsobjekt zu erstellen, das für die Authentifizierung Ihres Clients mit Azure Active Directory verwendet werden kann.
