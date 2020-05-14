---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: 1f73c46d5bc259c94e58a48a8a6bdb883a4454fd
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369828"
---
Konfigurieren Sie in der `DemoApplication`-Hauptklasse eine neue Spring Bean, die ein Datenbankschema erstellt:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Diese Spring Bean verwendet eine Datei namens *schema.sql*. Erstellen Sie daher diese Datei im Ordner *src/main/resources*:
