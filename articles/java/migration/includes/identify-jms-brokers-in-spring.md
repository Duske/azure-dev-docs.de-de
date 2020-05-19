---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: d17b69ea5299c24406a63fc6239c350b6cbcbdd4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990142"
---
#### <a name="jms-message-brokers"></a>JMS-Nachrichtenbroker

Ermitteln Sie die verwendeten Broker, indem Sie sich im Buildmanifest (in der Regel eine Datei vom Typ *pom.xml* oder *build.gradle*) die relevanten Abhängigkeiten ansehen.

Bei einer Spring Boot-Anwendung, die ActiveMQ verwendet, ist diese Abhängigkeit beispielsweise in der Datei *pom.xml* enthalten:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

Bei Spring Boot-Anwendungen, für die proprietäre Broker genutzt werden, sind Abhängigkeiten meist direkt in den JMS-Treiberbibliotheken der Broker enthalten. Hier ist ein Beispiel aus der Datei *build.gradle* angegeben:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```
