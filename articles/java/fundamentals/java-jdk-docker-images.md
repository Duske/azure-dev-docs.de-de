---
title: Verwenden von Docker-Images mit einem JDK für die Azure Java-Entwicklung
description: Hier erfahren Sie, wie Sie Docker-Images mit einem Java Development Kit (JDK) für Azure über die Befehlszeilenschnittstelle verwenden.
ms.date: 04/09/2019
ms.topic: conceptual
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d1e66ac7e00f23fa4376d9dccddd301bbbe07bfd
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379824"
---
# <a name="use-docker-with-a-java-development-kit-jdk-for-azure"></a>Verwenden von Docker mit einem Java Development Kit (JDK) für Azure

Dieser Artikel beschreibt die Verwendung von Docker mit einem Java Development Kit (JDK) für Azure. Vorgefertigte Docker-Images für Java 7, 8 und 11 sind über [Docker Hub](https://hub.docker.com/_/microsoft-java-se) verfügbar.

Auf Docker Hub stehen zertifizierte Docker-Containerimages für das Zulu JDK, die JRE und die monitorlose JRE für mehrere Basisbetriebssystem-Images zur Verfügung:

* [JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [JRE](https://hub.docker.com/_/microsoft-java-jre)
* [JRE (monitorlos)](https://hub.docker.com/_/microsoft-java-jre-headless)

## <a name="running-a-docker-image"></a>Ausführen eines Docker-Images

Docker-Images können wie im folgenden Beispiel gezeigt mit der Syntax `$ docker run mcr.microsoft.com/java/jdk:tag java` ausgeführt werden:

```cli
docker run mcr.microsoft.com/java/jdk:8-zulu-alpine java -version
```

## <a name="creating-a-docker-image"></a>Erstellen eines Docker-Images

Sie können wie in den folgenden Beispielen gezeigt unter Verwendung der offiziellen Docker Hub-Images von Microsoft ein Image erstellen:

### <a name="create-a-docker-file"></a>Erstellen einer Docker-Datei

```cli
FROM mcr.microsoft.com/java/jdk:8-zulu-alpine
  
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
