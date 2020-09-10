---
title: 'Exemplarische Vorgehensweise: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine ausführliche exemplarische Vorgehensweise zum Authentifizieren einer Python-App bei Azure Active Directory, Azure Key Vault und Azure Queue Storage mithilfe der Bibliothek „azure-identity“ des Azure Python SDK
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 7f716f3276c0b93ec37ba0941f4029b2ee817fa0
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379543"
---
# <a name="walkthrough-integrated-authentication-for-python-apps-with-azure-services"></a>Exemplarische Vorgehensweise: Integrierte Authentifizierung für Python-Apps bei Azure-Diensten

Azure Active Directory (Azure AD) und Azure Key Vault bieten zusammen eine umfassende und bequeme Möglichkeit zum Authentifizieren von Anwendungen bei Azure-Diensten sowie bei Drittanbieterdiensten, bei denen Zugriffsschlüssel beteiligt sind.

Nachdem Sie einige Hintergrundinformationen bereitgestellt haben, werden diese Authentifizierungsfunktionen in dieser exemplarischen Vorgehensweise im Kontext des Beispiels [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication) erläutert.

Der Einfachheit halber ist das Beispiel bereits in Azure bereitgestellt, sodass Sie es im Einsatz sehen können. Wenn Sie das Beispiel in Ihrem eigenen Azure-Abonnement bereitstellen möchten, enthält das Repository auch Azure CLI Bereitstellungsskripts.

## <a name="part-1-background"></a>Teil 1: Hintergrund

Obwohl viele Azure-Dienste für die Autorisierung ausschließlich von der rollenbasierten Zugriffssteuerung abhängen, steuern bestimmte Dienste den Zugriff auf die jeweiligen Ressourcen mithilfe von Geheimnissen oder Schlüsseln. Zu diesen Diensten gehören Azure Storage, Datenbanken, Cognitive Services, Key Vault und Event Hubs.

Beim Erstellen einer Cloud-App, die Ressourcen innerhalb dieser Dienste verwendet, verwenden Sie das Azure-Portal, die Azure CLI oder Azure PowerShell, um Schlüssel für Ihre App zu erstellen und zu konfigurieren, die an bestimmte Zugriffsrichtlinien gebunden sind. Diese Schlüssel verhindern den Zugriff auf diese App-spezifischen Ressourcen durch jeden anderen nicht autorisierten Code.

Innerhalb dieses allgemeinen Entwurfs müssen Cloud-Apps diese Schlüssel in der Regel verwalten und sich bei jedem Dienst einzeln authentifizieren – ein Prozess, der mühsam und fehleranfällig sein kann. Wenn Sie Schlüssel direkt im App-Code verwalten, besteht auch die Gefahr, dass diese Schlüssel in der Quellcodeverwaltung verfügbar gemacht werden und Schlüssel auf unsicheren Entwicklerarbeitsstationen gespeichert werden.

Glücklicherweise stellt Azure zwei spezifische Dienste bereit, die den Prozess vereinfachen und für höhere Sicherheit sorgen:

- Azure Key Vault bietet sicheren cloudbasierten Speicher für Zugriffsschlüssel (zusammen mit Kryptografieschlüsseln und Zertifikaten, die in diesem Artikel nicht behandelt werden). Bei Verwendung von Key Vault greift die App nur zur Laufzeit auf solche Schlüssel zu, sodass Sie niemals direkt im Quellcode angezeigt werden.

- Mit verwalteten Identitäten in Azure Active Directory (Azure AD) muss sich die App nur einmal bei Active Directory authentifizieren. Die App wird dann automatisch bei anderen Azure-Diensten authentifiziert, einschließlich Key Vault. Folglich muss sich Ihr Code nie mit Schlüsseln oder anderen Anmeldeinformationen für diese Azure-Dienste befassen. Noch besser: Sie können denselben Code sowohl lokal als auch in der Cloud mit minimalen Konfigurationsanforderungen ausführen.

Wenn Azure AD und Key Vault zusammen verwendet werden, muss sich Ihre App also nie bei einzelnen Azure-Diensten authentifizieren und kann einfach und sicher auf alle Schlüssel zugreifen, die für Dienste von Drittanbietern erforderlich sind.

> [!IMPORTANT]
> In diesem Artikel wird der allgemeine, allgemeine Begriff „Schlüssel“ verwendet, um sich auf das zu beziehen, was in Azure Key Vault als „Geheimnisse“ gespeichert wird, wie z. B. ein Zugriffsschlüssel für eine REST-API. Diese Verwendung sollte nicht mit der Verwaltung von *krytografischen* Schlüsseln durch Key Vault verwechselt werden, dabei handelt es sich um eine von den *Geheimnissen* von Key Vault getrennte Funktion.

## <a name="example-cloud-app-scenario"></a>Beispielszenario für Cloud-Apps

Das folgende Szenario hilft dabei, den Authentifizierungsprozess von Azure besser zu verstehen:

- Eine Haupt-App macht einen öffentlichen (nicht authentifizierten) API-Endpunkt verfügbar, der auf HTTP-Anforderungen mit JSON-Daten antwortet. Der in diesem Artikel gezeigte Beispielendpunkt ist als einfache Flask-App implementiert, die für Azure App Service bereitgestellt wird.

- Um die Antwort zu generieren, ruft die API eine Drittanbieter-API auf, die einen Zugriffsschlüssel erfordert. Die App ruft diesen Zugriffsschlüssel zur Laufzeit aus Azure Key Vault ab.

- Vor dem Zurückgeben der Antwort schreibt die API eine Meldung zur späteren Verarbeitung in eine Azure Storage-Warteschlange. (Die spezifische Verarbeitung dieser Meldungen ist für das Hauptszenario nicht relevant).

![Diagramm des Anwendungsszenarios](media/walkthrough-tutorial-authentication/scenario-diagram.png)

> [!NOTE]
> Ein öffentlicher API-Endpunkt ist zwar in der Regel durch einen eigenen Zugriffsschlüssel geschützt, für den Zweck dieses Artikels wird jedoch angenommen, dass der Endpunkt offen und nicht authentifiziert ist. Diese Annahme vermeidet Verwechslungen zwischen den Authentifizierungsanforderungen der App und denen eines *externen* Aufrufers dieses Endpunkts. In diesem Szenario wird ein solcher externer Aufrufer nicht veranschaulicht.

> [!div class="nextstepaction"]
> [Teil 2: Authentifizierungsanforderungen >>>](walkthrough-tutorial-authentication-02.md)
