---
title: Verwalten lokaler Dienstprinzipale für die Azure-Entwicklung
description: Verwalten von Dienstprinzipalen, die für die lokale Entwicklung mithilfe des Azure-Portals oder der Azure CLI erstellt werden.
ms.date: 05/12/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 8901a7ef9de7bbca31c5ba0352c79a4ee2c5cdf9
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983102"
---
# <a name="how-to-manage-service-principals"></a>Verwalten von Dienstprinzipalen

Aus Sicherheitsgründen möchten Sie immer sorgfältig verwalten, wie der App-Code für den Zugriff auf und die Änderung von Azure-Ressourcen autorisiert ist. Wenn Sie Code lokal testen, sollten Sie immer einen lokalen *Dienstprinzipal* verwenden, anstatt als vollständig berechtigter Benutzer zu arbeiten, wie unter [Konfigurieren der lokalen Python-Entwicklungsumgebung: Authentifizierung](configure-local-development-environment.md#configure-authentication) beschrieben wird.

Im Laufe der Zeit müssen Sie diese Dienstprinzipale wahrscheinlich löschen, umbenennen oder anderweitig verwalten, was mithilfe des Azure-Portals oder der Azure CLI geschehen kann.

## <a name="basics-of-azure-authorization"></a>Grundlagen der Azure-Autorisierung

Wenn Ihr Code versucht, einen Vorgang für Azure-Ressourcen auszuführen (über Klassen in den Azure-Bibliotheken), wird von Azure sichergestellt, dass die Anwendung dazu autorisiert ist, diese Aktion auszuführen. Sie verwenden das [Azure-Portal](https://portal.azure.com) oder die Azure CLI, um der Identität der Anwendung bestimmte rollen- oder ressourcenbasierte Berechtigungen zu erteilen. (Durch diese Vorgehensweise wird vermieden, dass übermäßige Berechtigungen für die Anwendung gewährt werden, die ausgenutzt werden können, wenn die Sicherheit Ihrer Anwendung beeinträchtigt sein sollte.)

Bei der Bereitstellung in Azure ist die Identität der Anwendung in der Regel mit dem Namen identisch, den Sie der App innerhalb des Diensts geben, der sie gehostet (z. B. Azure App Service, Azure Functions, ein virtueller Computer usw., wenn die verwaltete Identität aktiviert ist). Wenn Sie den Code lokal ausführen, ist jedoch kein solcher Hostingdienst beteiligt, sodass Sie Azure einen geeigneten Ersatz präsentieren müssen.

Zu diesem Zweck verwenden Sie einen lokalen *Dienstprinzipal*, bei der es sich nicht um eine Benutzeridentität, sondern um einen anderen Namen für eine App-Identität handelt. Der Dienstprinzipal besitzt einen Namen, einen „Mandantenbezeichner“ (im Wesentlichen eine ID für Ihre Organisation), einen App- oder „Clientbezeichner“ und ein Geheimnis/Kennwort. Diese Anmeldeinformationen sind ausreichend, um die Identität bei Azure zu authentifizieren. Azure kann dann überprüfen, ob diese Identität für den Zugriff auf eine bestimmte Ressource autorisiert ist.

Jeder Entwickler sollte über seinen eigenen Dienstprinzipal verfügen, der innerhalb seines Benutzerkontos auf der Arbeitsstation gesichert ist und niemals in einem Quellcodeverwaltungsrepository gespeichert wird. Wenn ein beliebiger Dienstprinzipal gestohlen oder kompromittiert wird, können Sie ihn problemlos im Azure-Portal löschen, um alle Berechtigungen aufzuheben, und den Dienstprinzipal dann für diesen Entwickler erneut erstellen.

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

- Erstellen, Anzeigen, Aktualisieren und Löschen von Dienstprinzipalen: Befehl [az ad sp](/cli/azure/ad/sp?view=azure-cli-latest). Siehe auch [Erstellen eines Azure-Dienstprinzipals mit der Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Verwalten von Rollenzuweisungen: Befehl [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest).

Weitere Informationen:

- [Authentifizieren von Python-Apps mit Azure-Diensten](azure-sdk-authenticate.md)
