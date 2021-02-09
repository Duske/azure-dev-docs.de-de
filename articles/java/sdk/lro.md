---
title: Zeitintensive Vorgänge im Azure SDK für Java
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit zeitintensiven Vorgängen
author: anuchandy
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: anuchan
ms.openlocfilehash: a20fb7a638fefd0182bea89e3d5c4555442a4547
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528488"
---
# <a name="long-running-operations-in-the-azure-sdk-for-java"></a>Zeitintensive Vorgänge im Azure SDK für Java

Dieser Artikel bietet eine Übersicht über die Verwendung zeitintensiver Vorgänge mit dem Azure SDK für Java.

Bestimmte Vorgänge in Azure können längere Zeit in Anspruch nehmen. Diese Vorgänge liegen außerhalb des standardmäßigen HTTP-Stils von schnellem Anforderung/Antwort-Flow. Beispielsweise sind das Kopieren von Daten aus einer Quell-URL in ein Speicherblob oder das Trainieren eines Modells zum Erkennen von Formularen Vorgänge, die zwischen einigen Sekunden und mehreren Minuten in Anspruch nehmen können. Solche Vorgänge werden als „zeitintensive Vorgänge“ bezeichnet und im Englischen häufig als „LRO“ (Long Running Operations) abgekürzt. Ein LRO kann Sekunden, Minuten, Stunden, Tage oder länger in Anspruch nehmen, je nach angefordertem Vorgang und dem Prozess, der serverseitig ausgeführt werden muss.

In den Java-Clientbibliotheken für Azure gibt es eine Konvention, dass alle zeitintensiven Vorgänge mit dem Präfix `begin` beginnen. Dieses Präfix zeigt an, dass die Ausführung dieses Vorgangs zeitintensiv ist und dass die Möglichkeiten der Interaktion mit diesem Vorgang etwas von dem üblichen Anforderung/Antwort-Flow abweichen. Zusammen mit dem Präfix `begin` unterscheidet sich auch der Rückgabetyp des Vorgangs vom üblichen Typ, um den vollständigen Umfang der Funktionen eines zeitintensiven Vorgangs zu ermöglichen. Wie bei den meisten Dingen im Azure SDK für Java gibt es sowohl synchrone als auch asynchrone APIs für zeitintensive Vorgänge:

* Bei synchronen Clients geben zeitintensive Vorgänge eine `SyncPoller`-Instanz zurück.
* Bei asynchronen Clients geben zeitintensive Vorgänge eine `PollerFlux`-Instanz zurück.

Sowohl `SyncPoller` als auch `PollerFlux` sind die clientseitigen Abstraktionen, die die Interaktion mit serverseitigen zeitintensiven Vorgängen vereinfachen sollen. Im weiteren Verlauf dieses Artikels werden bewährte Methoden für die Arbeit mit diesen Typen beschrieben.

## <a name="synchronous-long-running-operations"></a>Synchrone zeitintensive Vorgänge

Das Aufrufen einer API, die einen `SyncPoller` zurückgibt, startet sofort den zeitintensiven Vorgang. Die API gibt den `SyncPoller` sofort zurück, sodass Sie den Status des zeitintensiven Vorgangs überwachen und das Endergebnis abrufen können. Im folgenden Beispiel wird gezeigt, wie Sie den Status eines zeitintensiven Vorgangs mithilfe des `SyncPoller`überwachen.

```java
SyncPoller<UploadBlobProgress, UploadedBlobProperties> poller = syncClient.beginUploadFromUri(<URI to upload from>)
PollResponse<UploadBlobProgress> response;

do {
    response = poller.poll();
    System.out.println("Status of long running upload operation: " + response.getStatus());
    Duration pollInterval = response.getRetryAfter();
    TimeUnit.MILLISECONDS.sleep(pollInterval.toMillis());
} while (!response.getStatus().isComplete());
```

In diesem Beispiel wird die `poll()`-Methode mit dem `SyncPoller` verwendet, um Informationen zum Status des zeitintensiven Vorgangs abzurufen. Mit diesem Code wird der Status in der Konsole ausgegeben, aber eine bessere Implementierung würde relevante Entscheidungen auf Grundlage dieses Status treffen.

Die `getRetryAfter()`-Methode gibt Informationen darüber zurück, wie lange vor der nächsten Abfrage gewartet werden soll. Die meisten zeitintensiven Azure-Vorgänge geben die Abrufverzögerung als Teil ihrer HTTP-Antwort zurück (d. h. der häufig verwendete `retry-after`-Header). Wenn die Antwort die Abrufverzögerung nicht enthält, gibt die `getRetryAfter()`-Methode die Dauer zurück, die zum Zeitpunkt des Aufrufs des zeitintensiven Vorgangs angegeben wurde.

Im obigen Beispiel wird eine `do..while`-Schleife verwendet, um wiederholt abzufragen, bis der zeitintensive Vorgang abgeschlossen ist. Wenn Sie an diesen Zwischenergebnissen nicht interessiert sind, können Sie stattdessen `waitForCompletion()` aufrufen. Dieser Aufruf blockiert den aktuellen Thread, bis der zeitintensive Vorgang abgeschlossen ist und die letzte Abrufantwort zurückgibt:

```java
PollResponse<UploadBlobProgress> response = poller.waitForCompletion();
```

Wenn die letzte Abfrageantwort anzeigt, dass der zeitintensive Vorgang erfolgreich abgeschlossen wurde, können Sie das Endergebnis mithilfe von `getFinalResult()` abrufen:

```java
if (LongRunningOperationStatus.SUCCESSFULLY_COMPLETED == response.getStatus()) {
    UploadedBlobProperties result = poller.getFinalResult();
}
```

Weitere nützliche APIs in `SyncPoller` sind unter anderem:

1. `waitForCompletion(Duration)`: Wartet für die angegebene Timeoutdauer auf den Abschluss des zeitintensiven Vorgangs.
1. `waitUntil(LongRunningOperationStatus)`: Wartet, bis der angegebene Status des zeitintensiven Vorgangs empfangen wurde.
1. `waitUntil(LongRunningOperationStatus, Duration)`: Wartet, bis der angegebene Status des zeitintensiven Vorgangs empfangen wurde, oder bis die angegebene Timeoutdauer abgelaufen ist.

## <a name="asynchronous-long-running-operations"></a>Asynchrone zeitintensive Vorgänge

Das folgende Beispiel zeigt, wie Sie mit dem `PollerFlux` einen zeitintensiven Vorgang beobachten können. Bei asynchronen APIs erfolgen die Netzwerkaufrufe in einem anderen Thread als dem Hauptthread, der `subscribe()` aufruft. Dies bedeutet, dass der Hauptthread beendet werden kann, bevor das Ergebnis verfügbar ist. Es liegt an Ihnen sicherzustellen, dass die Anwendung nicht beendet wird, bevor der asynchrone Vorgang beendet werden konnte.

Die asynchrone API gibt sofort einen `PollerFlux` zurück, aber der zeitintensive Vorgang selbst wird erst gestartet, nachdem Sie den `PollerFlux` abonniert haben. Dieser Prozess funktioniert wie alle `Flux`-basierten APIs. Das folgende Beispiel zeigt einen asynchronen zeitintensiven Vorgang:

```java
asyncClient.beginUploadFromUri(...)
    .subscribe(response -> System.out.println("Status of long running upload operation: " + response.getStatus()));
```

Im folgenden Beispiel erhalten Sie Zwischenstatusaktualisierungen für den zeitintensiven Vorgang. Sie können diese Aktualisierungen verwenden, um zu bestimmen, ob der zeitintensive Vorgang weiterhin in der erwarteten Weise funktioniert. Mit diesem Beispiel wird der Status in der Konsole ausgegeben, aber eine bessere Implementierung würde relevante Entscheidungen zur Fehlerbehandlung auf Grundlage dieses Status treffen.

Wenn Sie an den Zwischenstatusaktualisierungen nicht interessiert sind und nur über das Endergebnis informiert werden möchten, sobald dieses eingeht, können Sie Code verwenden, der dem folgenden Beispiel ähnelt:

```java
asyncClient.beginUploadFromUri(...)
    .last()
    .flatMap(response -> {
        if (LongRunningOperationStatus.SUCCESSFULLY_COMPLETED == response.getStatus()) {
            return response.getFinalResult();
        }
        return Mono.error(new IllegalStateException("Polling completed unsuccessfully with status: "+ response.getStatus()));
    })
    .subscribe(
        finalResult -> processFormPages(finalResult),
        ex -> countDownLatch.countDown(),
        () -> countDownLatch.countDown());
```

In diesem Code Rufen Sie das Endergebnis des zeitintensiven Vorgangs durch Aufrufen von `last()` ab. Dieser Aufruf teilt dem `PollerFlux` mit, dass Sie auf den Abschluss aller Abrufe warten möchten. Zu diesem Zeitpunkt hat dann der zeitintensive Vorgang einen Endzustand erreicht, und Sie können seinen Status überprüfen, um das Ergebnis zu bestimmen. Wenn der Poller anzeigt, dass der zeitintensive Vorgang erfolgreich abgeschlossen wurde, können Sie das Endergebnis abrufen und an den Consumer im subscribe-Aufruf übergeben.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den zeitintensiven APIs im Azure SDK für Java vertraut sind, finden Sie Informationen zum weiteren Anpassen des HTTP-Clients unter [Konfigurieren von Proxys im Azure SDK für Java](proxying.md).
