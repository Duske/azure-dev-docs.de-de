---
ms.openlocfilehash: f644d66895b7b8bcce345cd42c6efedfaddb4240
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764482"
---
Dieser Code verwendet die CLI-basierte Authentifizierung (`AzureCliCredential`), da er Aktionen veranschaulicht, die Sie andernfalls direkt mit der Azure CLI ausführen würden. In beiden Fällen verwenden Sie dieselbe Identität für die Authentifizierung.

Um solchen Code in einem Produktionsskript zu verwenden (z. B. zum Automatisieren der VM-Verwaltung), verwenden Sie `DefaultAzureCredential` (empfohlen) oder eine dienstprinzipalbasierte Methode, wie unter [Authentifizieren von Python-Apps mit Azure-Diensten](../azure-sdk-authenticate.md) beschrieben.
