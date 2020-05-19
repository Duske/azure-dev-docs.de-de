---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: 34701a7d3fd85fea412be859dc21823df2dde053
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990192"
---
#### <a name="identify-spring-boot-versions"></a>Ermitteln von Spring Boot-Versionen

Überprüfen Sie die Abhängigkeiten der einzelnen Anwendungen, die migriert werden, um ihre Spring Boot-Version zu bestimmen.

##### <a name="maven"></a>Maven

Bei Maven-Projekten befindet sich die Spring Boot-Version in der Regel im `<parent>`-Element der POM-Datei:

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

##### <a name="gradle"></a>Gradle

Bei Gradle-Projekten befindet sich die Spring Boot-Version in der Regel im Abschnitt `plugins` als Version des Plug-Ins `org.springframework.boot`:

```gradle
plugins {
  id 'org.springframework.boot' version '2.2.6.RELEASE'
  id 'io.spring.dependency-management' version '1.0.9.RELEASE'
  id 'java'
}
```
