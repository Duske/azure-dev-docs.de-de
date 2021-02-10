---
title: HTTP-Clients und -Pipelines im Azure SDK für Java
description: Übersicht über die HTTP-Client- und Pipelinefunktionen im Azure SDK für Java
author: srnagar
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: srnagar
ms.openlocfilehash: 93595096bc6d9fde1226f6875b866ea28b0a85bb
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528535"
---
# <a name="http-clients-and-pipelines-in-the-azure-sdk-for-java"></a>HTTP-Clients und -Pipelines im Azure SDK für Java

Dieser Artikel bietet eine Übersicht über die HTTP-Client- und Pipelinefunktionen im Azure SDK für Java. Diese Funktionen bieten für Entwickler, die Azure SDK für Java-Bibliotheken verwenden, eine konsistente, leistungsstarke und flexible Benutzeroberfläche.

## <a name="http-clients"></a>HTTP-Clients

Das Azure SDK für Java wird mithilfe einer `HttpClient`-Abstraktion implementiert. Diese Abstraktion ermöglicht eine austauschbare Architektur, die mehrere HTTP-Clientbibliotheken oder benutzerdefinierte Implementierungen akzeptiert. Um die Abhängigkeitsverwaltung jedoch für die meisten Benutzer zu vereinfachen, sind alle Azure-Clientbibliotheken von `azure-core-http-netty` abhängig. Daher ist der [Netty](https://netty.io)-HTTP-Client der Standardclient, der in allen Azure SDK für Java-Bibliotheken verwendet wird.

Obwohl Netty der HTTP-Standardclient ist, stellt das SDK drei Clientimplementierungen abhängig davon bereit, welche Abhängigkeiten bereits in Ihrem Projekt vorhanden sind. Diese Implementierungen beziehen sich auf:

* [Netty](https://netty.io)
* [OkHttp](https://square.github.io/okhttp/)
* Den neuen [HttpClient](https://openjdk.java.net/groups/net/httpclient/intro.html), der in JDK 11 eingeführt wurde

### <a name="replace-the-default-http-client"></a>Ersetzen des HTTP-Standardclients

Wenn Sie eine andere Implementierung bevorzugen, können Sie die Abhängigkeit von Netty entfernen, indem Sie sie in den Buildkonfigurationsdateien ausschließen. In einer Maven-Datei *pom.xml* schließen Sie die Netty-Abhängigkeit aus und schließen eine andere Abhängigkeit ein.

Im folgenden Beispiel wird gezeigt, wie die Netty-Abhängigkeit aus einer realen Abhängigkeit von der `azure-security-keyvault-secrets`-Bibliothek ausgeschlossen wird. Stellen Sie sicher, dass Sie Netty aus allen entsprechenden `com.azure`-Bibliotheken ausschließen, wie hier gezeigt:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-security-keyvault-secrets</artifactId>
    <version>4.2.2.</version>
    <exclusions>
      <exclusion>
        <groupId>com.azure</groupId>
        <artifactId>azure-core-http-netty</artifactId>
      </exclusion>
    </exclusions>
</dependency>

<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-core-http-okhttp</artifactId>
  <version>1.3.3</version>
</dependency>
```

> [!NOTE]
> Wenn Sie die Netty-Abhängigkeit entfernen, aber keine Implementierung stattdessen bereitstellen, kann die Anwendung nicht gestartet werden. Eine `HttpClient`-Implementierung muss im Klassenpfad vorhanden sein.

### <a name="configure-http-clients"></a>Konfigurieren von HTTP-Clients

Wenn Sie einen Dienstclient erstellen, wird standardmäßig `HttpClient.createDefault()` verwendet. Diese Methode gibt basierend auf der bereitgestellten HTTP-Clientimplementierung eine grundlegende `HttpClient`-Instanz zurück. Wenn Sie einen komplexeren HTTP-Client benötigen (z. B. einen Proxy), bietet jede Implementierung einen Generator, mit dem Sie eine konfigurierte `HttpClient`-Instanz erstellen können. Die Generatoren sind `NettyAsyncHttpClientBuilder`, `OkHttpAsyncHttpClientBuilder` und `JdkAsyncHttpClientBuilder`.

In den folgenden Beispielen wird gezeigt, wie `HttpClient`-Instanzen mit Netty, OkHttp und dem JDK 11-HTTP-Client erstellt werden. Diese Instanzen verwenden als Proxy `http://localhost:3128` und authentifizieren sich mit dem Benutzer *example* mit dem Kennwort *weakPassword*.

```java
// Netty
HttpClient httpClient = new NettyAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// OkHttp
HttpClient httpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();

// JDK 11 HttpClient
HttpClient client = new JdkAsyncHttpClientBuilder()
    .proxy(new ProxyOptions(ProxyOptions.Type.HTTP, new InetSocketAddress("localhost", 3128))
        .setCredentials("example", "weakPassword"))
    .build();
```

Sie können jetzt die generierte `HttpClient`-Instanz an einen Dienstclient-Generator übergeben, damit sie als Client für die Kommunikation mit dem Dienst verwendet wird. Im folgenden Beispiel wird die neue `HttpClient`-Instanz verwendet, um einen Azure Storage Blob-Client zu erstellen.

```java
BlobClient blobClient = new BlobClientBuilder()
    .connectionString(<connection string>)
    .containerName("container")
    .blobName("blob")
    .httpClient(httpClient)
    .build();
```

Für Verwaltungsbibliotheken können Sie den `HttpClient` während der Manager-Konfiguration festlegen.

```java
AzureResourceManager azureResourceManager = AzureResourceManager.configure()
    .withHttpClient(httpClient)
    .authenticate(credential, profile)
    .withDefaultSubscription();
```

## <a name="http-pipeline"></a>HTTP-Pipeline

Die HTTP-Pipeline ist eine der Hauptkomponenten zum Erreichen von Konsistenz und Diagnosefähigkeiten in den Java-Clientbibliotheken für Azure. Eine HTTP-Pipeline besteht aus den folgenden Komponenten:

* Einem HTTP-Transport
* HTTP-Pipelinerichtlinien

Sie können eine eigene benutzerdefinierte HTTP-Pipeline bereitstellen, wenn Sie einen Client erstellen. Wenn Sie keine Pipeline bereitstellen, erstellt die Clientbibliothek eine Pipeline, die so konfiguriert ist, dass sie mit der jeweiligen Clientbibliothek funktioniert.

### <a name="http-transport"></a>HTTP-Transport

Der HTTP-Transport ist für das Herstellen der Verbindung mit dem Server und das Senden und Empfangen von HTTP-Nachrichten verantwortlich. Der HTTP-Transport bildet das Gateway für die Azure SDK-Clientbibliotheken für die Interaktion mit Azure-Diensten. Wie bereits weiter oben in diesem Artikel erwähntwurde, verwendet das Azure SDK für Java standardmäßig [Netty](https://netty.io/) für den HTTP-Transport. Das SDK stellt jedoch auch einen austauschbaren HTTP-Transport bereit, sodass Sie ggf. andere Implementierungen verwenden können. Das SDK bietet auch zwei weitere HTTP-Transportimplementierungen für OkHttp und den HTTP-Client, der im Lieferumfang von JDK 11 oder höher enthalten ist.

### <a name="http-pipeline-policies"></a>HTTP-Pipelinerichtlinien

Eine Pipeline besteht aus einer Abfolge von Schritten, die für jeden HTTP-Anforderung/Antwort-Roundtrip ausgeführt werden. Jede Richtlinie dient einem dedizierten Zweck und wirkt sich auf eine Anforderung oder eine Antwort (und manchmal auf beides) aus. Da alle Clientbibliotheken über eine standardmäßige Ebene „Azure Core“ verfügen, gewährleistet diese Ebene, dass jede Richtlinie in der Pipeline in der richtigen Reihenfolge ausgeführt wird. Wenn Sie eine Anforderung senden, werden die Richtlinien in der Reihenfolge ausgeführt, in der sie der Pipeline hinzugefügt wurden. Wenn Sie eine Antwort vom Dienst erhalten, werden die Richtlinien in umgekehrter Reihenfolge ausgeführt. Alle Richtlinien, die der Pipeline hinzugefügt werden, werden vor dem Senden der Anforderung und nach dem Empfangen einer Antwort ausgeführt. Die Richtlinie muss entscheiden, ob sie für die Anforderung, für die Antwort oder für beides eine Aktion ausführt. Eine Protokollierungsrichtlinie protokolliert z. B. die Anforderung und die Antwort, aber die Authentifizierungsrichtlinie ist nur an der Änderung der Anforderung interessiert.

Das Azure Core-Framework stellt der Richtlinie die erforderlichen Anforderungs- und Antwortdaten zusammen mit dem erforderlichen Kontext zum Ausführen der Richtlinie bereit. Die Richtlinie kann dann den Vorgang mit den angegebenen Daten ausführen und die Steuerung dann an die nächste Richtlinie in der Pipeline übergeben.

![HTTP-Pipelinediagramm](./media/http-pipeline.svg)

### <a name="http-pipeline-policy-position"></a>Position der HTTP-Pipelinerichtlinie

Wenn Sie HTTP-Anforderungen an Clouddienste senden, ist es wichtig, vorübergehende Fehler zu behandeln und fehlerhafte Versuche zu wiederholen. Da diese Funktion eine gängige Anforderung ist, stellt Azure Core eine Wiederholungsrichtlinie zur Verfügung, die auf vorübergehende Fehler achten und die Anforderung automatisch wiederholen kann.

Diese Wiederholungsrichtlinie teilt daher die gesamte Pipeline in zwei Teile auf: Richtlinien, die vor der Wiederholungsrichtlinie ausgeführt werden, und Richtlinien, die nach der Wiederholungsrichtlinie ausgeführt werden. Richtlinien, die vor der Wiederholungsrichtlinie hinzugefügt wurden, werden nur ein Mal pro API-Vorgang ausgeführt. Richtlinien, die nach der Wiederholungsrichtlinie hinzugefügt wurden, werden so oft wie die Wiederholungsversuche ausgeführt.

Wenn Sie die HTTP-Pipeline erstellen, sollten Sie also wissen, ob eine Richtlinie für jede Anforderungswiederholung oder ein Mal pro API-Vorgang ausgeführt werden soll.

### <a name="common-http-pipeline-policies"></a>Gängige HTTP-Pipelinerichtlinien

HTTP-Pipelines für REST-basierte Dienste verfügen über Konfigurationen mit Richtlinien für Authentifizierung, Wiederholungsversuche, Protokollierung, Telemetrie und die Angabe der Anforderungs-ID im Header. Diese häufig erforderlichen HTTP-Richtlinien sind in Azure Core vordefiniert und können der Pipeline hinzugefügt werden.

| Richtlinie                | GitHub-Link        |
|-----------------------|--------------------|
| Wiederholungsrichtlinie          | [RetryPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RetryPolicy.java) |
| Authentifizierungsrichtlinie | [BearerTokenAuthenticationPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/BearerTokenAuthenticationPolicy.java) |
| Protokollierungsrichtlinie        | [HttpLoggingPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/HttpLoggingPolicy.java) |
| Anforderungs-ID-Richtlinie     | [RequestIdPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/RequestIdPolicy.java) |
| Telemetrierichtlinie      | [UserAgentPolicy.java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/http/policy/UserAgentPolicy.java) |

### <a name="custom-http-pipeline-policy"></a>Benutzerdefinierte HTTP-Pipelinerichtlinie

Die HTTP-Pipelinerichtlinie bietet einen bequemen Mechanismus zum Ändern oder Ergänzen der Anforderung und der Antwort. Sie können der Pipeline benutzerdefinierte Richtlinien hinzufügen, die entweder vom Benutzer oder vom Entwickler der Clientbibliothek erstellt werden. Wenn Sie der Pipeline die Richtlinie hinzufügen, können Sie angeben, ob diese Richtlinie pro Aufruf oder pro Wiederholungsversuch ausgeführt werden soll.

Um eine benutzerdefinierte HTTP-Pipelinerichtlinie zu erstellen, erweitern Sie einfach einen Basisrichtlinientyp und implementieren eine abstrakte Methode. Anschließend können Sie die Richtlinie in die Pipeline integrieren.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit der HTTP-Clientfunktionalität im Azure SDK für Java vertraut sind, finden Sie weitere Informationen unter [Konfigurieren von Proxys im Azure SDK für Java](proxying.md), um zu erfahren, wie Sie den von Ihnen verwendeten HTTP-Client weiter anpassen können.
