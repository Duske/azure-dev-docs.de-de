---
title: 'Exemplarische Vorgehensweise, Teil 2: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine Erläuterung der unterschiedlichen Authentifizierungsanforderungen und Herausforderungen im Beispielszenario und der Art und Weise, wie diese Herausforderungen bei der integrierten Azure-Authentifizierung erfüllt werden.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: c6719e3c86b590edff551d98e5a961fd08f857c3
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379540"
---
# <a name="part-2-authentication-needs-in-the-scenario"></a>Teil 2: Authentifizierungsanforderungen im Szenario

[Vorheriger Teil: Einführung und Hintergrund](walkthrough-tutorial-authentication-01.md)

In diesem Beispielszenario gelten folgende Authentifizierungsanforderungen für die Haupt-App:

- Authentifizierung bei Azure Key Vault für den Zugriff auf den gespeicherten API-Schlüssel von Drittanbietern
- Authentifizierung bei der Drittanbieter-API mithilfe des API-Schlüssels
- Authentifizierung bei Azure Queue Storage mithilfe der erforderlichen Anmeldeinformationen für das Speicherkonto

Mit diesen drei separaten Anforderungen muss die Anwendung drei Sätze von Anmeldeinformationen verwalten: zwei für Azure-Ressourcen (Key Vault und Queue Storage) und eine für eine externe Ressource (die Drittanbieter-API).

Wie bereits erwähnt, können Sie alle Anmeldeinformationen in Key Vault auf sichere Weise verwalten. Davon ausgenommen sind die Anmeldeinformationen, die für Key Vault selbst erforderlich sind. Nachdem die Anwendung bei Key Vault authentifiziert wurde, kann sie alle anderen Schlüssel zur Laufzeit abrufen, um die Authentifizierung bei Diensten wie Queue Storage durchzuführen.

Bei diesem Ansatz muss die App jedoch weiterhin die Anmeldeinformationen für Key Vault separat verwalten. Wie können Sie dann diese Anmeldeinformationen sicher verwalten, sodass sie sowohl für die lokale Entwicklung als auch für die Produktionsbereitstellung in der Cloud funktionieren?

Eine partielle Lösung besteht darin, den Schlüssel in einer serverseitigen Umgebungsvariablen (d. h. über eine Anwendungseinstellung bei Azure App Service und Azure Functions) zu speichern, die zumindest den Schlüssel aus der Quellcodeverwaltung heraushält. Zum Ausführen des Codes auf einer Entwicklerarbeitsstation müssen Sie jedoch die Umgebungsvariable lokal replizieren. Dies birgt die Gefahr, dass die Anmeldeinformationen offengelegt und/oder versehentlich in die Quellcodeverwaltung eingebunden werden. Sie können das Problem in gewissem Umfang umgehen, indem Sie spezielle Prozeduren in der Entwicklungsversion Ihres Codes implementieren, allerdings wird dadurch der Entwicklungsprozess komplexer.

Glücklicherweise macht die integrierte Authentifizierung bei Azure Active Directory (AD) es möglich, dass eine App keinerlei Azure-Anmeldeinformationen verarbeitet.

## <a name="integrated-authentication-with-managed-identity"></a>Integrierte Authentifizierung mit verwalteter Identität

Viele Azure-Dienste wie Storage und Key Vault sind in Azure Active Directory (Azure AD) integriert, sodass beim Authentifizieren der Anwendung bei Azure AD mithilfe einer [verwalteten Identität](/azure/active-directory/managed-identities-azure-resources/overview) automatisch die Authentifizierung bei anderen verbundenen Ressourcen erfolgt. Die Autorisierung für die Identität erfolgt über [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)](how-to-assign-role-permissions.md) und gelegentlich über andere Zugriffsrichtlinien.

Diese Integration bedeutet, dass Sie keine Azure-bezogenen Anmeldeinformationen in Ihrem App-Code verarbeiten müssen, und dass diese Anmeldeinformationen niemals auf Entwicklerarbeitsstationen oder in der Quellcodeverwaltung angezeigt werden. Außerdem wird die Verarbeitung von Schlüsseln für APIs und Dienste von Drittanbietern vollständig zur Laufzeit durchgeführt, sodass diese Schlüssel sicher bleiben.

Die verwaltete Identität eignet sich speziell für Apps, die in Azure bereitgestellt werden. Bei der lokalen Entwicklung erstellen Sie einen separaten Dienstprinzipal, der bei lokaler Ausführung als App-Identität fungiert. Sie stellen diesen Dienstprinzipal den Azure-Bibliotheken mithilfe von Umgebungsvariablen zur Verfügung, wie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure – Konfigurieren der Authentifizierung](configure-local-development-environment.md#configure-authentication) beschrieben. Außerdem weisen Sie diesem Dienstprinzipal neben der verwalteten Identität, die in der Cloud verwendet wird, Rollen Berechtigungen zu.

Wenn Sie diese Schritte für den lokalen Dienstprinzipal ausführen, funktioniert derselbe Code sowohl lokal als auch in der Cloud, um die App bei Azure-Ressourcen zu authentifizieren. Diese Details werden in [Authentifizieren und Autorisieren von Apps](azure-sdk-authenticate.md) erläutert, aber die kurze Version lautet wie folgt:

1. Erstellen Sie in Ihrem Code ein `DefaultAzureCredential`-Objekt, das automatisch Ihre verwaltete Identität bei der Ausführung in Azure und Ihren separaten Dienstprinzipal bei der lokalen Ausführung verwendet.

1. Verwenden Sie diese Anmeldeinformationen beim Erstellen der entsprechenden Clientobjekte für die einzelnen Ressourcen, auf die Sie zugreifen möchten (Key Vault, Queue Storage usw.).

1. Die Authentifizierung erfolgt dann, wenn Sie über das Clientobjekt eine Vorgangsmethode aufrufen, die einen Rest-API-Aufruf für die Ressource generiert.

1. Wenn die App-Identität gültig ist, prüft Azure auch, ob diese Identität auch für den jeweiligen Vorgang autorisiert ist.

Im restlichen Teil dieses Tutorials werden alle Details des Prozesses im Zusammenhang mit dem Beispielszenario und der zugehörige Beispielcode veranschaulicht.

Im Bereitstellungsskript des Beispiels werden alle Ressourcen in einer Ressourcengruppe mit dem Namen `auth-scenario-rg` erstellt. Diese Gruppe wird mit dem Azure CLI-Befehl [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) erstellt.

> [!div class="nextstepaction"]
> [Teil 3: Beispiel für die Implementierung einer Drittanbieter-API >>>](walkthrough-tutorial-authentication-03.md)
