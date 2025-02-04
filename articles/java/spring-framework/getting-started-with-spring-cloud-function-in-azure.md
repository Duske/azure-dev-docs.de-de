---
title: Einstieg in Spring Cloud Function in Azure
description: Erfahren Sie mehr über die Verwendung von Spring Cloud Function in Azure.
documentationcenter: java
author: jdubois
manager: brborges
ms.author: judubois
ms.date: 07/17/2019
ms.service: azure-functions
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 5c13a69769bef56c2b67607118b0a20c4f59cd5c
ms.sourcegitcommit: 576c878c338d286060010646b96f3ad0fdbcb814
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102118433"
---
# <a name="getting-started-with-spring-cloud-function-in-azure"></a>Einstieg in Spring Cloud Function in Azure

Dieser Artikel führt Sie durch die [Verwendung von Spring Cloud Functions](https://spring.io/projects/spring-cloud-function) zum Entwickeln einer Java-Funktion und deren Veröffentlichung in Azure Functions. Wenn Sie fertig sind, wird Ihr Funktionscode für den [Verbrauchstarif](/azure/azure-functions/functions-scale#consumption-plan) in Azure ausgeführt und kann mithilfe einer HTTP-Anforderung ausgelöst werden.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Um Funktionen mit Java zu entwickeln, muss Folgendes installiert sein:

- [Java Developer Kit](../fundamentals/java-jdk-long-term-support.md), Version 8
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher
- [Azure-Befehlszeilenschnittstelle](/cli/azure)
- [Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2), Version 2.7.1158 oder höher

> [!IMPORTANT]
> Damit Sie diesen Schnellstart durchführen können, muss die Umgebungsvariable JAVA_HOME auf den Installationsspeicherort des JDK festgelegt sein.

## <a name="what-we-are-going-to-build"></a>Was wir erstellen werden

Wir werden eine klassische „Hello, World“-Funktion erstellen, die unter Azure Functions ausgeführt und mit Spring Cloud Function konfiguriert wird.

Sie empfängt ein einfaches `User`-JSON-Objekt, das einen Benutzernamen enthält, und sendet ein `Greeting`-Objekt zurück, das die Begrüßung dieses Benutzers enthält.

Das Projekt, das wir hier erstellen, ist auf [https://github.com/Azure-Samples/hello-spring-function-azure](https://github.com/Azure-Samples/hello-spring-function-azure) verfügbar, so dass Sie dieses Beispielrepository direkt verwenden können, wenn Sie die endgültige Arbeit sehen möchten, die in diesem Schnellstart beschrieben wird.

## <a name="create-a-new-maven-project"></a>Erstellen eines neuen Maven-Projekts

Wir erstellen ein leeres Maven-Projekt und konfigurieren es mit Spring Cloud Function und Azure Functions.

Erstellen Sie in einem leeren Ordner eine neue Datei *pom.xml*, kopieren Sie den Inhalt aus dem Beispielprojekt unter [https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml](https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml), und fügen Sie ihn ein.

> [!NOTE]
> Diese Datei verwendet Maven-Abhängigkeiten von Spring Boot und Spring Cloud Function und konfiguriert die Spring Boot- und Azure Functions Maven-Plug-Ins.

Für Ihre Anwendung müssen einige Eigenschaften angepasst werden:

- `<functionAppName>` ist der Name Ihrer Azure-Funktion.
- `<functionAppRegion>` ist der Name der Azure-Region, in der die Funktion bereitgestellt wird.
- `<functionResourceGroup>` ist der Name der von Ihnen verwendeten Azure-Ressourcengruppe.

Sie sollten diese Eigenschaften direkt am oberen Rand der Datei *pom.xml* ändern:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>

    <azure.functions.java.library.version>1.4.0</azure.functions.java.library.version>
    <azure.functions.maven.plugin.version>1.9.1</azure.functions.maven.plugin.version>

    <!-- customize those two properties. The functionAppName should be unique across Azure -->
    <functionResourceGroup>my-spring-function-resource-group</functionResourceGroup>
    <functionAppName>my-spring-function</functionAppName>

    <functionAppRegion>eastus</functionAppRegion>
    <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    <start-class>com.example.DemoApplication</start-class>
    <spring.boot.wrapper.version>1.0.26.RELEASE</spring.boot.wrapper.version>
</properties>
```

## <a name="create-azure-configuration-files"></a>Erstellen von Azure-Konfigurationsdateien

Erstellen Sie einen *src/main/azure*-Ordner, und fügen Sie die folgenden Azure Functions-Konfigurationsdateien dort ein.

*host.json*:

```json
{
  "version": "2.0",
  "functionTimeout": "00:10:00"
}
```

*local.settings.json*:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "java",
    "MAIN_CLASS":"com.example.DemoApplication",
    "AzureWebJobsDashboard": ""
  }
}
```

## <a name="create-domain-objects"></a>Erstellen von Domänenobjekten

Azure Functions kann Objekte im JSON-Format empfangen und senden.
Wir erstellen jetzt unser `User`- und `Greeting`-Objekt, die das Domänenmodell darstellen.
Sie können komplexere Objekte mit weiteren Eigenschaften erstellen, wenn Sie diese Schnellstartanleitung anpassen und für sich interessanter gestalten möchten.

Erstellen Sie einen Ordner *src/main/java/com/example/model*, und fügen Sie die folgenden beiden Dateien hinzu:

*User.java*:

```java
package com.example.model;

public class User {

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

*Greeting.java*:

```java
package com.example.model;

public class Greeting {

    public Greeting() {
    }

    public Greeting(String message) {
        this.message = message;
    }

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

## <a name="create-the-spring-boot-application"></a>Erstellen der Spring Boot-Anwendung

Diese Anwendung verwaltet die gesamte Geschäftslogik und hat Zugriff auf das gesamte Spring Boot-Ökosystem.
Dies bietet Ihnen zwei wesentliche Vorteile gegenüber einer Azure-Standardfunktion:

- Sie beruht nicht auf den Azure Functions-APIs und kann daher problemlos auf andere Systeme portiert werden. Beispielsweise könnte sie in einer normalen Spring Boot-Anwendung wiederverwendet werden.
- Sie kann alle `@Enable`-Anmerkungen von Spring Boot verwenden, um problemlos leistungsstarke neue Features hinzuzufügen.

Erstellen Sie im Ordner *src/main/java/com/example* die folgende Datei, die eine normale Spring Boot-Anwendung ist:

*DemoApplication.java*:

```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(HelloFunction.class, args);
    }
}
```

Erstellen Sie nun die folgende Datei. Sie enthält eine Spring Boot-Komponente, die für die auszuführende Funktion steht:

*HelloFunction.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.springframework.stereotype.Component;

import java.util.function.Function;

@Component
public class HelloFunction implements Function<User, Greeting> {

    @Override
    public Greeting apply(User user) {
        return new Greeting("Hello, " + user.getName() + "!\n");
    }
}
```

> [!NOTE]
> Die `HelloFunction`-Funktion ist recht spezifisch:
>
> - Es handelt sich um eine Funktion vom Typ `java.util.function.Function`. Dies ist die Funktion, die in dieser Schnellstartanleitung verwendet wird. Sie enthält die Geschäftslogik und verwendet eine Standard-Java-API, um ein Objekt in ein anderes zu transformieren.
> - Da sie die `@Component`-Anmerkung enthält, ist es eine Spring Bean-Funktion. Sie hat standardmäßig den Namen der Klasse und beginnt mit einem Kleinbuchstaben: `helloFunction`. Dies ist wichtig, wenn Sie weitere Funktionen in Ihrer Anwendung erstellen möchten, da dieser Name mit dem Azure Functions-Namen übereinstimmen muss, der im nächsten Abschnitt erstellt wird.

## <a name="create-the-azure-function"></a>Erstellen der Azure-Funktion

Um die gesamte Azure Functions-API nutzen zu können, werden wir jetzt eine bestimmte Klasse programmieren: Es handelt sich um eine Azure-Funktion, die ihre Ausführung an die Spring Cloud Function-Instanz delegiert, die wir im vorherigen Schritt erstellt haben.

Erstellen Sie im Ordner *src/main/java/com/example* die folgende Azure-Funktion:

*HelloHandler.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.AuthorizationLevel;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.HttpTrigger;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import java.util.Optional;

public class HelloHandler extends AzureSpringBootRequestHandler<User, Greeting> {

    @FunctionName("hello")
    public HttpResponseMessage execute(
            @HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<User>> request,
            ExecutionContext context) {
        User user = request.getBody()
                .filter((u -> u.getName() != null))
                .orElseGet(() -> new User(
                        request.getQueryParameters()
                                .getOrDefault("name", "world")));
        context.getLogger().info("Greeting user name: " + user.getName());
        return request
                .createResponseBuilder(HttpStatus.OK)
                .body(handleRequest(user, context))
                .header("Content-Type", "application/json")
                .build();
    }
}
```

Diese Java-Klasse ist eine Azure-Funktion mit den folgenden interessanten Features:

- Sie erweitert `AzureSpringBootRequestHandler`, wodurch die Verknüpfung zwischen Azure Functions und Spring Cloud Function erfolgt. Dadurch wird die `handleRequest()`-Methode bereitgestellt, die in der `body()`-Methode verwendet wird.
- Der Name der Funktion lautet `hello` (gemäß Definition durch die Anmerkung `@FunctionName("hello")`).
- Dabei handelt es sich um eine echte Azure-Funktion, sodass Sie hier die gesamte Azure Functions-API verwenden können.

## <a name="add-unit-tests"></a>Hinzufügen von Komponententests

Dieser Schritt ist natürlich optional, aber als gute Entwickler sollten Sie Komponententests hinzufügen, um zu überprüfen, ob die Anwendung ordnungsgemäß funktioniert.

Erstellen Sie einen Ordner *src/test/java/com/example*, und fügen Sie die folgenden JUnit-Tests hinzu:

*HelloFunctionTest.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.junit.jupiter.api.Test;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloFunctionTest {

    @Test
    public void test() {
        Greeting result = new HelloFunction().apply(new User("foo"));
        assertThat(result.getMessage()).isEqualTo("Hello, foo!\n");
    }

    @Test
    public void start() throws Exception {
        AzureSpringBootRequestHandler<User, Greeting> handler = new AzureSpringBootRequestHandler<>(
                HelloFunction.class);
        Greeting result = handler.handleRequest(new User("foo"), null);
        handler.close();
        assertThat(result.getMessage()).isEqualTo("Hello, foo!\n");
    }
}
```

Nun können Sie Ihre Azure-Funktion mit Maven testen:

```bash
mvn clean test
```

## <a name="run-the-function-locally"></a>Lokales Ausführen der Funktion

Bevor Sie Ihre Anwendung in Azure Functions bereitstellen, testen Sie sie zuerst lokal.

Zuerst müssen Sie Ihre Anwendung in eine JAR-Datei packen:

```bash
mvn package
```

Nachdem die Anwendung gepackt wurde, können Sie sie mithilfe des Maven-Plug-Ins `azure-functions` ausführen:

```bash
mvn azure-functions:run
```

Die Azure-Funktion sollte nun auf dem Localhost mit Port 7071 verfügbar sein. Sie können die Funktion testen, indem Sie Ihr eine POST-Anforderung mit einem `User`-Objekt im JSON-Format senden. Verwenden Sie beispielsweise cURL:

```bash
curl http://localhost:7071/api/hello -d "{\"name\":\"Azure\"}"
```

Die Funktion sollte Ihnen mit einem `Greeting`-Objekt antworten, immer noch im JSON-Format:

```Output
{
  "message": "Welcome, Azure"
}
```

Im Folgenden finden Sie einen Screenshot der cURL-Anforderung am oberen und der lokalen Azure-Funktion am unteren Bildschirmrand:

 ![Lokale Ausführung der Azure-Funktion][RFL01]

## <a name="deploy-the-function-to-azure-functions"></a>Bereitstellen der Funktion in Azure Functions

Jetzt veröffentlichen Sie die Azure-Funktion in der Produktionsumgebung. Beachten Sie, dass die Eigenschaften `<functionAppName>`, `<functionAppRegion>` und `<functionResourceGroup>`, die Sie in der Datei *pom.xml* definiert haben, zum Konfigurieren der Funktion verwendet werden.

> [!NOTE]
> Das Maven-Plug-In muss über Azure authentifiziert werden. Haben Sie die Azure CLI installiert, verwenden Sie `az login`, bevor Sie fortfahren.
> [Hier](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) finden Sie weitere Authentifizierungsoptionen.

Führen Sie Maven aus, um die Funktion automatisch bereitzustellen:

```bash
mvn azure-functions:deploy
```

Wechseln Sie nun zum [Azure-Portal](https://portal.azure.com), um die `Function App` zu suchen, die erstellt wurde.

Klicken Sie auf die Funktion:

- Beachten Sie in der Funktionsübersicht die URL der Funktion.
- Wählen Sie die Registerkarte **Plattformfeatures** aus, um den **Protokollstreaming**-Dienst zu suchen, und wählen Sie dann diesen Dienst aus, um Ihre ausgeführte Funktion zu überprüfen.

Greifen Sie jetzt wie im vorherigen Abschnitt mit cURL auf die ausgeführte Funktion zu. Ersetzen Sie `your-function-name` durch den tatsächlichen Funktionsnamen:

```bash
curl https://your-function-name.azurewebsites.net/api/hello -d "{\"name\":\"Azure\"}"
```

Die Funktion sollte Ihnen wie im vorherigen Abschnitt mit einem `Greeting`-Objekt antworten, immer noch im JSON-Format:

```Output
{
  "message": "Welcome, Azure"
}
```

Herzlichen Glückwunsch, bei Ihnen wird unter Azure Functions eine Spring Cloud Function-Instanz ausgeführt!

<!-- IMG List -->

[RFL01]: media/getting-started-with-spring-cloud-function-in-azure/RFL01.png
