---
title: Cloudspeicher zum Erstellen von äußerst sicheren, langlebigen und skalierbaren Apps mit Azure Storage
description: Erfahren Sie mehr über die Dienste, mit denen umfangreiche strukturierte und nicht strukturierte Daten einer mobilen Anwendung in der Cloud gespeichert werden können.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/08/2020
ms.author: masoucou
ms.openlocfilehash: 7a1171ebe64b0353fc57180adce63ee702431207
ms.sourcegitcommit: f02ff55cef47581303a217cf1d310879cd722237
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84632362"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Cloudspeicher für äußerst sichere, langlebige und skalierbare Apps mit Azure Storage

[Azure Storage](https://azure.microsoft.com/services/storage/) ist Microsofts Cloudspeicherlösung für moderne Anwendungen, die einen stark skalierbaren Objektspeicher für Datenobjekte, einen Dateisystemdienst für die Cloud, einen Messagingspeicher für zuverlässiges Messaging und einen NoSQL-Speicher bereitstellt. Für Azure Storage gilt Folgendes:

- **Robust und hoch verfügbar:** Mit Redundanz wird sichergestellt, dass Ihre Daten sicher sind, falls es zu vorübergehenden Hardwareausfällen kommt. Sie können sich auch für das Replizieren von Daten über Rechenzentren oder geografische Regionen hinweg entscheiden, um eine weitere Schutzebene vor lokalen Notfällen oder Naturkatastrophen zu schaffen. Daten, die auf diese Weise repliziert werden, sind bei einem unerwarteten Ausfall weiterhin hoch verfügbar.
- **Sicher:** Alle Daten, die in Azure Storage geschrieben werden, werden vom Dienst verschlüsselt. Bei Azure Storage können Sie genau steuern, wer Zugriff auf Ihre Daten hat.
- **Skalierbar:** Dienste sind auf hohe Skalierbarkeit ausgelegt, um die Datenspeicherungs- und Leistungsanforderungen heutiger Anwendungen zu erfüllen.
- **Verwaltet:** In Azure werden Hardwarewartung, Updates und die Behandlung kritischer Probleme für Sie übernommen.
- **Zugänglich:** Der Zugriff auf die Daten ist von überall auf der Welt über HTTP oder HTTPS möglich. Microsoft stellt Clientbibliotheken in verschiedenen Sprachen bereit wie .NET, Java, Node.js, Python, PHP, Ruby und Go sowie eine ausgereifte REST-API. Skripterstellung wird in Azure PowerShell oder in der Azure CLI unterstützt. Im Azure-Portal und über Azure Storage-Explorer werden einfache visuelle Lösungen für die Arbeit mit Ihren Daten bereitgestellt.

Mit den folgenden Diensten können Sie Cloudspeicher in ihren mobilen Apps aktivieren.

## <a name="azure-blob-storage"></a>Azure Blob Storage

[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) bietet eine Objektspeicherlösung für die Cloud. Blob Storage ist zum Speichern von großen Mengen unstrukturierter Daten optimiert, die nicht an ein bestimmtes Datenmodell oder eine bestimmte Datendefinition gebunden sind, z. B. Text oder binäre Daten. Er unterstützt verschiedene Sprachen, die von Clientbibliotheken verwendet werden. Blobspeicher ist für Folgendes konzipiert:

- Bereitstellen von Bildern oder Dokumenten direkt für einen Browser.
- Speichern von Dateien für verteilten Zugriff.
- Streaming von Video und Audio.
- Schreiben in Protokolldateien.
- Speichern von Daten für Sicherung und Wiederherstellung, Notfallwiederherstellung und Archivierung.
- Speichern von Daten für Analysen durch einen lokalen oder von Azure gehosteten Dienst.

### <a name="azure-blob-storage-references"></a>Azure Blob Storage-Referenzen

- [Azure portal](https://portal.azure.com)
- [Dokumentation zu Azure Blob Storage](/azure/storage/blobs/storage-blobs-introduction)
- [Schnellstarts](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [Beispiele](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure-Tabellenspeicher

[Azure Table Storage](https://azure.microsoft.com/services/storage/tables/) speichert strukturierte NoSQL-Daten in der Cloud und bietet einen Schlüssel- oder Attributspeicher mit einem schemalosen Design. Mit Azure Table Storage können Sie große Mengen strukturierter Daten speichern. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Tabellenspeicher wird normalerweise für Folgendes verwendet:

- Speichern strukturierter Daten in TB-Größe zur Verarbeitung webbasierter Anwendungen.
- Speichern von Datensätzen, die keine komplexen Verknüpfungen, Fremdschlüssel oder gespeicherte Prozeduren erfordern und für schnellen Zugriff denormalisiert werden können.
- Schnelles Abfragen von Daten mithilfe eines gruppierten Indexes.
- Zugriff auf Daten mithilfe des OData-Protokolls und von LINQ-Abfragen mit Windows Communication Foundation (WCF) Data Services .NET-Bibliotheken.

Sie können Tabellenspeicher zum Speichern und Abfragen umfangreicher strukturierter, nicht relationaler Datasets verwenden. Ihre Tabellen werden mit steigender Nachfrage skaliert.

### <a name="azure-table-storage-references"></a>Azure Table Storage-Referenzen

- [Azure portal](https://portal.azure.com)
- [Azure Table Storage-Dokumentation](/azure/storage/tables/table-storage-overview)
- [Beispiele](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Schnellstarts](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-queue-storage"></a>Azure Queue Storage

[Azure Queue Storage](https://azure.microsoft.com/services/storage/queues/) ist ein Dienst für die Speicherung sehr vieler Nachrichten. Sie können überall auf der Welt über authentifizierte Aufrufe mithilfe von HTTP oder HTTPS auf Nachrichten zugreifen. Eine Warteschlangennachricht kann bis zu 64 KB groß sein. Eine Warteschlange kann Millionen Nachrichten enthalten, bis die maximale Kapazität eines Speicherkontos erreicht ist. Warteschlangen werden häufig verwendet, um ein Arbeits-Backlog zur asynchronen Verarbeitung zu erstellen.

###  <a name="azure-queue-storage-references"></a>Azure Queue Storage-Referenzen

- [Azure portal](https://portal.azure.com)
- [Dokumentation zu Azure Queue Storage](/azure/storage/queues/)
- [Schnellstarts](/azure/storage/queues/storage-quickstart-queues-portal)
- [Beispiele](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
