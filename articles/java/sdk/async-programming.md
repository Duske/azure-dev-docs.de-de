---
title: Asynchrone Programmierung im Azure SDK für Java
description: Übersicht des asynchronen Programmiermodells im Azure SDK für Java
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 64a692b10175a7c08ff8a32e56a638209f239c19
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528564"
---
# <a name="asynchronous-programming-in-the-azure-sdk-for-java"></a>Asynchrone Programmierung im Azure SDK für Java

In diesem Artikel wird das asynchrone Programmiermodell im Azure SDK für Java beschrieben.

Das Azure SDK enthielt anfänglich nur nicht blockierende, asynchrone APIs für die Interaktion mit Azure-Diensten. Mit diesen APIs können Sie das Azure SDK zum Erstellen skalierbarer Anwendungen verwenden, die Systemressourcen effizient nutzen. Allerdings enthält das [Azure SDK für Java](https://github.com/Azure/azure-sdk-for-java#client-new-releases) auch synchrone Clients, die sich an eine breitere Zielgruppe wenden. So werden unsere Clientbibliotheken auch für Benutzer zugänglich, die mit der asynchronen Programmierung nicht vertraut sind. (Weitere Informationen finden Sie unter [Zugänglich](https://azure.github.io/azure-sdk/general_introduction.html#approachable) („Approachable“) in den Entwurfsrichtlinien des Azure SDK.) Somit bieten alle Java-Clientbibliotheken im Azure SDK für Java sowohl asynchrone als auch synchrone Clients. Wir empfehlen jedoch, die asynchronen Clients für Produktionssysteme zu verwenden, um die Verwendung von Systemressourcen zu maximieren.

## <a name="reactive-streams"></a>Reaktive Streams

Wenn Sie sich den Abschnitt [Asynchrone Dienstclients](https://azure.github.io/azure-sdk/java_introduction.html#async-service-clients) in den [Java Azure SDK-Entwurfsrichtlinien](https://azure.github.io/azure-sdk/java_introduction.html) ansehen, werden Sie feststellen, dass unsere asynchronen APIs anstelle der von Java 8 bereitgestellten `CompletableFuture` reaktive Typen verwenden. Warum haben wir uns für reaktive Typen anstelle von Typen entschieden, die nativ in JDK verfügbar sind?

In Java 8 wurden Features wie [Streams](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html), [Lambdaausdrücke](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) und [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) eingeführt. Diese Features bieten zahlreiche Funktionen, unterliegen jedoch auch ein paar Einschränkungen.

`CompletableFuture` bietet rückrufbasierte, nicht blockierende Funktionen und die `CompletionStage`-Schnittstelle, die für das einfache Verfassen einer Reihe asynchroner Vorgänge zulässig ist. Lambdaausdrücke erleichtern die Lesbarkeit dieser pushbasierten APIs. Streams stellen funktionsartige Vorgänge bereit, um eine Sammlung von Datenelementen zu verarbeiten. Streams sind jedoch synchron und können nicht wiederverwendet werden. `CompletableFuture` gestattet Ihnen das Erstellen einer einzelnen Anforderung, bietet Unterstützung für einen Rückruf und erwartet eine _einzige_ Antwort. Viele Clouddienste erfordern aber die Möglichkeit, Daten zu streamen, z. B. Event Hubs.

Reaktive Streams können dabei helfen, diese Einschränkungen zu überwinden, indem Elemente von einer Quelle auf einen Abonnenten gestreamt werden. Wenn ein Abonnent Daten von einer Quelle anfordert, sendet die Quelle eine beliebige Anzahl von Ergebnissen zurück. Diese müssen nicht alle auf einmal gesendet werden. Die Übertragung erfolgt über einen Zeitraum immer dann, wenn die Quelle Daten besitzt, die gesendet werden können.

In diesem Modell registriert der Abonnent Ereignishandler, um Daten bei deren Ankunft zu verarbeiten. Diese pushbasierten Interaktionen benachrichtigen den Abonnenten über eindeutige Signale:

- Ein `onSubscribe()`-Aufruf zeigt an, dass die Datenübertragung gerade gestartet werden soll.
- Ein `onError()`-Aufruf zeigt an, dass ein Fehler aufgetreten ist, der auch das Ende der Datenübertragung kennzeichnet.
- Ein `onComplete()`-Aufruf zeigt den erfolgreichen Abschluss der Datenübertragung an.

Im Gegensatz zu Java Streams behandeln reaktive Streams Fehler als Ereignisse der ersten Klasse. Reaktive Streams verfügen über einen dedizierten Kanal für die Quelle, um jegliche Fehler an den Abonnenten zu kommunizieren. Außerdem ermöglichen reaktive Streams dem Abonnenten das Aushandeln der Datenübertragungsrate, um diese Streams in ein Push/Pull-Modell umzuwandeln.

Die Spezifikation für [reaktive Streams](https://github.com/reactive-streams/reactive-streams-jvm#reactive-streams) stellt einen Standard für die Art der Übertragung von Daten bereit. Die Spezifikation definiert auf allgemeiner Ebene die folgenden vier Schnittstellen und legt Regeln für die Implementierung dieser Schnittstellen fest.

- **Herausgeber** ist die Quelle eines Datenstroms.
- **Abonnent** ist der Verbraucher eines Datenstroms.
- Das **Abonnement** verwaltet den Zustand der Datenübertragung zwischen einem Herausgeber und einem Abonnenten.
- Der **Prozessor** ist sowohl ein Herausgeber als auch ein Abonnent.

Es gibt einige bekannte Java-Bibliotheken, die Implementierungen dieser Spezifikation bereitstellen, z. B. [RxJava](https://github.com/ReactiveX/RxJava), [Akka Streams](https://doc.akka.io/docs/akka/current/stream/stream-introduction.html), [Vert.x](https://vertx.io/docs/#reactive) und [Project Reactor](https://projectreactor.io/docs/core/release/reference/).

Das Azure SDK für Java hat Project Reactor integriert, um asynchrone APIs bereitzustellen. Der Hauptfaktor für diese Entscheidung bestand darin, eine reibungslose Integration mit [Spring Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html) bereitzustellen, das auch Project Reactor verwendet. Ein weiterer Faktor für die Entscheidung für Project Reactor anstelle von RxJava war, dass Project Reactor Java 8 verwendet, während RxJava zu diesem Zeitpunkt noch Java 7 verwendete. Project Reactor bietet außerdem einen umfangreichen Satz von Operatoren, die zusammensetzbar sind und es Ihnen ermöglichen, deklarativen Code zum Erstellen von Datenverarbeitungspipelines zu schreiben. Ein weiterer angenehmer Vorteil von Project Reactor ist, dass es über Adapter zum Konvertieren von Project Reactor-Typen in andere gängige Implementierungstypen verfügt.

## <a name="comparing-apis-of-synchronous-and-asynchronous-operations"></a>Vergleich von APIs synchroner und asynchroner Vorgänge

Wir haben die synchronen Clients und Optionen für asynchrone Clients diskutiert. In der folgenden Tabelle wird zusammengefasst, wie APIs aussehen, die mithilfe dieser Optionen entworfen wurden:

| API-Typ                                           | Kein Wert                   | Einzelwert            | Mehrere Werte                         |
|----------------------------------------------------|----------------------------|-------------------------|-------------------------------|
| Standard Java – synchrone APIs                   | `void`                     | `T`                     | `Iterable<T>`                 |
| Standard Java – asynchrone APIs                  | `CompletableFuture<Void>`  | `CompletableFuture<T>`  | `CompletableFuture<List<T>>`   |
| Schnittstellen für reaktive Streams                        | `Publisher<Void>`          | `Publisher<T>`          | `Publisher<T>`                 |
| Project Reactor-Implementierung von reaktiven Streams | `Mono<Void>`               | `Mono<T>`               | `Flux<T>`                      |

Aus Gründen der Vollständigkeit ist es erwähnenswert, dass Java 9 die [Flow](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/Flow.html)-Klasse eingeführt hat, die die vier Schnittstellen für reaktive Streams enthält. Diese Klasse enthält jedoch keine Implementierung.

## <a name="use-async-apis-in-the-azure-sdk-for-java"></a>Verwenden asynchroner APIs im Azure SDK für Java

Die Spezifikation für reaktive Streams unterscheidet nicht zwischen den Typen von Herausgebern. In der Spezifikation für reaktive Streams erzeugen Herausgeber null oder mehr Datenelemente. In vielen Fällen gibt es eine nützliche Unterscheidung zwischen einem Herausgeber, der höchstens ein Datenelement erzeugt, und einem Herausgeber, der null oder mehr erzeugt. In cloudbasierten APIs zeigt diese Unterscheidung an, ob eine Anforderung eine einwertige Antwort oder eine Sammlung zurückgibt. Project Reactor bietet zwei Typen, um diese Unterscheidung zu treffen: [Mono](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Mono.html) und [Flux](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html). Eine API, die einen `Mono` zurückgibt, enthält eine Antwort mit höchstens einem Wert, und eine API, die einen `Flux` zurückgibt, enthält eine Antwort, die null oder mehr Werte enthält.

Angenommen, Sie verwenden beispielsweise einen [ConfigurationAsyncClient](/java/api/com.azure.data.appconfiguration.configurationasyncclient), um eine mit dem Azure App Configuration-Dienst gespeicherte Konfiguration abzurufen. (Weitere Informationen finden Sie unter [Was ist Azure App Configuration?](/azure/azure-app-configuration/overview).)

Wenn Sie einen `ConfigurationAsyncClient` erstellen und `getConfigurationSetting()` auf dem Client aufrufen, gibt er einen `Mono` zurück, der anzeigt, dass die Antwort einen einzelnen Wert enthält. Das Aufrufen dieser Methode alleine bewirkt jedoch gar nichts. Der Client hat noch keine Anforderung an den Azure App Configuration-Dienst gesendet. Zu diesem Zeitpunkt ist die von dieser API zurückgegebene `Mono<ConfigurationSetting>` lediglich eine „Assembly“ der Datenverarbeitungspipeline. Dies bedeutet, dass die erforderliche Einrichtung für die Verwendung der Daten abgeschlossen ist. Um die Datenübertragung tatsächlich auszulösen (d. h., um die Anforderung an den Dienst zu senden und die Antwort zu erhalten), müssen Sie den zurückgegebenen `Mono` abonnieren. Daher müssen Sie beim Umgang mit diesen reaktiven Streams daran denken, `subscribe()` aufzurufen, weil nichts passiert, bis Sie dies tun.

Im folgenden Beispiel wird gezeigt, wie Sie den `Mono` abonnieren und den Konfigurationswert in der Konsole ausgeben.

```java
ConfigurationAsyncClient asyncClient = new ConfigurationClientBuilder()
    .connectionString("<your connection string>")
    .buildAsyncClient();

asyncClient.getConfigurationSetting("<your config key>", "<your config value>").subscribe(
    config -> System.out.println("Config value: " + config.getValue()),
    ex -> System.out.println("Error getting configuration: " + ex.getMessage()),
    () -> System.out.println("Successfully retrieved configuration setting"));

System.out.println("Done");
```

Beachten Sie, dass der Beispielcode nach dem Aufrufen von `getConfigurationSetting()` auf dem Client das Ergebnis abonniert und drei separate Lambdaausdrücke bereitstellt. Der erste Lambdaausdruck verwendet Daten, die vom Dienst empfangen werden, und wird bei erfolgreicher Antwort ausgelöst. Der zweite Lambdaausdruck wird ausgelöst, wenn beim Abrufen der Konfiguration ein Fehler auftritt. Der dritte Lambdaausdruck wird aufgerufen, wenn der Datenstrom abgeschlossen ist, was bedeutet, dass von diesem Stream keine weiteren Datenelemente mehr erwartet werden.

> [!NOTE]
> Wie bei jeder asynchronen Programmierung wird die Ausführung nach der Erstellung des Abonnements wie gewohnt fortgesetzt. Wenn das Programm von nichts aktiv gehalten und weiterhin ausgeführt wird, wird es möglicherweise beendet, bevor der asynchrone Vorgang abgeschlossen ist. Der Hauptthread, der `subscribe()` aufgerufen hat, wartet nicht, bis Sie den Netzwerkaufruf an Azure App Configuration tätigen und eine Antwort empfangen. In Produktionssystemen könnten Sie mit der Verarbeitung von etwas anderem fortfahren, aber in diesem Beispiel können Sie eine kleine Verzögerung hinzufügen, indem Sie `Thread.sleep()` aufrufen oder ein `CountDownLatch` verwenden, um dem asynchronen Vorgang die Möglichkeit zu geben, die Verarbeitung abzuschließen.

Wie im folgenden Beispiel gezeigt, folgen auch APIs, die einen `Flux` zurückgeben, einem ähnlichen Muster. Der Unterschied besteht darin, dass der erste Rückruf, der der `subscribe()`-Methode bereitgestellt wird, für jedes Datenelement in der Antwort mehrmals aufgerufen wird. Der Fehler oder die Abschlussrückrufe werden genau einmal aufgerufen und als Beendigungssignale betrachtet. Es werden keine anderen Rückrufe mehr aufgerufen, wenn eines dieser Signale vom Herausgeber empfangen wird.

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(
    event -> System.out.println("Sequence number of received event: " + event.getData().getSequenceNumber()),
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));
```

### <a name="backpressure"></a>Rückstaus

Was geschieht, wenn die Quelle die Daten schneller produziert, als der Abonnent sie verarbeiten kann? Der Abonnent kann mit Daten überlastet werden, was zu Fehlern aufgrund nicht ausreichenden Arbeitsspeichers führen kann. Der Abonnent benötigt eine Möglichkeit für Rückmeldungen an den Herausgeber, um die Übertragung zu verlangsamen, wenn er nicht Schritt halten kann. Wenn Sie `subscribe()` für einen `Flux` aufrufen, wie im obigen Beispiel gezeigt, fordert der Abonnent standardmäßig einen unbegrenzten Datenstrom an, was dem Herausgeber signalisiert, dass die Daten so schnell wie möglich gesendet werden sollen. Dieses Verhalten ist nicht immer wünschenswert, und der Abonnent muss möglicherweise die Herausgaberate mittels „Rückstau“ steuern. Rückstau erlaubt dem Abonnenten, die Kontrolle über den Fluss von Datenelementen zu übernehmen. Ein Abonnent fordert eine begrenzte Anzahl von Datenelementen an, die er verarbeiten kann. Nachdem der Abonnent die Verarbeitung dieser Elemente abgeschlossen hat, kann der Abonnent weitere anfordern. Mithilfe von „Rückstau“ können Sie ein Push-Modell für die Datenübertragung in ein Push/Pull-Modell umwandeln.

Im folgenden Beispiel wird gezeigt, wie Sie die Rate steuern können, mit der Ereignisse vom Event Hubs-Consumer empfangen werden:

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.request(1); // request another event when the subscriber is ready
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

Wenn der Abonnent zum ersten Mal eine „Verbindung“ mit dem Herausgeber herstellt, übergibt der Herausgeber den Abonnenten an eine `Subscription`-Instanz, die den Zustand der Datenübertragung verwaltet. Dieses `Subscription` ist das Medium, über das der Abonnent Rückdruckstau anwenden kann, indem er `request()` aufruft, um anzugeben, wie viele weitere Datenelemente er verarbeiten kann.

Wenn der Abonnent jedes Mal, wenn er `onNext()`aufruft, mehr als ein Datenelement anfordert, z. B. `request(10)`, sendet der Herausgeber die nächsten 10 Elemente sofort, wenn sie verfügbar sind, oder sobald sie verfügbar werden. Diese Elemente werden in einem Puffer beim Abonnenten gesammelt, und da jeder `onNext()`-Aufruf 10 weitere Elemente anfordert, wächst der Rückstand so lange, bis entweder der Herausgeber keine weiteren zu sendenden Datenelemente mehr hat, oder bis der Puffer des Abonnenten überläuft, was zu Fehlern aufgrund nicht ausreichenden Arbeitsspeichers führt.

### <a name="cancel-a-subscription"></a>Kündigen eines Abonnements

Ein Abonnement verwaltet den Zustand der Datenübertragung zwischen einem Herausgeber und einem Abonnenten. Das Abonnement ist aktiv, bis der Herausgeber die Übertragung aller Daten an den Abonnenten abgeschlossen hat, oder bis der Abonnent nicht mehr am Empfang von Daten interessiert ist. Es gibt eine Reihe von Möglichkeiten, wie Sie ein Abonnement kündigen können, wie unten gezeigt.

Im folgenden Beispiel wird das Abonnement durch Beseitigen des Abonnenten gekündigt:

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

Disposable disposable = asyncClient.receive().subscribe(
    partitionEvent -> {
        Long num = partitionEvent.getData().getSequenceNumber()
        System.out.println("Sequence number of received event: " + num);
    },
    ex -> System.out.println("Error receiving events: " + ex.getMessage()),
    () -> System.out.println("Successfully completed receiving all events"));

// much later on in your code, when you are ready to cancel the subscription,
// you can call the dispose method, as such:
disposable.dispose();
```

Im folgenden Beispiel wird das Abonnement durch Aufrufen der `cancel()`-Methode für das `Subscription` gekündigt:

```java
EventHubConsumerAsyncClient asyncClient = new EventHubClientBuilder()
    .connectionString("<your connection string>")
    .consumerGroup("<your consumer group>")
    .buildAsyncConsumerClient();

asyncClient.receive().subscribe(new Subscriber<PartitionEvent>() {
    private Subscription subscription;

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
        this.subscription.request(1); // request 1 data element to begin with
    }

    @Override
    public void onNext(PartitionEvent partitionEvent) {
        System.out.println("Sequence number of received event: " + partitionEvent.getData().getSequenceNumber());
        this.subscription.cancel(); // Cancels the subscription. No further event is received.
    }

    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error receiving events: " + throwable.getMessage());
    }

    @Override
    public void onComplete() {
        System.out.println("Successfully completed receiving all events")
    }
});
```

## <a name="conclusion"></a>Zusammenfassung

Threads sind kostspielige Ressourcen, die Sie nicht beim Warten auf Antworten von Remotedienstaufrufen verschwenden sollten. Wenn die Einführung von Microservicesarchitekturen zunimmt, wird es entscheidend, Ressourcen effizient zu skalieren und zu nutzen. Asynchrone APIs sind vorteilhaft, wenn netzwerkgebundene Vorgänge vorliegen. Das Azure SDK für Java bietet einen umfangreichen Satz an APIs für asynchrone Vorgänge, um Ihnen bei der Maximierung Ihrer Systemressourcen zu helfen. Wir empfehlen Ihnen dringend, unsere asynchronen Clients auszuprobieren.

Weitere Informationen zu den Operatoren, die sich am besten für Ihre spezifischen Aufgaben eignen, finden Sie unter [Welchen Operator benötige ich?](https://projectreactor.io/docs/core/release/reference/#which-operator) im [Reactor 3-Referenzhandbuch](https://projectreactor.io/docs/core/release/reference/index.html).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die verschiedenen Konzepte der asynchronen Programmierung besser verstehen, ist es wichtig zu erfahren, wie die Ergebnisse durchlaufen werden. Weitere Informationen zu den besten Iterationsstrategien sowie Details zur Funktionsweise der Paginierung finden Sie unter [Paginierung und Iteration im Azure SDK für Java](pagination.md).
