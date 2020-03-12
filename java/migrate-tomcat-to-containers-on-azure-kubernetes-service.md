---
title: Migrieren von Tomcat-Anwendungen zu Containern unter Azure Kubernetes Service
description: In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene Tomcat-Anwendung für die Ausführung in einem Azure Kubernetes Service-Container migrieren möchten.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 1/20/2020
ms.openlocfilehash: fafe7b16b14f43f6fe97090de8964c4e78796bda
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78893741"
---
# <a name="migrate-tomcat-applications-to-containers-on-azure-kubernetes-service"></a>Migrieren von Tomcat-Anwendungen zu Containern unter Azure Kubernetes Service

In diesem Leitfaden wird beschrieben, was Sie beachten sollten, wenn Sie eine vorhandene Tomcat-Anwendung für die Ausführung unter Azure Kubernetes Service (AKS) migrieren möchten.

## <a name="pre-migration"></a>Vor der Migration

[!INCLUDE [inventory-external-resources](includes/migration/inventory-external-resources.md)]

[!INCLUDE [inventory-secrets](includes/migration/inventory-secrets.md)]

[!INCLUDE [inventory-persistence-usage](includes/migration/inventory-persistence-usage.md)]

<!-- AKS-specific addendum to inventory-persistence-usage -->
#### <a name="dynamic-or-internal-content"></a>Dynamischer oder interner Inhalt

Für Dateien, für die von Ihrer Anwendung häufige Schreib- und Lesevorgänge durchgeführt werden (z. B. temporäre Datendateien), oder für statische Dateien, die nur für Ihre Anwendung sichtbar sind, können Sie Azure Storage-Freigaben als persistente Volumes bereitstellen. Weitere Informationen finden Sie unter [Dynamisches Erstellen und Verwenden eines persistenten Volumes mit Azure Files in Azure Kubernetes Service (AKS)](/azure/aks/azure-files-dynamic-pv).

### <a name="identify-session-persistence-mechanism"></a>Identifizieren eines Mechanismus für Sitzungspersistenz

Untersuchen Sie die *context.xml*-Dateien in Ihrer Anwendung und der Tomcat-Konfiguration, um den verwendeten Manager für Sitzungspersistenz zu ermitteln. Suchen Sie nach dem `<Manager>`-Element, und notieren Sie sich den Wert des `className`-Attributs.

Die integrierten [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html)-Implementierungen von Tomcat, z. B. [StandardManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Standard_Implementation) oder [FileStore](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html#Nested_Components), sind nicht für die Nutzung mit einer verteilten skalierten Plattform wie Kubernetes konzipiert. AKS nimmt ggf. einen Lastenausgleich zwischen verschiedenen Pods vor und kann für Pods jederzeit einen transparenten Neustart durchführen. Das dauerhafte Speichern eines veränderlichen Zustands in einem Dateisystem ist daher nicht zu empfehlen.

Falls das Erzielen von Sitzungspersistenz erforderlich ist, müssen Sie eine andere `PersistentManager`-Implementierung verwenden, bei der in einen externen Datenspeicher geschrieben wird, z. B. Pivotal-Sitzungs-Manager mit Redis Cache. Weitere Informationen finden Sie unter [Verwenden von Redis als Sitzungscache mit Tomcat](/azure/app-service/containers/configure-language-java#use-redis-as-a-session-cache-with-tomcat).

### <a name="special-cases"></a>Spezialfälle

Für bestimmte Produktionsszenarien sind unter Umständen zusätzliche Änderungen erforderlich, oder es gelten zusätzliche Einschränkungen. Szenarien dieser Art treten zwar meist nicht sehr häufig auf, aber Sie sollten trotzdem sicherstellen, dass sie für Ihre Anwendung entweder nicht zutreffen oder korrekt behoben werden.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Ermitteln, ob für die Anwendung geplante Aufträge benötigt werden

Geplante Aufträge, z. B. Quartz Scheduler-Aufgaben oder cron-Aufträge, können für Tomcat-Bereitstellungen in Containern nicht verwendet werden. Wenn Ihre Anwendung horizontal hochskaliert wird, wird ein geplanter Auftrag unter Umständen mehrmals pro geplantem Zeitraum ausgeführt. Diese Situation kann unerwünschte Konsequenzen haben.

Inventarisieren Sie alle geplanten Aufträge innerhalb oder außerhalb des Anwendungsservers.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Ermitteln, ob Ihre Anwendung betriebssystemspezifischen Code enthält

Falls Ihre Anwendung Code enthält, in den das Betriebssystem für die Ausführung Ihrer Anwendung einbezogen ist, müssen Sie Ihre Anwendung so umgestalten, dass KEINE Abhängigkeit vom zugrunde liegenden Betriebssystem besteht. Beispielsweise müssen alle Vorkommen von `/` oder `\` in Dateisystempfaden ggf. durch [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) oder [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-) ersetzt werden.

#### <a name="determine-whether-memoryrealm-is-used"></a>Ermitteln, ob MemoryRealm genutzt wird

Für [MemoryRealm](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/realm/MemoryRealm.html) wird eine persistente XML-Datei benötigt. Unter Kubernetes muss diese Datei dem Containerimage hinzugefügt oder [in freigegebenen Speicher hochgeladen werden, der für Container verfügbar gemacht wird](#identify-session-persistence-mechanism). Der Parameter `pathName` muss entsprechend geändert werden.

Gehen Sie wie folgt vor, um zu ermitteln, ob `MemoryRealm` derzeit verwendet wird: Untersuchen Sie Ihre *server.xml*- und *context.xml*-Dateien, und suchen Sie nach `<Realm>`-Elementen, für die das `className`-Attribut auf `org.apache.catalina.realm.MemoryRealm` festgelegt ist.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Ermitteln, ob die SSL-Sitzungsverfolgung genutzt wird

Bei Bereitstellungen in Containern werden SSL-Sitzungen normalerweise außerhalb des Anwendungscontainers angeordnet. Dies erfolgt meist über den Eingangscontroller. Wenn für Ihre Anwendung die [SSL-Sitzungsverfolgung](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL) benötigt wird, sollten Sie sicherstellen, dass SSL-Datenverkehr direkt an den Anwendungscontainer übergeben wird.

#### <a name="determine-whether-accesslogvalve-is-used"></a>Ermitteln, ob AccessLogValve genutzt wird

Bei Verwendung von [AccessLogValve](https://tomcat.apache.org/tomcat-9.0-doc/api/org/apache/catalina/valves/AccessLogValve.html) sollte der Parameter `directory` auf eine [bereitgestellte Azure Files-Freigabe](/azure/aks/azure-files-dynamic-pv) oder eines der zugehörigen Unterverzeichnisse festgelegt werden.

### <a name="in-place-testing"></a>Direktes Testen

Migrieren Sie Ihre Anwendung vor der Erstellung von Containerimages zu dem JDK und der Tomcat-Instanz, das bzw. die Sie unter AKS nutzen möchten. Führen Sie für Ihre Anwendung gründliche Tests durch, um die Kompatibilität und Leistung sicherzustellen.

### <a name="parameterize-the-configuration"></a>Parametrisieren der Konfiguration

Bei der Migrationsvorbereitung haben Sie in *server.xml*- und *context.xml*-Dateien ggf. Geheimnisse und externe Abhängigkeiten identifiziert, z. B. Datenquellen. Ersetzen Sie für alle hierbei identifizierten Elemente den Benutzernamen, das Kennwort, die Verbindungszeichenfolge und die URL durch eine Umgebungsvariable.

Angenommen, die *context.xml*-Datei enthält das folgende Element:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="jdbc:postgresql://postgresdb.contoso.com/wickedsecret?ssl=true"
    driverClassName="org.postgresql.Driver"
    username="postgres"
    password="t00secure2gue$$"
/>
```

In diesem Fall können Sie die Änderungen vornehmen, die im folgenden Beispiel angegeben sind:

```xml
<Resource
    name="jdbc/dbconnection"
    type="javax.sql.DataSource"
    url="${postgresdb.connectionString}"
    driverClassName="org.postgresql.Driver"
    username="${postgresdb.username}"
    password="${postgresdb.password}"
/>
```

## <a name="migration"></a>Migration

Wir empfehlen Ihnen, für jede zu migrierende Anwendung (WAR-Datei) die unten angegebenen Schritte auszuführen (mit Ausnahme des ersten Schritts „Bereitstellen von Containerregistrierung und AKS“).

> [!NOTE]
> Einige Tomcat-Bereitstellungen verfügen ggf. über mehrere Anwendungen, die auf einem einzelnen Tomcat-Server ausgeführt werden. Falls dies für Ihre Bereitstellung zutrifft, empfehlen wir Ihnen dringend, jede Anwendung in einem separaten Pod auszuführen. Auf diese Weise können Sie die Ressourcenverwendung für jede Anwendung optimieren und gleichzeitig die Komplexität und Kopplung minimieren.

### <a name="provision-container-registry-and-aks"></a>Bereitstellen von Containerregistrierung und AKS

Erstellen Sie eine Containerregistrierung und einen Azure Kubernetes-Cluster, für den der Dienstprinzipal in der Registrierung über die Rolle „Leser“ verfügt. Achten Sie darauf, dass Sie für die Netzwerkanforderungen Ihres Clusters [das richtige Netzwerkmodell auswählen](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model).

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```

### <a name="prepare-the-deployment-artifacts"></a>Vorbereiten der Bereitstellungsartefakte

Klonen Sie das [GitHub-Repository für die Schnellstartanleitung für Tomcat in Containern](https://github.com/Azure/tomcat-container-quickstart). Es enthält eine Dockerfile und Tomcat-Konfigurationsdateien mit einigen empfohlenen Optimierungen. In den unten angegebenen Schritten werden Änderungen beschrieben, die für diese Dateien ratsam sind, bevor Sie das Containerimage erstellen und unter AKS bereitstellen.

#### <a name="open-ports-for-clustering-if-needed"></a>Öffnen von Ports für das Clustering (falls erforderlich)

Wenn Sie das [Tomcat-Clustering](https://tomcat.apache.org/tomcat-9.0-doc/cluster-howto.html) unter AKS nutzen möchten, sollten Sie sicherstellen, dass in der Dockerfile die erforderlichen Portbereiche verfügbar gemacht werden. Achten Sie beim Angeben der Server-IP-Adresse in *server.xml* darauf, einen Wert aus einer Variablen zu verwenden, für die beim Starten des Containers die Initialisierung auf die IP-Adresse des Pods durchgeführt wird.

Alternativ kann der Sitzungszustand auch [an einem anderen Ort dauerhaft gespeichert werden](#identify-session-persistence-mechanism), damit er für alle Replikate verfügbar ist.

Suchen Sie für die Ermittlung, ob für Ihre Anwendung das Clustering verwendet wird, in der *server.xml*-Datei in den Elementen `<Host>` oder `<Engine>` nach dem `<Cluster>`-Element.

#### <a name="add-jndi-resources"></a>Hinzufügen von JNDI-Ressourcen

Bearbeiten Sie *server.xml*, um die Ressourcen hinzuzufügen, die Sie in den Schritten für die Migration vorbereitet haben, z. B. Datenquellen.

Beispiel:

```xml
<!-- Global JNDI resources
      Documentation at /docs/jndi-resources-howto.html
-->
<GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml"
               />

    <!-- Migrated datasources here: -->
    <Resource
        name="jdbc/dbconnection"
        type="javax.sql.DataSource"
        url="${postgresdb.connectionString}"
        driverClassName="org.postgresql.Driver"
        username="${postgresdb.username}"
        password="${postgresdb.password}"
    />
    <!-- End of migrated datasources -->
</GlobalNamingResources>
```

### <a name="build-and-push-the-image"></a>Erstellen und Pushen des Images

Die einfachste Möglichkeit zum Erstellen und Hochladen des Images in Azure Container Registry (ACR) zur Verwendung durch AKS ist die Nutzung des Befehls `az acr build`. Für diesen Befehl ist es nicht erforderlich, dass Docker auf Ihrem Computer installiert ist. Wenn Sie im aktuellen Verzeichnis beispielsweise über die obige Dockerfile und das Anwendungspaket *petclinic.war* verfügen, können Sie das Containerimage in ACR in nur einem Schritt erstellen:

```bash
az acr build -t "${acrName}.azurecr.io/petclinic:{{.Run.ID}}" -r $acrName --build-arg APP_FILE=petclinic.war --build-arg=prod.server.xml .
```

Sie können den Parameter `--build-arg APP_FILE...` weglassen, wenn Ihre WAR-Datei den Namen *ROOT.war* hat. Sie können den Parameter `--build-arg SERVER_XML...` weglassen, wenn die XML-Datei des Servers den Namen *server.xml* hat. Beide Dateien müssen sich in demselben Verzeichnis wie die *Dockerfile* befinden.

Alternativ können Sie die Docker-CLI verwenden, um das Image lokal zu erstellen. Dieser Ansatz kann das Testen und Optimieren des Images vor der ersten Bereitstellung unter ACR vereinfachen. Hierfür muss allerdings die Docker-CLI installiert und der Docker-Daemon ausgeführt werden.

```bash
# Build the image locally
sudo docker build . --build-arg APP_FILE=petclinic.war -t "${acrName}.azurecr.io/petclinic:1"

# Run the image locally
sudo docker run -d -p 8080:8080 "${acrName}.azurecr.io/petclinic:1"

# Your application can now be accessed with a browser at http://localhost:8080.

# Log into ACR
sudo az acr login -n $acrName

# Push the image to ACR
sudo docker push "${acrName}.azurecr.io/petclinic:1"
```

Ausführlichere Informationen zum Erstellen und Speichern von Containerimages in Azure finden Sie im entsprechenden [Microsoft Learn-Kurs](/learn/modules/build-and-store-container-images/).

### <a name="provision-a-public-ip-address"></a>Bereitstellen einer öffentlichen IP-Adresse

Wenn für Ihre Anwendung der Zugriff von außerhalb Ihrer internen oder virtuellen Netzwerke möglich sein soll, ist eine öffentliche statische IP-Adresse erforderlich. Diese IP-Adresse sollte innerhalb der Ressourcengruppe des Clusterknotens bereitgestellt werden.

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```

### <a name="deploy-to-aks"></a>Bereitstellen für AKS

[Erstellen und Anwenden Ihrer Kubernetes-YAML-Dateien](/azure/aks/kubernetes-walkthrough#run-the-application). Wenn Sie einen externen Lastenausgleich erstellen (ob für Ihre Anwendung oder einen Eingangscontroller), sollten Sie darauf achten, dass Sie die IP-Adresse aus dem vorherigen Abschnitt als `LoadBalancerIP` angeben.

Binden Sie [externalisierte Parameter als Umgebungsvariablen](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) ein. Vermeiden Sie die Einbindung von Geheimnissen (z. B. Kennwörter, API-Schlüssel und JDBC-Verbindungszeichenfolgen). Geheimnisse werden im Abschnitt [Konfigurieren von KeyVault FlexVolume](#configure-keyvault-flexvolume) beschrieben.

### <a name="configure-persistent-storage"></a>Konfigurieren von persistentem Speicher

Konfigurieren Sie ein oder mehrere [persistente Volumes](/azure/aks/azure-disks-dynamic-pv), wenn für Ihre Anwendung nicht flüchtiger Speicher benötigt wird.

Es kann ratsam sein, ein persistentes Volume mit Azure Files zu erstellen, das im Tomcat-Protokollverzeichnis ( */tomcat_logs*) bereitgestellt wird. Die Protokolle können dann zentral aufbewahrt werden. Weitere Informationen finden Sie unter [Dynamisches Erstellen und Verwenden eines persistenten Volumes mit Azure Files in Azure Kubernetes Service (AKS)](/azure/aks/azure-files-dynamic-pv).

### <a name="configure-keyvault-flexvolume"></a>Konfigurieren von KeyVault FlexVolume

[Erstellen Sie eine Azure KeyVault-Instanz](/azure/key-vault/quick-create-cli), und fügen Sie alle erforderlichen Geheimnisse ein. Konfigurieren Sie anschließend eine [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md)-Instanz, um diese Geheimnisse für Pods zugänglich zu machen.

Sie müssen das Startskript ändern (*startup.sh* im GitHub-Repository für [Tomcat in Containern](https://github.com/Azure/tomcat-container-quickstart)), um die Zertifikate in den lokalen Keystore des Containers zu importieren.

### <a name="migrate-scheduled-jobs"></a>Migrieren von geplanten Aufträgen

Definieren Sie zum Ausführen von geplanten Aufträgen in Ihrem AKS-Cluster je nach Bedarf [Cron-Aufträge](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/).

## <a name="post-migration"></a>Nach der Migration

Nachdem Sie Ihre Anwendung zu AKS migriert haben, sollten Sie sich vergewissern, dass sie wie erwartet funktioniert. Als Nächstes können Sie sich über unsere Empfehlungen informieren, mit denen Sie Ihre Anwendung cloudnativer gestalten können.

* Erwägen Sie, [einen DNS-Namen der IP-Adresse hinzuzufügen](/azure/aks/ingress-static-ip#configure-a-dns-name), die Ihrem Eingangscontroller oder dem Lastenausgleichsmodul der Anwendung zugeordnet ist.

* Erwägen Sie, [HELM-Diagramme für Ihre Anwendung hinzuzufügen](https://helm.sh/docs/topics/charts/). Mit einem Helm-Diagramm können Sie Ihre Anwendungsbereitstellung parametrisieren, damit diese von unterschiedlichen Kunden genutzt werden kann.

* Entwerfen und implementieren Sie eine DevOps-Strategie. Sie können [Bereitstellungen automatisieren und mit Azure Pipelines testen](/azure/devops/pipelines/ecosystems/kubernetes/aks-template), um die Zuverlässigkeit sicherzustellen, während gleichzeitig die Entwicklungsgeschwindigkeit erhöht wird.

* Aktivieren Sie die [Azure-Überwachung für den Cluster](/azure/azure-monitor/insights/container-insights-enable-existing-clusters), um das Sammeln von Containerprotokollen, Nachverfolgen der Nutzung und andere Optionen zu ermöglichen.

* Erwägen Sie, anwendungsspezifische Metriken über Prometheus verfügbar zu machen. Prometheus ist ein Open-Source-Framework für Metriken, das von der Kubernetes-Community viel genutzt wird. Sie können die [Erfassung von Prometheus-Metriken in Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration) konfigurieren, anstatt Ihren eigenen Prometheus-Server zu hosten. So ermöglichen Sie die Aggregation von Metriken aus Ihren Anwendungen und die automatisierte Reaktion auf anomale Bedingungen bzw. deren Eskalation.

* Entwerfen und implementieren Sie eine Strategie für Geschäftskontinuität und Notfallwiederherstellung. Bei unternehmenskritischen Anwendungen sollten Sie erwägen, eine [Bereitstellungsarchitektur mit mehreren Regionen](/azure/aks/operator-best-practices-multi-region) zu verwenden.

* Informieren Sie sich über die [Richtlinie zur Unterstützung der Kubernetes-Version](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). Wir sind dafür verantwortlich, [Ihren AKS-Cluster zu aktualisieren und auf dem neuesten Stand zu halten](/azure/aks/upgrade-cluster), damit sichergestellt ist, dass immer eine unterstützte Version ausgeführt wird.

* Sorgen Sie dafür, dass alle Teammitglieder, die für die Clusterverwaltung und Anwendungsentwicklung zuständig sind, über die entsprechenden [bewährten Methoden für AKS](/azure/aks/best-practices) informiert sind.

* Sehen Sie sich die Elemente in der Datei *logging.properties* an. Erwägen Sie, einen Teil der Protokollierungsausgabe zu entfernen bzw. zu reduzieren, um die Leistung zu verbessern.

* Sie haben auch die Möglichkeit, [die Größe des Codecaches zu überwachen](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) und der Variablen `JAVA_OPTS` in der Dockerfile die Parameter `-XX:InitialCodeCacheSize` und `-XX:ReservedCodeCacheSize` hinzuzufügen, um die Leistung weiter zu optimieren.
