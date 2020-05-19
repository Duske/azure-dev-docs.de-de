---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: b15ebf1491dd494701dd5c18e0248e73bdd86f2c
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990202"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Ermitteln des Bestands an Konfigurationsquellen und Geheimnissen

#### <a name="inventory-passwords-and-secure-strings"></a>Ermitteln des Bestands an Kennwörtern und sicheren Zeichenfolgen

Überprüfen Sie alle Eigenschaften und Konfigurationsdateien sowie alle Umgebungsvariablen in den Produktionsbereitstellungen auf Geheimniszeichenfolgen und Kennwörter. Bei einer Spring Cloud-Anwendung befinden sich solche Zeichenfolgen in der Regel in der Datei *application.properties* oder *application.yml* individueller Dienste oder im Spring Cloud Config-Repository.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]

#### <a name="determine-whether-spring-cloud-vault-is-used"></a>Ermitteln, ob Spring Cloud Vault verwendet wird

Wenn Sie Spring Cloud Vault verwenden, um Geheimnisse zu speichern und darauf zuzugreifen, ermitteln Sie den zugrunde liegenden Geheimnisspeicher (HashiCorp Vault oder CredHub). Ermitteln Sie anschließend alle vom Anwendungscode verwendeten Geheimnisse.

#### <a name="locate-the-configuration-server-source"></a>Suchen der Konfigurationsserverquelle

Wenn Ihre Anwendung einen [Spring Cloud Config-Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) verwendet, ermitteln Sie den Speicherort der Konfiguration. Diese Einstellung befindet sich üblicherweise in der Datei *bootstrap.yml* oder *bootstrap.properties*, manchmal aber auch in der Datei *application.yml* oder *application.properties*. Die Einstellung sieht wie im folgenden Beispiel aus:

```properties
spring.cloud.config.server.git.uri: file://${user.home}/spring-cloud-config-repo
```

Als zugrunde liegender Datenspeicher von Spring Cloud Config wird zwar meist „git“ verwendet, es kann jedoch auch eines der anderen möglichen Back-Ends verwendet werden, wie weiter oben gezeigt. Informationen zu anderen Back-Ends wie [relationale Datenbank (JDBC)](https://cloud.spring.io/spring-cloud-config/reference/html/#_jdbc_backend), [SVN](https://cloud.spring.io/spring-cloud-config/reference/html/#_version_control_backend_filesystem_use) oder [lokales Dateisystem](https://cloud.spring.io/spring-cloud-config/reference/html/#_file_system_backend) finden Sie in der [Dokumentation zu Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/reference/html/#_environment_repository).

> [!NOTE]
> Falls Ihre Konfigurationsserverdaten lokal gespeichert sind (Beispiel: GitHub Enterprise), müssen sie über ein Git-Repository für Azure Spring Cloud verfügbar gemacht werden.
