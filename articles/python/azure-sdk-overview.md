---
title: Verwenden der Azure-Bibliotheken (SDK) für Python
description: Übersicht über die Features und Funktionen der Azure-Bibliotheken für Python, die Entwicklern zu mehr Produktivität beim Bereitstellen, Verwenden und Verwalten von Azure-Ressourcen verhelfen.
ms.date: 09/19/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 6dab8085cd9d18f34ea1dc307a414fa18cdbcb83
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2020
ms.locfileid: "90829956"
---
# <a name="use-the-azure-libraries-sdk-for-python"></a>Verwenden der Azure-Bibliotheken (SDK) für Python

Die Open-Source-basierten Azure-Bibliotheken für Python vereinfachen die Bereitstellung, Verwaltung und Verwendung von Azure-Ressourcen über Python-Anwendungscode.

## <a name="the-details-you-really-want-to-know"></a>Die Details, die Sie wirklich interessieren

- Über die Azure-Bibliotheken kommunizieren Sie mit Azure-Diensten *über* Python-Code, den Sie entweder lokal oder in der Cloud ausführen. (Ob Sie Python-Code innerhalb des Bereichs eines bestimmten Diensts ausführen können, hängt davon ab, ob dieser Dienst selbst zurzeit Python unterstützt.)

- Die Bibliotheken unterstützen Python 2.7 und Python 3.5.3 oder höher und wurden außerdem mit PyPy 5.4 oder höher getestet.

- Im Azure SDK für Python sind über 180 einzelne Python-Bibliotheken enthalten, die sich auf bestimmte Azure-Dienste beziehen. Das SDK enthält keine weiteren Tools.

- Wenn Sie Code lokal ausführen, basiert die Authentifizierung mit Azure auf Umgebungsvariablen, wie unter [Konfigurieren der lokalen Entwicklungsumgebung](configure-local-development-environment.md) beschrieben. 

- Die benötigten Bibliothekspakete werden mithilfe von `pip install <library_name>` unter Angabe der Bibliotheksnamen aus dem [Paket-Index für das Python SDK](azure-sdk-library-package-index.md) installiert. Ausführlichere Informationen finden Sie unter [Installieren des Azure SDK für Python-Bibliotheken](azure-sdk-install.md).

- Es gibt getrennte „Verwaltungs“- und „Client“-Bibliotheken (manchmal auch als Bibliotheken der „Verwaltungsebene“ bzw. „Datenebene“ bezeichnet). Jede Gruppe dient unterschiedlichen Zwecken und wird von einer anderen Art von Code genutzt. Weitere Details finden Sie in den folgenden Abschnitten weiter unten in diesem Artikel:
  - [Bereitstellen und Verwalten von Azure-Ressourcen mit Verwaltungsbibliotheken](#provision-and-manage-azure-resources-with-management-libraries)
  - [Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken](#connect-to-and-use-azure-resources-with-client-libraries)

- Dokumentationsmaterial für die Bibliotheken finden Sie in der nach Azure-Dienst strukturierten [Referenz](/python/api/overview/azure/?view=azure-python) oder im nach Paketname strukturierten [Python-API-Browser](/python/api/?view=azure-python). Derzeit müssen Sie häufig auf eine Reihe von Ebenen klicken, um zu den Klassen und Methoden zu gelangen, die Sie interessieren. Wir möchten uns im Voraus für diese unterdurchschnittliche Erfahrung entschuldigen. Wir arbeiten an einer Verbesserung!

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

Die *Verwaltungs*bibliotheken des SDK (oder die „Verwaltungsebene“), deren Namen alle mit `azure-mgmt-` beginnen, helfen Ihnen beim Erstellen, Bereitstellen und anderweitigen Verwalten von Azure-Ressourcen über Python-Skripts. Alle Azure-Dienste verfügen über entsprechende Verwaltungsbibliotheken.

Mit den Verwaltungsbibliotheken können Sie Konfigurations- und Bereitstellungsskripts schreiben, um dieselben Aufgaben auszuführen, die Sie auch über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [Azure CLI](/cli/azure/install-azure-cli) durchführen können. (Wie zuvor erwähnt, ist die Azure CLI in Python geschrieben und verwendet die Verwaltungsbibliotheken, um ihre verschiedenen Befehle zu implementieren.)

Ausführliche Informationen zur Verwendung der Verwaltungsbibliotheken finden Sie im [SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Verbinden mit und Verwenden von Azure-Ressourcen mit Clientbibliotheken

Die *Client*bibliotheken (oder die „Datenebene“) helfen Ihnen beim Schreiben von Python-Anwendungscode, um mit bereits bereitgestellten Diensten zu interagieren. Clientbibliotheken sind nur für Dienste mit Client-API-Unterstützung vorhanden.

Ausführliche Informationen zur Verwendung der Clientbibliotheken finden Sie im [GitHub-Repository des SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) in der Datei *README.md* oder *README.rst*, die sich im Projektordner der jeweiligen Bibliothek befindet. Weitere Codeausschnitte stehen in der [Referenzdokumentation](/python/api?view=azure-python) sowie in den [Azure-Beispielen](/samples/browse/?languages=python&products=azure) zur Verfügung.

## <a name="get-help-and-connect-with-the-sdk-team"></a>Hilfe erhalten und das SDK-Team kontaktieren

- Sehen Sie sich die [Dokumentation zu Azure-Bibliotheken für Python](https://aka.ms/python-docs) an.
- Stellen Sie in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python) Fragen an die Community.
- Dokumentieren Sie Probleme für das SDK auf [GitHub](https://github.com/Azure/azure-sdk-for-python/issues).
- Erwähnen Sie [@AzureSDK](https://twitter.com/AzureSdk/) auf Twitter.

## <a name="next-step"></a>Nächster Schritt

Es wird dringend empfohlen, Ihre lokale Entwicklungsumgebung einmalig einzurichten, um die Azure-Bibliotheken für Python problemlos verwenden zu können.

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung für Azure](configure-local-development-environment.md)
