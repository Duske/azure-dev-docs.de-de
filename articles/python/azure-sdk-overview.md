---
title: Verwenden der Azure-Bibliotheken (SDK) für Python
description: Übersicht über die Features und Funktionen der Azure-Bibliotheken für Python, die Entwicklern zu mehr Produktivität beim Bereitstellen, Verwenden und Verwalten von Azure-Ressourcen verhelfen.
ms.date: 09/19/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e24ecd235598d5298c5a71a317cd2415d9bfe01f
ms.sourcegitcommit: 6fbf9e489b194586887a2c11152044be5b3a2b99
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98759542"
---
# <a name="use-the-azure-libraries-sdk-for-python"></a>Verwenden der Azure-Bibliotheken (SDK) für Python

Die Open-Source-basierten Azure-Bibliotheken für Python vereinfachen die Bereitstellung, Verwaltung und Verwendung von Azure-Ressourcen über Python-Anwendungscode.

## <a name="the-details-you-really-want-to-know"></a>Die Details, die Sie wirklich interessieren

- Über die Azure-Bibliotheken kommunizieren Sie mit Azure-Diensten *über* Python-Code, den Sie entweder lokal oder in der Cloud ausführen. (Ob Sie Python-Code innerhalb des Bereichs eines bestimmten Diensts ausführen können, hängt davon ab, ob dieser Dienst selbst zurzeit Python unterstützt.)

- Die Bibliotheken unterstützen Python 2.7 und Python 3.5.3 oder höher und wurden außerdem mit PyPy 5.4 oder höher getestet.

- Im Azure SDK für Python sind über 180 einzelne Python-Bibliotheken enthalten, die sich auf bestimmte Azure-Dienste beziehen. Das SDK enthält keine weiteren Tools.

- Wenn Sie Code lokal ausführen, basiert die Authentifizierung mit Azure auf Umgebungsvariablen, wie unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md) beschrieben. 

- Die benötigten Bibliothekspakete werden mithilfe von `pip install <library_name>` unter Angabe der Bibliotheksnamen aus dem [Paket-Index für das Python SDK](azure-sdk-library-package-index.md) installiert. Ausführlichere Informationen finden Sie unter [Installieren des Azure SDK für Python-Bibliotheken](azure-sdk-install.md).

- Es gibt getrennte Bibliotheken für die **Verwaltung** und für den **Client**. Diese werden manchmal auch als Bibliotheken der Verwaltungs- bzw. Datenebene bezeichnet. Jede Gruppe dient unterschiedlichen Zwecken und wird von einer anderen Art von Code genutzt. Weitere Details finden Sie in den folgenden Abschnitten weiter unten in diesem Artikel:
  - [Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken](#provision-and-manage-azure-resources-with-management-libraries)
  - [Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken](#connect-to-and-use-azure-resources-with-client-libraries)

- Dokumentationsmaterial für die Bibliotheken finden Sie in der nach Azure-Dienst strukturierten [Referenz](/python/api/overview/azure/) oder im nach Paketname strukturierten [Python-API-Browser](/python/api/). Derzeit müssen Sie häufig auf eine Reihe von Ebenen klicken, um zu den Klassen und Methoden zu gelangen, die Sie interessieren. Wir möchten uns im Voraus für diese unterdurchschnittliche Erfahrung entschuldigen. Wir arbeiten an einer Verbesserung!

- Wenn Sie die Bibliotheken selbst ausprobieren möchten, sollten Sie zunächst [Ihre lokale Entwicklungsumgebung einrichten](configure-local-development-environment.md). Anschließend können Sie eines der folgenden eigenständigen Beispiele ausprobieren. Die Reihenfolge spielt dabei keine Rolle: [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md), [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md), [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Web-App](azure-sdk-example-web-app.md), [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen einer Datenbank](azure-sdk-example-database.md) und [Beispiel: Verwenden der Azure-Bibliotheken zum Bereitstellen eines virtuellen Computers](azure-sdk-example-virtual-machines.md).

- Ein Demonstrationsvideo von der virtuellen PyCon 2020 finden Sie unter <a href="https://www.youtube.com/watch?v=M1pVxItg2Mg&feature=youtu.be&ocid=AID3006292" target="_blank">Verwenden von Azure SDKs für die Interaktion mit Azure-Ressourcen</a> (youtube.com).

### <a name="non-essential-but-still-interesting-details"></a>Nicht wesentliche, aber immer noch interessante Details

- Da die Azure-Befehlszeilenschnittstelle in Python unter Verwendung der Verwaltungsbibliotheken geschrieben wurde, ist alles, was mit Azure CLI-Befehlen möglich ist, auch mit einem Python-Skript möglich. Dies bedeutet, dass CLI-Befehle viele hilfreiche Features bereitstellen, wie z. B. das gemeinsame Ausführen mehrerer Aufgaben, das automatische Verarbeiten asynchroner Vorgänge, das Formatieren von Ausgaben wie Verbindungszeichenfolgen usw. Folglich kann die Verwendung der CLI (oder ihres Äquivalents, der Azure PowerShell) für automatisierte Bereitstellungs- und Verwaltungsskripts wesentlich bequemer sein, als das Schreiben des entsprechenden Python-Codes, es sei denn, Sie möchten einen wesentlich höheren Grad der Kontrolle über den Prozess ausüben.

- Da die Azure-Bibliotheken für Python auf der zugrunde liegenden Azure-REST-API basieren, können diese APIs mit vertrauten Python-Paradigmen verwendet werden. Sie können die REST-API aber auch immer direkt aus Python-Code heraus verwenden, wenn dies gewünscht ist.

- Der Quellcode für die Azure-Bibliotheken steht unter [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) zur Verfügung. Da es sich um ein Open-Source-Projekt handelt, sind Beiträge herzlich willkommen.

- Die Bibliotheken können zwar mit anderen Interpretern wie IronPython und Jython verwendet werden, mit denen sie nicht getestet wurden, dabei kann es jedoch zu vereinzelten Problemen und Inkompatibilitäten kommen.

- Das Quellrepository für die Referenzdokumentation zur Bibliotheks-API finden Sie unter [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/).

- Die Azure-Bibliotheken für Python werden momentan mit allgemeinen Cloudmustern wie Authentifizierungsprotokollen, Protokollierung, Ablaufverfolgung, Transportprotokollen, gepufferten Antworten und Wiederholungen aktualisiert.

  - Diese gemeinsam genutzten Funktionen sind in der Bibliothek [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core) enthalten.

  - Die Bibliotheken, die aktuell mit der Core-Bibliothek funktionieren, sind unter[Neueste Releases des Azure SDK für Python](azure-sdk-library-package-index.md#libraries-using-azurecore) aufgeführt. Diese Bibliotheken, primär die Clientbibliotheken, werden manchmal als „Track 2“ bezeichnet.

  - Die Verwaltungsbibliotheken sowie alle anderen Bibliotheken, die noch nicht aktualisiert wurden, werden mitunter als „Track 1“ bezeichnet.

- Ausführliche Informationen zu den von uns auf die Bibliotheken angewendeten Richtlinien finden Sie unter [Python-Richtlinien: Einführung](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken

Die *Verwaltungs* bibliotheken des SDK (oder die „Verwaltungsebene“), deren Namen alle mit `azure-mgmt-` beginnen, helfen Ihnen beim Erstellen, Bereitstellen und anderweitigen Verwalten von Azure-Ressourcen über Python-Skripts. Alle Azure-Dienste verfügen über entsprechende Verwaltungsbibliotheken.

Mit den Verwaltungsbibliotheken können Sie Konfigurations- und Bereitstellungsskripts schreiben, um dieselben Aufgaben auszuführen, die Sie auch über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [Azure CLI](/cli/azure/install-azure-cli) durchführen können. (Wie zuvor erwähnt, ist die Azure CLI in Python geschrieben und verwendet die Verwaltungsbibliotheken, um ihre verschiedenen Befehle zu implementieren.)

In den folgenden Beispielen wird die Verwendung einiger der primären Verwaltungsbibliotheken veranschaulicht:

- [Bereitstellen einer Ressourcengruppe](azure-sdk-example-resource-group.md)
- [Auflisten von Ressourcengruppen in einem Abonnement](azure-sdk-example-list-resource-groups.md)
- [Bereitstellen von Azure Storage](azure-sdk-example-storage.md)
- [Bereitstellen einer Web-App sowie von Code](azure-sdk-example-web-app.md)
- [Bereitstellen und Abfragen einer Datenbank](azure-sdk-example-database.md)
- [Bereitstellen von virtuellen Computern](azure-sdk-example-virtual-machines.md)

Ausführliche Informationen zur Verwendung der Verwaltungsbibliotheken finden Sie im [SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api) sowie in den [Azure-Beispielen](/samples/browse/?languages=python&term=Getting%20started%20-%20Managing) zur Verfügung.

### <a name="migrating-from-older-management-libraries"></a>Migrieren aus älteren Verwaltungsbibliotheken

Wenn Sie Code von älteren Versionen der Verwaltungsbibliotheken migrieren möchten, lesen Sie die folgenden Informationen:

- Wenn Sie die Klasse `ServicePrincipalCredentials` verwenden, lesen Sie [Authentifizieren mit Zugriffstoken-Anmeldeinformationen](azure-sdk-authenticate.md#authenticate-with-token-credentials).
- Die Namen asynchroner APIs haben sich geändert. Entsprechende Informationen finden Sie unter [Asynchrone Vorgänge](azure-sdk-library-usage-patterns.md#asynchronous-operations). Kurz gesagt: In neueren Bibliotheken beginnen die Namen asynchroner APIs mit `begin_`. Die API-Signatur bleibt in den meisten Fällen unverändert.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken

Die *Client* bibliotheken (oder die „Datenebene“) helfen Ihnen beim Schreiben von Python-Anwendungscode, um mit bereits bereitgestellten Diensten zu interagieren. Clientbibliotheken sind nur für Dienste mit Client-API-Unterstützung vorhanden.

Im Artikel [Beispiel: Zugriff auf Azure Storage mit den Azure-Bibliotheken für Python](azure-sdk-example-storage-use.md) wird die Verwendung der Clientbibliothek grundlegend erläutert.

Für verschiedene Azure-Dienste stehen ebenfalls Beispiele mit diesen Bibliotheken zur Verfügung. Weitere Links finden Sie auf den folgenden Indexseiten:

- [App-Hosting](quickstarts-app-hosting.md)
- [Cognitive Services](quickstarts-cognitive-services.md)
- [Datenlösungen](quickstarts-data-solutions.md)
- [Identität und Sicherheit](quickstarts-identity-security.md)
- [Maschinelles Lernen](quickstarts-machine-learning.md)
- [Messaging und IoT](quickstarts-messaging-iot.md)
- [Sonstige Dienste](quickstarts-other-services.md)

Ausführliche Informationen zur Verwendung der Clientbibliotheken finden Sie im [GitHub-Repository des SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api) sowie in den [Azure-Beispielen](/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="get-help-and-connect-with-the-sdk-team"></a>Hilfe erhalten und das SDK-Team kontaktieren

- Sehen Sie sich die [Dokumentation zu Azure-Bibliotheken für Python](https://aka.ms/python-docs) an.
- Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python) Fragen an die Community.
- Dokumentieren Sie Probleme für das SDK auf [GitHub](https://github.com/Azure/azure-sdk-for-python/issues).
- Erwähnen Sie [@AzureSDK](https://twitter.com/AzureSdk/) auf Twitter.
- [Kurze Umfrage zum Azure SDK für Python](https://microsoft.qualtrics.com/jfe/form/SV_bNFX0HECjzPWMiG?Q_CHL=docs)

## <a name="next-step"></a>Nächster Schritt

Es wird dringend empfohlen, Ihre lokale Entwicklungsumgebung einmalig einzurichten, um die Azure-Bibliotheken für Python problemlos verwenden zu können.

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md)
