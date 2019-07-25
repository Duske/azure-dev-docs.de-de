---
title: Verwenden von Docker-Images mit einem JDK für die Azure Java-Entwicklung
description: ''
author: bmitchell287
manager: douge
ms.author: brendm
ms.date: 04/09/2019
ms.devlang: java
ms.topic: conceptual
ms.service: Azure
ms.openlocfilehash: 5de2866f1f303deccf40158ac7451dbd35de92f1
ms.sourcegitcommit: 4cc7f5e1e4601065bfcb4c2eeb7d47ad0bec61f8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68430958"
---
# <a name="use-docker-with-a-jdk-for-azure"></a>Verwenden von Docker mit einem JDK für Azure 

Vorgefertigte Docker-Images für Java 7, 8 und 11 sind über [Docker Hub](https://hub.docker.com/_/microsoft-java-se) verfügbar.

Auf Docker Hub stehen zertifizierte Docker-Containerimages für das Zulu JDK, die JRE und die monitorlose JRE für mehrere Basisbetriebssystem-Images zur Verfügung:

* [JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Monitorlose JRE](https://hub.docker.com/_/microsoft-java-jre-headless)

## <a name="running-a-docker-image"></a>Ausführen eines Docker-Images

Docker-Images können wie im folgenden Beispiel gezeigt mit der Syntax `$ docker run mcr.microsoft.com/java/jdk:tag java` ausgeführt werden:

```cli
docker run mcr.microsoft.com/java/jdk:8u212-zulu-alpine java -version 
```

## <a name="creating-a-docker-image"></a>Erstellen eines Docker-Images

Sie können wie in den folgenden Beispielen gezeigt unter Verwendung der offiziellen Docker Hub-Images von Microsoft ein Image erstellen:

### <a name="create-a-docker-file"></a>Erstellen einer Docker-Datei

```cli
FROM mcr.microsoft.com/java/jdk:8u212-zulu-alpine 
  
RUN echo $' \
  
public class HelloWorld { \
   public static void main(String[] args) { \
      // Prints "Hello, World" in the terminal window. \
      System.out.println("Hello, World - From Microsoft Azure !!!"); \
   } \
}' > HelloWorld.java
  
RUN javac HelloWorld.java
  
CMD ["java", "HelloWorld"]
```

### <a name="build-a-docker-image"></a>Erstellen eines Docker-Images

```cli
docker build -t hello-world
```

### <a name="run-the-new-image"></a>Ausführen des neuen Images

```cli
docker run hello-world
```

Die folgende Ausgabe wird angezeigt:

```output
Hello World - From Microsoft Azure !!!
```
