---
title: Konfigurieren von Proxys im Azure SDK für Java
description: Übersicht über die Azure SDK für Java-Konzepte im Zusammenhang mit der Weiterleitung über Proxys
author: alzimmermsft
ms.date: 02/02/2021
ms.topic: conceptual
ms.custom: devx-track-java
ms.author: alzimmer
ms.openlocfilehash: ce4305e1f76a15ab799523f67002315b0883c3e1
ms.sourcegitcommit: 71847ee0a1fee3f3320503629d9a8c82319a1f6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99528477"
---
# <a name="configure-proxies-in-the-azure-sdk-for-java"></a>Konfigurieren von Proxys im Azure SDK für Java

Dieser Artikel bietet eine Übersicht, wie Sie das Azure SDK für Java konfigurieren, um Proxys ordnungsgemäß nutzen zu können.

## <a name="http-proxy-configuration"></a>HTTP-Proxykonfiguration

Die Azure-Clientbibliotheken für Java bieten verschiedene Möglichkeiten zum Konfigurieren eines Proxys für einen `HttpClient`.

Jede Methode zur Bereitstellung eines Proxys hat ihre eigenen Vor- und Nachteile und bietet unterschiedliche Ebenen von Kapselung. Wenn Sie einen Proxy für einen `HttpClient`konfiguriert haben, verwendet dieser den Proxy für den Rest seiner Lebensdauer. Dadurch, dass der Proxy an einen einzelnen `HttpClient` gebunden ist, kann eine Anwendung mehrere `HttpClient`-Instanzen verwenden, von denen jede einen anderen Proxy verwenden kann, um die Anforderungen einer Anwendung an die Proxyweiterleitung zu erfüllen.

Die Konfigurationsoptionen für Proxys lauten:

* [Verwenden eines Umgebungsproxys](#use-an-environment-proxy)
* [Verwenden eines Konfigurationsproxys](#use-a-configuration-proxy)
* [Verwenden eines expliziten Proxys](#use-an-explicit-proxy)

### <a name="use-an-environment-proxy"></a>Verwenden eines Umgebungsproxys

Standardmäßig untersuchen HTTP-Client-Generatoren die Umgebung auf Proxykonfigurationen. Bei diesem Vorgang werden `Configuration`-APIs des Azure SDK für Java verwendet. Wenn der Generator einen Client erstellt, wird er mit einer Kopie der „globalen Konfiguration“ konfiguriert, die mittels Aufruf von `Configuration.getGlobalConfiguration()`abgerufen wurde. Mit diesem Aufruf wird eine beliebige HTTP-Proxykonfiguration aus der Systemumgebung eingelesen.

Wenn der Generator die Umgebung untersucht, sucht er dabei nach den folgenden Umgebungskonfigurationen in der angegebenen Reihenfolge:

1. `HTTPS_PROXY`
2. `HTTP_PROXY`
3. `https.proxy*`
4. `http.proxy*`

Der `*` stellt die bekannten Java-Proxyeigenschaften dar. Weitere Informationen finden Sie in der Oracle-Dokumentation unter [Java-Netzwerk und -Proxys](https://docs.oracle.com/javase/8/docs/technotes/guides/net/proxies.html).

Wenn der Generator eine der Umgebungskonfigurationen findet, erstellt er eine `ProxyOptions`-Instanz durch Aufrufen von `ProxyOptions.fromConfiguration(Configuration.getGlobalConfiguration())`. Dieser Artikel enthält weiter unten noch Details zum `ProxyOptions`-Typ.

> [!Important]
> Um eine Proxykonfiguration verwenden zu können, erfordert Java, dass Sie die Systemumgebungseigenschaft`java.net.useSystemProxies` auf `true` festlegen.

Sie können auch eine HTTP-Clientinstanz erstellen, die keine der in den Systemumgebungsvariablen vorhandenen Proxykonfigurationen verwendet. Um das Standardverhalten außer Kraft zu setzen, legen Sie im HTTP-Client-Generator explizit eine anders konfigurierte `Configuration` fest. Wenn Sie im Generator eine `Configuration` festlegen, ruft dieser nicht mehr `Configuration.getGlobalConfiguration()` auf. Wenn Sie z. B. `configuration(Configuration)` mithilfe von `Configuration.NONE` aufrufen, können Sie den Generator explizit daran hindern, die Umgebung auf Konfigurationen zu untersuchen.

Im folgenden Beispiel wird die Umgebungsvariable `HTTP_PROXY` mit dem Wert `localhost:8888` verwendet, um Fiddler als Proxy zu verwenden. Dieser Code veranschaulicht das Erstellen eines Netty- und eines OkHttp-HTTP-Clients. (Weitere Informationen zur HTTP-Clientkonfiguration finden Sie unter [HTTP-Clients und -Pipelines](http-client-pipeline.md).)

```bash
export HTTP_PROXY=localhost:8888
```

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder().build();
HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder().build();
```

Um die Verwendung des Umgebungsproxys zu verhindern, konfigurieren Sie den HTTP-Client-Generator mit `Configuration.NONE`, wie im folgenden Beispiel gezeigt:

```java
HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(Configuration.NONE)
    .build();
```

### <a name="use-a-configuration-proxy"></a>Verwenden eines Konfigurationsproxys

Anstatt HTTP-Client-Generatoren aus der Umgebung lesen zu lassen, können Sie diese so konfigurieren, dass sie eine benutzerdefinierte `Configuration` mit denselben Proxyeinstellungen verwenden, die bereits von der Umgebung akzeptiert werden. Diese Konfiguration bietet die Möglichkeit, wiederverwendbare Konfigurationen zu nutzen, deren Gültigkeit auf einen begrenzten Anwendungsfall beschränkt ist. Wenn der HTTP-Client-Generator den `HttpClient` erstellt, verwendet er hierzu die von `ProxyOptions.fromConfiguration(<Configuration passed into the builder>)` zurückgegebenen `ProxyOptions`.

Im folgenden Beispiel werden die in einem `Configuration`-Objekt festgelegten `http.proxy*`-Konfigurationen verwendet, um einen Proxy zu verwenden, der Fiddler als Proxy authentifiziert.

```java
Configuration configuration = new Configuration()
    .put("java.net.useSystemProxies", "true")
    .put("http.proxyHost", "localhost")
    .put("http.proxyPort", "8888")
    .put("http.proxyUser", "1")
    .put("http.proxyPassword", "1");

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .configuration(configuration)
    .build();
```

### <a name="use-an-explicit-proxy"></a>Verwenden eines expliziten Proxys

Die Java-Clientbibliotheken enthalten im Lieferumfang eine `ProxyOptions`-Klasse, die als Typ für Azure-Clientbibliotheken zur Konfiguration eines Proxys fungiert. Sie können `ProxyOptions` mit dem Netzwerkprotokoll konfigurieren, das zum Senden von Proxyanforderungen verwendet wird, sowie mit der Proxyadresse, den Anmeldeinformationen für die Proxyauthentifizierung und den Hosts, die keinen Proxy verwenden. Erforderlich sind nur das Proxynetzwerkprotokoll und die Proxyadresse. Wenn Sie Anmeldeinformationen für die Authentifizierung verwenden, müssen Sie sowohl den Benutzernamen als auch das Kennwort festlegen.

Im folgenden Beispiel wird eine einfache `ProxyOptions`-Instanz erstellt, die Anforderungen per Proxy an die Fiddler-Standardadresse (`localhost:8888`) weiterleitet:

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.HTTP, new InetSocketAddress("localhost", 8888));
```

Im folgenden Beispiel wird eine authentifizierte `ProxyOptions`-Instanz erstellt, die Anforderungen per Proxy an eine Fiddler-Instanz weiterleitet, die Proxyauthentifizierung erfordert:

```java
// Fiddler uses username "1" and password "1" with basic authentication as its proxy authentication requirement.
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.HTTP, new InetSocketAddess("localhost", 8888))
    .setCredentials("1", "1");
```

Sie können HTTP-Client-Generatoren direkt mit `ProxyOptions` konfigurieren, um einen expliziten Proxy anzugeben, der verwendet werden soll. Diese Konfiguration ist die präziseste Möglichkeit zum Bereitstellen eines Proxys und in der Regel nicht so flexibel, wie das Übergeben einer `Configuration`, die Sie so mutieren können, dass sie die Anforderungen an die Proxyweiterleitung aktualisiert.

Im folgenden Beispiel werden `ProxyOptions` verwendet, damit Fiddler als Proxy verwendet wird:

```java
ProxyOptions proxyOptions = new ProxyOptions(ProxyOptions.HTTP, new InetSocketAddress("localhost", 8888));

HttpClient nettyHttpClient = new NettyAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();

HttpClient okhttpHttpClient = new OkHttpAsyncHttpClientBuilder()
    .proxy(proxyOptions)
    .build();
```

## <a name="next-steps"></a>Nächste Schritte

Nun, da Sie mit der Proxykonfiguration im Azure SDK für Java vertraut sind, finden Sie unter [Konfigurieren der Ablaufverfolgung im Azure SDK für Java](tracing.md) Informationen zum besseren Verständnis von Abläufen innerhalb Ihrer Anwendung und zur Diagnose von Problemen.
