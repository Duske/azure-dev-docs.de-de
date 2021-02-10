---
title: Paginierung und Iteration im Azure SDK für Java
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit Paginierung und Iteration
author: anuchandy
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: anuchan
ms.openlocfilehash: 0cb9e519931d24dcd97aa4a52742974427df5969
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528481"
---
# <a name="pagination-and-iteration-in-the-azure-sdk-for-java"></a>Paginierung und Iteration im Azure SDK für Java

Dieser Artikel bietet einen Überblick darüber, wie Sie das Azure SDK für die Java-Funktionen zur Paginierung und Iteration verwenden können, um effizient und produktiv mit großen Datasets zu arbeiten.

Viele der von den Clientbibliotheken im Azure Java SDK bereitgestellten Vorgänge geben mehr als ein Ergebnis zurück. Das Azure Java SDK definiert einen Satz akzeptabler Rückgabetypen in diesen Fällen, um sicherzustellen, dass die Entwicklererfahrung durch Konsistenz optimiert wird. Die verwendeten Rückgabetypen sind `PagedIterable` für synchrone APIs und `PagedFlux` für asynchrone APIs. Die APIs unterscheiden sich leicht hinsichtlich ihrer unterschiedlichen Anwendungsfälle, besitzen aber konzeptionell dieselben Anforderungen:

- Ermöglichen der einfachen Iteration über jedes einzelne Element in der Sammlung, wobei jegliche Erfordernis von manueller Paginierung oder Nachverfolgung von Fortsetzungstoken ignoriert wird. Sowohl `PagedIterable` als auch `PagedFlux` erleichtern diese Aufgabe, indem die Iteration über eine paginierte Antwort erfolgt, die in den angegebenen Typ `T` deserialisiert wurde. `PagedIterable` implementiert die `Iterable`-Schnittstelle und bietet eine API zum Abrufen eines `Stream`, während `PagedFlux` einen `Flux` bietet. In allen Fällen ist der Vorgang der Paginierung transparent, und die Iteration wird fortgesetzt, während noch Ergebnisse vorhanden sind, die noch iteriert werden müssen.

- Ermöglichen der explizit seitenweisen Iteration. Hierdurch können Sie besser verstehen, wann Anforderungen gestellt werden, und Sie können auf Antwortinformationen pro Seite zugreifen. Sowohl `PagedIterable` als auch `PagedFlux` besitzen Methoden, die Typen zurückgeben, die für die seitenweise Iteration geeignet sind, anstatt über einzelne Elemente.

Dieser Artikel ist aufgeteilt zwischen den synchronen und asynchronen APIs des Java Azure SDK. Die synchronen Iterations-APIs sehen Sie, wenn Sie mit synchronen Clients arbeiten, und die asynchronen Iterations-APIs, wenn Sie mit asynchronen Clients arbeiten.

## <a name="synchronous-pagination-and-iteration"></a>Synchrone Paginierung und Iteration

In diesem Abschnitt werden die synchronen APIs behandelt.

### <a name="iterate-over-individual-elements"></a>Iterieren über einzelne Elemente

Wie bereits erwähnt, besteht der gängigste Anwendungsfall in der Iteration über jedes einzelne Element, anstatt seitenweise. Die folgenden Codebeispiele veranschaulichen, wie Ihnen die `PagedIterable`-API gestattet, Ihren bevorzugten Iterationstyp zu verwenden, um diese Funktionalität zu implementieren.

#### <a name="use-a-for-each-loop"></a>Verwenden einer „For Each“-Schleife

Da `PagedIterable` `Iterable` implementiert, können Sie durch alle Elemente iterieren, wie im folgenden Beispiel gezeigt:

```java
PagedIterable<Secret> secrets = client.listSecrets();
for (Secret secret : secrets) {
   System.out.println("Secret is: " + secret);
}
```

#### <a name="use-stream"></a>Verwenden von Streams

Da für `PagedIterable` eine `stream()`-Methode definiert ist, können Sie diese aufrufen, um die Standardstream-APIs von Java zu verwenden, wie im folgenden Beispiel gezeigt:

```java
client.listSecrets()
      .stream()
      .forEach(secret -> System.out.println("Secret is: " + secret));
```

#### <a name="use-iterator"></a>Verwenden des Iterators

Da `PagedIterable` `Iterable` implementiert, besitzt es auch eine `iterator()`-Methode, die den Iterator-Programmierstil von Java ermöglicht, wie im folgenden Beispiel gezeigt:

```java
Iterator<Secret> secrets = client.listSecrets().iterator();
while (it.hasNext()) {
   System.out.println("Secret is: " + it.next());
}
```

### <a name="iterate-over-pages"></a>Iterieren über Seiten

Wenn Sie mit einzelnen Seiten arbeiten, können Sie seitenweise iterieren, z. B. wenn Sie HTTP-Antwortinformationen benötigen, oder wenn Fortsetzungstoken wichtig sind, um den Verlauf zu erhalten. Unabhängig davon, ob Sie seitenweise oder elementweise iterieren, gibt es bei der Leistung oder der Anzahl der vom Dienst ausgeführten Aufrufe keinen Unterschied. Die zugrunde liegende Implementierung lädt die nächste Seite auf Anforderung, und wenn Sie das Abonnement von `PagedFlux` kündigen, was jederzeit möglich ist, erfolgen keine weiteren Aufrufe an den Dienst.

#### <a name="use-a-for-each-loop"></a>Verwenden einer „For Each“-Schleife

Wenn Sie `listSecrets()` aufrufen, erhalten Sie ein `PagedIterable`, das eine `iterableByPage()`-API besitzt. Diese API erzeugt eine `Iterable<PagedResponse<Secret>>` anstelle eines `Iterable<Secret>`. Die `PagedResponse` bietet die Antwortmetadaten sowie Zugriff auf das Fortsetzungstoken, wie im folgenden Beispiel gezeigt:

```java
Iterable<PagedResponse<Secret>> secretPages = client.listSecrets().iterableByPage();
for (PagedResponse<Secret> page : secretPages) {
   System.out.println("Response code: " + page.getStatusCode());
   System.out.println("Continuation Token: " + page.getContinuationToken());
   page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
}
```

Außerdem gibt es eine `iterableByPage`-Überladung, die ein Fortsetzungstoken akzeptiert. Sie können diese Überladung aufrufen, wenn Sie zu einem späteren Zeitpunkt zum selben Punkt in der Iteration zurückkehren möchten.

#### <a name="use-stream"></a>Verwenden von Streams

Im folgenden Beispiel wird gezeigt, wie die `streamByPage()`-Methode denselben Vorgang wie den oben gezeigten ausführt. Diese API verfügt auch über eine Fortsetzungstokenüberladung, um zu einem späteren Zeitpunkt zum selben Punkt in der Iteration zurückzukehren.

```java
client.listSecrets()
      .streamByPage()
      .forEach(page -> {
          System.out.println("Response code: " + page.getStatusCode());
          System.out.println("Continuation Token: " + page.getContinuationToken());
          page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
      });
```

## <a name="asynchronously-observe-pages-and-individual-elements"></a>Asynchrones Beobachten von Seiten und einzelnen Elementen

In diesem Abschnitt werden die asynchronen APIs behandelt. Bei asynchronen APIs erfolgen die Netzwerkaufrufe in einem anderen Thread als dem Hauptthread, der `subscribe()` aufruft. Dies bedeutet, dass der Hauptthread beendet werden kann, bevor das Ergebnis verfügbar ist. Es liegt an Ihnen sicherzustellen, dass die Anwendung nicht beendet wird, bevor der asynchrone Vorgang beendet werden konnte.

### <a name="observe-individual-elements"></a>Beobachten einzelner Elemente

Das folgende Beispiel zeigt, wie Sie mit der `PagedFlux`-API einzelne Elemente asynchron beobachten können. Es gibt verschiedene Möglichkeiten, um einen Flux-Typ zu abonnieren. Weitere Informationen finden Sie unter [Einfache Möglichkeiten zum Erstellen eines Flux oder Mono und zu dessen Abonnierung](https://projectreactor.io/docs/core/release/reference/#_simple_ways_to_create_a_flux_or_mono_and_subscribe_to_it) im [Reaktor 3-Referenzhandbuch](https://projectreactor.io/docs/core/release/reference). Bei diesem Beispiel handelt es sich um eine Variante, bei der drei Lambdaausdrücke vorhanden sind, jeweils einer für den Consumer, für den Fehlerconsumer und den Abschlussconsumer. Es ist bewährte Praxis, alle drei zu verwenden, doch in manchen Fällen ist es nur notwendig, den Consumer und vielleicht noch den Fehlerconsumer zu verwenden.

 ```java
asyncClient.listSecrets()
    .subscribe(secret -> System.out.println("Secret value: " + secret),
        ex -> System.out.println("Error listing secrets: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all secrets"));
 ```

### <a name="observe-pages"></a>Beobachten von Seiten

 Das folgende Beispiel zeigt, wie Sie mit der `PagedFlux`-API jede Seite asynchron beobachten können, wiederum durch Verwendung einer `byPage()`-API und durch Bereitstellen eines Consumers, Fehlerconsumers und Abschlussconsumers.

  ```java
asyncClient.listSecrets().byPage()
    .subscribe(page -> {
            System.out.println("Response code: " + page.getStatusCode());
            System.out.println("Continuation Token: " + page.getContinuationToken());
            page.getElements().forEach(secret -> System.out.println("Secret value: " + secret))
        },
        ex -> System.out.println("Error listing pages with secret: " + ex.getMessage()),
        () -> System.out.println("Successfully listed all pages with secret"));
 ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit Paginierung und Iteration im Azure ADK für Java vertraut sind, sollten Sie [Zeitintensive Vorgänge im Azure SDK für Java](lro.md) lesen. Zeitintensive Vorgänge sind Vorgänge, deren Ausführung länger dauert, als die der meisten normalen HTTP-Anforderungen, normalerweise, weil der Aufwand auf Serverseite höher ist.
