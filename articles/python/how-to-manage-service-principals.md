---
title: Verwalten lokaler Dienstprinzipale für die Azure-Entwicklung
description: Verwalten von Dienstprinzipalen, die für die lokale Entwicklung mithilfe des Azure-Portals oder der Azure CLI erstellt werden.
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 9d090a4615621c60485b64fac22929472c0cd175
ms.sourcegitcommit: 29b161c450479e5d264473482d31e8d3bf29c7c0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764788"
---
# <a name="how-to-manage-service-principals"></a>Verwalten von Dienstprinzipalen

Wie unter [Authentifizieren von Apps mit Azure](azure-sdk-authenticate.md) beschrieben, nutzen Sie Dienstprinzipale häufig zum Identifizieren einer App mit Azure (eine Ausnahme ist die Verwendung einer verwalteten Identität).

Im Laufe der Zeit müssen Sie diese Dienstprinzipale normalerweise löschen, umbenennen oder anderweitig verwalten. Hierfür können Sie das Azure-Portal oder die Azure CLI verwenden.

## <a name="manage-service-principals-using-the-azure-portal"></a>Verwalten von Dienstprinzipalen über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zur Seite **Azure Active Directory**, indem Sie entweder das Symbol auf der Homepage des Portals verwenden oder in der Suchleiste des Portals nach „Azure Active Directory“ suchen.

    ![Suchen nach „Azure Active Directory“ im Azure-Portal](media/how-to-manage-service-principals/azure-ad-portal-search.png)

1. Wählen Sie im linken Navigationsmenü **Verwalten** > **App-Registrierungen** aus. Ihre lokalen Entwicklungsdienstprinzipale werden in der Liste angezeigt:

    ![App-Registrierungen in Azure Active Directory](media/how-to-manage-service-principals/azure-ad-app-registrations.png)

1. Wählen Sie einen der Dienstprinzipale aus, um zu seiner Eigenschaftenseite zu navigieren, auf der Sie ID-Werte untersuchen, den Dienstprinzipal umbenennen oder löschen und verschiedene Endpunkt-URLs abrufen können.

1. Der Prozess der Autorisierung eines Dienstprinzipals für den Zugriff auf eine bestimmte Ressource hängt in der Regel vom fraglichen Dienst ab. Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Dienst. Die Artikel [Autorisierung für Blob Storage](/azure/storage/common/storage-auth-aad-rbac-portal) und [Autorisierung für Queue Storage](/azure/storage/common/storage-auth-aad-rbac-portal) beschreiben den Vorgang im Rahmen von Azure Storage.

## <a name="manage-service-principals-using-the-azure-cli"></a>Verwalten von Dienstprinzipalen mit der Azure CLI

Mithilfe der Azure CLI können Sie viele der gleichen Vorgänge für Dienstprinzipale ausführen, die Sie über das Azure-Portal durchführen können:

- Erstellen, Anzeigen, Aktualisieren und Löschen von Dienstprinzipalen: Befehl [az ad sp](/cli/azure/ad/sp). Siehe auch [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).
- Verwalten von Rollenzuweisungen: Befehl [az role assignment](/cli/azure/role/assignment).

Weitere Informationen:

- [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md)
