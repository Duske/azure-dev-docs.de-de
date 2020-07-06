---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: fa66c4e9db481e31853c8e67816a14b6ee753fd2
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507499"
---
Konfigurieren Sie in der `DemoApplication`-Hauptklasse mit dem folgenden Code eine neue Spring Bean, die ein Datenbankschema erstellt:

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

Diese Spring Bean verwendet eine Datei namens *schema.sql*. Erstellen Sie daher diese Datei im Ordner *src/main/resources*, und fügen Sie den folgenden Text hinzu:
