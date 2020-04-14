---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 4b5b73eee66c4a5c9eb28b79804e0dc610f639d6
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612079"
---
### <a name="inventory-external-resources"></a>Bestand: Externe Ressourcen

Externe Ressourcen, z. B. Datenquellen, JMS-Nachrichtenbroker und andere, werden per JNDI (Java Naming and Directory Interface) eingefügt. Für einige dieser Ressourcen ist unter Umständen eine Migration oder erneute Konfiguration erforderlich.

#### <a name="inside-your-application"></a>Innerhalb Ihrer Anwendung

Untersuchen Sie die Datei *META-INF/context.xml*. Suchen Sie im `<Context>`-Element nach `<Resource>`-Elementen.

#### <a name="on-the-application-servers"></a>Auf den Anwendungsservern

Untersuchen Sie die Dateien *$CATALINA_BASE/conf/context.xml* und *$CATALINA_BASE/conf/server.xml* sowie die *XML*-Dateien, die in Verzeichnissen der Art *$CATALINA_BASE/conf/[Engine-Name]/[Hostname]* enthalten sind.

In *context.xml*-Dateien werden JNDI-Ressourcen im `<Context>`-Element der obersten Ebene mithilfe von `<Resource>`-Elementen beschrieben.

In *server.xml*-Dateien werden JNDI-Ressourcen im `<GlobalNamingResources>`-Element mithilfe von `<Resource>`-Elementen beschrieben.

#### <a name="datasources"></a>Datenquellen

Datenquellen sind JNDI-Ressourcen, für die das `type`-Attribut auf `javax.sql.DataSource` festgelegt ist. Dokumentieren Sie für jede Datenquelle die folgenden Informationen:

* Wie lautet der Name der Datenquelle?
* Wie ist der Verbindungspool konfiguriert?
* Wo ist die JAR-Datei mit den JDBC-Treibern zu finden?

Weitere Informationen finden Sie in der Tomcat-Dokumentation unter [Anleitung zur JNDI-Datenquelle](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html).

#### <a name="all-other-external-resources"></a>Alle anderen externen Ressourcen

Es würde den Rahmen dieses Leitfadens sprengen, jede mögliche externe Abhängigkeit zu dokumentieren. Ihr Team ist dafür verantwortlich, zu überprüfen, dass nach der Migration die Anforderungen aller externen Abhängigkeiten Ihrer Anwendung abgedeckt werden können.
