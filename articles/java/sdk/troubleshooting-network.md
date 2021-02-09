---
title: Behandeln von Netzwerkproblemen bei Verwendung des Azure SDK für Java
description: Eine Übersicht über die Behandlung von Netzwerkproblemen im Zusammenhang mit der Verwendung des Azure SDK für Java
author: alzimmermsft
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: alzimmer
ms.openlocfilehash: 63852f253a648e473ba91ac361bc5d9d0629b8f1
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528456"
---
# <a name="troubleshoot-networking-issues"></a>Behandlung von Netzwerkproblemen

In diesem Artikel werden ein paar Tools beschrieben, mit denen sich Netzwerkprobleme verschiedener Komplexitäten diagnostizieren lassen. Zu diesen Problemen gehören Szenarien, die von der Behandlung eines unerwarteten Antwortwerts von einem Dienst bis zum Ermitteln der Grundursache einer Ausnahme durch eine geschlossene Verbindung reichen.

Für die clientseitige Problembehandlung bieten die Azure-Clientbibliotheken für Java eine konsistente und robuste Protokollierungsstory, wie in [Konfigurieren der Protokollierung im Azure SDK für Java](logging-overview.md) beschrieben. Die Clientbibliotheken führen allerdings Netzwerkaufrufe über verschiedene Protokolle aus, was zu Problembehandlungsszenarien führen kann, die den hier bereitgestellten Problembehandlungsumfang sprengen. Wenn solche Probleme auftreten, besteht die Lösung darin, die in diesem Artikel beschriebenen externen Tools zur Diagnose von Netzwerkproblemen zu verwenden.

## <a name="fiddler"></a>Fiddler

[Fiddler](https://docs.telerik.com/fiddler-everywhere/introduction) ist ein HTTP-Debugproxy, der es ermöglicht, dass Anforderungen und Antworten, die durch ihn geleitet werden, unverändert zu protokollieren. Die „rohen“ Anforderungen und Antworten, die Sie aufzeichnen, können Sie bei der Problembehandlung von Szenarien helfen, in denen der Dienst eine unerwartete Anforderung erhält oder der Client eine unerwartete Antwort. Um Fiddler verwenden zu können, müssen Sie die Clientbibliothek mit einem HTTP-Proxy konfigurieren. Wenn Sie HTTPS verwenden, benötigen Sie eine zusätzliche Konfiguration, die erforderlich ist, um die entschlüsselten Anforderungs- und Antworttexte zu untersuchen.

### <a name="add-an-http-proxy"></a>Hinzufügen eines HTTP-Proxys

Um einen HTTP-Proxy hinzuzufügen, befolgen Sie die Anweisungen unter [Konfigurieren von Proxys im Azure SDK für Java](proxying.md). Stellen Sie sicher, dass Sie die Fiddler-Standardadresse für `localhost` an Port 8888 verwenden.

### <a name="enable-https-decryption"></a>Aktivieren der HTTPS-Entschlüsselung

Standardmäßig kann Fiddler nur HTTP-Datenverkehr aufzeichnen. Wenn Ihre Anwendung HTTPS verwendet, müssen Sie zusätzliche Schritte durchführen, um dem Zertifikat von Fiddler zu vertrauen, damit es HTTPS-Datenverkehr aufzeichnen darf. Weitere Informationen finden Sie unter [HTTPS-Menü](https://docs.telerik.com/fiddler-everywhere/user-guide/settings/https) in der Fiddler-Dokumentation.

In den folgenden Prozeduren wird beschrieben, wie Sie die Java Runtime Environment (JRE) verwenden, um dem Zertifikat zu vertrauen. Ohne dem Zertifikat zu vertrauen, kann eine HTTPS-Anforderung über Fiddler mit Sicherheitswarnungen fehlschlagen.

**Linux/macOS**

1. Exportieren Sie das Zertifikat von Fiddler.
1. Suchen Sie „keytool“ von JRE (in der Regel `jre/bin`).
1. Suchen Sie „cacert“ von JRE (in der Regel `jre/lib/security`).
1. Öffnen Sie ein Bash-Fenster, und führen Sie den folgenden Befehl aus, um das Zertifikat zu importieren:

   ```bash
   sudo keytool -import -file <location of Fiddler certificate> -keystore <location of cacert> -alias Fiddler
   ```

1. Geben Sie ein Kennwort ein.
1. Vertrauen Sie dem Zertifikat.

**Windows**

1. Exportieren Sie das Zertifikat von Fiddler.
1. Suchen Sie „keytool“ von JRE (in der Regel `jre\bin`).
1. Suchen Sie „cacert“ von JRE (in der Regel `jre\lib\security`).
1. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus, um das Zertifikat zu importieren:

   ```cmd
   keytool.exe -import -file <location of Fiddler certificate> -keystore <location of cacert> -alias Fiddler
   ```

1. Geben Sie ein Kennwort ein.
1. Vertrauen Sie dem Zertifikat.

## <a name="wireshark"></a>WireShark

[WireShark](https://www.wireshark.org/) ist ein Netzwerkprotokoll-Analysetool, das Netzwerkdatenverkehr aufzeichnen kann, ohne dass Änderungen am Anwendungscode erforderlich sind. WireShark ist hochgradig konfigurierbar und kann Netzwerkdatenverkehr auf niedriger Ebene breit und spezifisch erfassen. Diese Funktion ist nützlich für die Problembehandlung von Szenarien wie z. B. Remotehosts, die eine Verbindung schließen, oder wenn Verbindungen während eines Vorgangs geschlossen werden. Die GUI von WireShark zeigt Aufzeichnungen mithilfe eines Farbschemas an, das eindeutige Erfassungsfälle identifiziert, z. B. eine TCP-Neuübertragung, RST usw. Sie können Erfassungen auch filtern, entweder zur Aufzeichnungszeit oder während der Analyse.

### <a name="configure-a-capture-filter"></a>Konfigurieren eines Aufzeichnungsfilters

Aufzeichnungsfilter verringern die Anzahl von Netzwerkaufrufen, die für die Analyse aufgezeichnet werden. Ohne Aufzeichnungsfilter erfasst WireShark den gesamten Datenverkehr, der über eine Netzwerkschnittstelle geleitet wird. Dieses Verhalten kann riesige Datenmengen erzeugen, bei denen der größte Teil für die Untersuchung Rauschen sein kann. Die Verwendung eines Aufzeichnungsfilters hilft dabei, den Datenverkehr präventiv einzugrenzen, der aufgezeichnet wird, um so eine Untersuchung zielgerichtet zu gestalten. Weitere Informationen finden Sie unter [Aufzeichnen von Livenetzwerkdaten](https://www.wireshark.org/docs/wsug_html_chunked/ChapterCapture.html) in der WireShark-Dokumentation.

Im folgenden Beispiel wird ein Aufzeichnungsfilter hinzugefügt, um Netzwerkdatenverkehr aufzuzeichnen, der an einen bestimmten Host gesendet oder von diesem empfangen wird.

Navigieren Sie in WireShark zu **Aufzeichnen > Aufzeichnungsfilter...** , und fügen Sie einen neuen Filter mit dem Wert `host <host IP or hostname>` hinzu. Mit diesem Filter wird der Datenverkehr nur von und zu diesem Host aufgezeichnet. Wenn die Anwendung mit mehreren Hosts kommuniziert, können Sie mehrere Aufzeichnungsfilter hinzufügen, oder Sie können die Host-IP-Adresse/den Hostnamen mit dem Operator „OR“ hinzufügen, um eine weniger strenge Aufzeichnungsfilterung zu ermöglichen.

### <a name="capture-to-disk"></a>Aufzeichnen auf Datenträger

Möglicherweise müssen Sie eine Anwendung für einen längeren Zeitraum ausführen, um eine unerwartete Netzwerkausnahme zu reproduzieren und den Datenverkehr zu sehen, der zu ihr führt. Darüber hinaus ist es eventuell nicht möglich, alle Aufzeichnungen im Arbeitsspeicher zu behalten. Glücklicherweise kann WireShark Aufzeichnungen auf einem Datenträger protokollieren, sodass Sie für die Nachbearbeitung verfügbar sind. Bei diesem Ansatz wird das Risiko vermieden, dass der Arbeitsspeicher nicht ausreicht, während Sie ein Problem reproduzieren. Weitere Informationen finden Sie unter [Dateieingabe, -ausgabe und Drucken](https://www.wireshark.org/docs/wsug_html_chunked/ChapterIO.html) in der WireShark-Dokumentation.

Im folgenden Beispiel wird WireShark so eingerichtet, dass Aufzeichnungen in mehreren Dateien dauerhaft auf einem Datenträger gespeichert werden, wobei die Dateien entweder nach einer Aufzeichnung von 100 KB oder anhand der Größe von 50 MB aufgeteilt werden.

Navigieren Sie in WireShark zu **Erfassen > Optionen**, suchen Sie die Registerkarte **Ausgabe**, und geben Sie dann einen Dateinamen ein, der verwendet werden soll. Diese Konfiguration bewirkt, dass WireShark Aufzeichnungen dauerhaft in einer einzelnen Datei speichert. Wenn Sie die Aufzeichnung in mehreren Dateien aktivieren möchten, wählen Sie **Neue Datei automatisch erstellen** und dann **Nach 100000 Paketen** sowie **Nach 50 MB** aus. Durch diese Konfiguration muss WireShark eine neue Datei erstellen, wenn eins der Prädikate erfüllt ist. Jede neue Datei verwendet denselben Basisnamen wie der eingegebene Dateiname und fügt einen eindeutigen Bezeichner an. Wenn Sie die Anzahl der Dateien begrenzen möchten, die WireShark erstellen kann, wählen Sie **Ringpuffer mit X Dateien verwenden** aus. Diese Option beschränkt die Protokollierung durch WireShark auf die angegebene Anzahl von Dateien. Wenn diese Anzahl von Dateien erreicht wird, beginnt WireShark mit dem Überschreiben der Dateien, beginnend mit der ältesten.

### <a name="filter-captures"></a>Filtern von Aufzeichnungen

Manchmal ist es nicht möglich, den von WireShark aufgezeichneten Datenverkehr eng abgegrenzt zu erfassen, beispielsweise wenn Ihre Anwendung mit mehreren Hosts kommuniziert, die verschiedene Protokolle verwenden. In diesem Szenario, in der Regel bei Verwendung der oben beschriebenen dauerhaften Aufzeichnung, ist es einfacher, die Analyse nach der Netzwerkaufzeichnung auszuführen. WireShark unterstützt eine filterähnliche Syntax zum Analysieren von Aufzeichnungen. Weitere Informationen finden Sie unter [Arbeiten mit aufgezeichneten Paketen](https://www.wireshark.org/docs/wsug_html_chunked/ChapterWork.html) in der WireShark-Dokumentation.

Im folgenden Beispiel wird eine dauerhaft gespeicherte Aufzeichnungsdatei geladen und nach `ip.src_host==<IP>` gefiltert.

Navigieren Sie in WireShark zu **Datei > Öffnen**, und laden Sie eine dauerhaft gespeicherte Aufzeichnung von dem oben verwendeten Dateispeicherort. Nachdem die Datei geladen wurde, wird unterhalb der Menüleiste eine Filtereingabe angezeigt. Geben Sie in die Filtereingabe `ip.src_host==<IP>` ein. Dieser Filter schränkt die Aufzeichnungsansicht ein, sodass nur Aufzeichnungen angezeigt werden, bei denen die Quelle vom Host mit der IP-Adresse `<IP>` stammt.

## <a name="next-steps"></a>Nächste Schritte

Dieses Thema hat die Verwendung verschiedener Tools zur Diagnose von Netzwerkproblemen bei der Arbeit mit dem Azure SDK für Java behandelt. Nachdem Sie nun mit den allgemeinen Verwendungsszenarien vertraut sind, können Sie mit der Erkundung des SDK beginnen. Weitere Informationen zu den verfügbaren APIs finden Sie in den [Azure SDK für Java-Bibliotheken](azure-sdk-library-package-index.md).
