---
title: 'Schnellstart: Konfigurieren eines virtuellen Windows-Computers in Azure mithilfe von Chef'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Chef einen virtuellen Windows-Computer in Azure bereitstellen und konfigurieren.
keywords: Chef, Azure, DevOps, virtueller Computer
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 17fc56cbf3aaed573cead58eb8d436d99efa391b
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "80893049"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Schnellstart: Konfigurieren eines virtuellen Windows-Computers in Azure mithilfe von Chef

Chef ermöglicht die Bereitstellung von Konfigurationen für die Automatisierung und den gewünschten Zustand.

Mit der aktuellen Veröffentlichung der Cloud-API bietet Chef eine nahtlose Integration in Azure und Ihnen die Möglichkeit, den Konfigurationsstatus über einen einzelnen Befehl bereitzustellen und zu implementieren.

In diesem Artikel wird die Chef-Umgebung für die Bereitstellung virtueller Azure-Computer eingerichtet, und Sie erfahren Schritt für Schritt, wie Sie eine Richtlinie oder ein Cookbook erstellen und dieses anschließend auf einem virtuellen Azure-Computer bereitstellen.

## <a name="chef-basics"></a>Grundlagen zu Chef

Machen Sie sich zunächst mit den [grundlegenden Konzepten von Chef](https://www.chef.io/chef) vertraut.

Das folgende Diagramm zeigt die allgemeine Chef-Architektur:

![Chef-Architektur](media/windows-vm-configure/chef-architecure.png)

Die Architektur von Chef setzt sich aus drei Hauptkomponenten zusammen: 
- Chef-Server: Der Verwaltungspunkt. Kann als gehostete Lösung oder als lokale Lösung verwendet werden.
- Chef-Client (Knoten): Der Agent, der sich auf den verwalteten Servern befindet.
- Chef-Arbeitsstation: Der Name für die Administrator-Arbeitsstation, auf der Sie Richtlinien erstellen und Verwaltungsbefehle ausführen, sowie für das Softwarepaket mit Chef-Tools.

Im Allgemeinen wird der Ort, an dem Sie Befehle ausführen als **Ihre Arbeitsstation** und das Softwarepaket als **Chef-Arbeitsstation** bezeichnet.

Beispiel: Sie laden den Befehl „knife“ als Teil der **Chef-Arbeitsstation** herunter, führen knife-Befehle jedoch über **Ihre Arbeitsstation** aus, um die Infrastruktur zu verwalten.

Chef nutzt auch sogenannte *Cookbooks* und *Rezepte*. Dabei handelt es sich um die Richtlinien, die für die Server definiert bzw. auf sie angewendet werden.

## <a name="preparing-your-workstation"></a>Vorbereiten der Arbeitsstation

Bereiten Sie zunächst Ihre Arbeitsstation vor, indem Sie ein Verzeichnis zum Speichern von Chef-Konfigurationsdateien und Rezeptbüchern (Cookbooks) erstellen.

Erstellen Sie ein Verzeichnis namens `C:\Chef`.

Laden Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) herunter, und installieren Sie sie auf Ihrer Arbeitsstation.

## <a name="configure-azure-service-principal"></a>Konfigurieren eines Azure-Dienstprinzipals

Der Dienstprinzipal hilft uns bei der Erstellung von Azure-Ressourcen über die Chef-Arbeitsstation.  Führen Sie in PowerShell die folgenden Befehle aus, um den Dienstprinzipal mit den erforderlichen Berechtigungen zu erstellen:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Notieren Sie sich Ihre Abonnement-ID (SubscriptionID), Ihre Mandanten-ID (TenantID), Ihre Client-ID (ClientID) und Ihren geheimen Clientschlüssel (das zuvor in diesem Tutorial festgelegte Kennwort) für später. 

## <a name="configure-chef-server"></a>Konfigurieren von Chef Server

Dieser Leitfaden setzt voraus, dass Sie sich für Hosted Chef registrieren.

Wenn Sie noch keinen Chef-Server verwenden, haben Sie folgende Möglichkeiten:

* Registrieren Sie sich für [Hosted Chef](https://manage.chef.io/signup). Dies ist die schnellste Möglichkeit zum Einstieg in Chef.
* Installieren Sie einen eigenständigen Chef-Server auf einem Linux-basierten Computer. Folgen Sie dazu den [Installationsanweisungen](https://docs.chef.io/install_server.html) in der [Chef-Dokumentation](https://docs.chef.io/).

### <a name="create-a-hosted-chef-account"></a>Erstellen eines Hosted Chef-Kontos

Registrieren Sie sich [hier](https://manage.chef.io/signup) für ein gehostetes Chef-Konto.

Während der Registrierung werden Sie aufgefordert, eine neue Organisation zu erstellen.

![Fenster für die Organisationserstellung](media/windows-vm-configure/create-organization.png)

Sobald Ihre Organisation erstellt wurde, laden Sie das Starterkit herunter.

![Konfigurieren von Chef](media/windows-vm-configure/configure-chef.png)

> [!NOTE]
> Wenn Sie eine Meldung mit dem Hinweis erhalten, dass Ihre Schlüssel zurückgesetzt werden, können Sie den Vorgang problemlos fortsetzen, da wir noch keine Infrastruktur konfiguriert haben.
>

Die ZIP-Datei dieses Starterkits enthält die Konfigurationsdateien und den Benutzerschlüssel für Ihre Organisation im Verzeichnis `.chef`.

Die Datei `organization-validator.pem` muss separat heruntergeladen werden, da es sich dabei um einen privaten Schlüssel handelt und private Schlüssel nicht auf dem Chef-Server gespeichert werden sollten. Wählen Sie unter [Chef Manage](https://manage.chef.io/) im Verwaltungsbereich die Option „Reset Validation Key“ (Validierungsschlüssel zurücksetzen) aus, um eine separat herunterladbare Datei zu erhalten. Speichern Sie die Datei unter „C:\chef“.

### <a name="configure-a-chef-workstation"></a>Konfigurieren einer Chef-Arbeitsstation

Extrahieren Sie den Inhalt der Datei `chef-starter.zip` in `c:\chef`.

Kopieren Sie alle Dateien aus `chef-starter\chef-repo\.chef` in das Verzeichnis `c:\chef`.

Kopieren Sie die Datei `organization-validator.pem` in `c:\chef`, falls sie in `c:\Downloads` gespeichert wurde.

Ihr Verzeichnis sollte in etwa wie folgt aussehen:

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Das Stammverzeichnis „C:\chef“ sollte jetzt fünf Dateien und vier Verzeichnisse (einschließlich des leeren Verzeichnisses „chef-repo“) enthalten.

### <a name="edit-kniferb"></a>Bearbeiten von „knife.rb“

Die PEM-Dateien enthalten die privaten Schlüssel für die Organisation und Administration für die Kommunikation, während die Datei „knife.rb“ die knife-Konfiguration enthält. Wir müssen die Datei knife.rb bearbeiten.

Öffnen Sie die Datei „knife.rb“ in einem Editor Ihrer Wahl. Die unveränderte Datei sollte etwa so aussehen:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Fügen Sie der Datei „knife.rb“ die folgenden Informationen hinzu, und ersetzen Sie dabei die Platzhalter durch Ihre eigenen Informationen:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Durch diese Zeilen wird sichergestellt, dass Knife auf das Cookbookverzeichnis unter `c:\chef\cookbooks` verweist.

Die Datei `knife.rb` sollte nun in etwa wie im folgenden Beispiel aussehen:

![Beispiel für die Knife-Datei](./media/windows-vm-configure/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Installieren der Chef-Arbeitsstation

[Laden Sie als Nächstes die Chef-Arbeitsstation herunter, und installieren Sie sie.](https://downloads.chef.io/chef-workstation/)

Installieren Sie die Chef-Arbeitsstation am Standardort.

Auf dem Desktop wird „CW PowerShell“ angezeigt. Dieses Tool wird für die Interaktion mit Chef-Produkten verwendet. „CW PowerShell“ macht neue Befehle verfügbar – beispielsweise `chef-run` und Chef-CLI-Befehle (etwa `chef`). Mit `chef -v` zeigen Sie Ihre installierte Version der Chef-Arbeitsstation und der Chef-Tools an. Die Version Ihrer Arbeitsstation können Sie auch überprüfen, indem Sie in der Chef Workstation-App die Option **About Chef Workstation** (Über Chef Workstation) auswählen.

`chef --version` sollte etwa Folgendes zurückgeben:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> Die Reihenfolge der Pfade ist wichtig! Wenn Ihre opscode-Pfade nicht in der richtigen Reihenfolge angegeben sind, treten Probleme auf.
>

Starten Sie die Arbeitsstation neu, bevor Sie fortfahren.

### <a name="install-knife-azure"></a>Installieren von Knife Azure

In diesem Tutorial wird davon ausgegangen, dass Sie Azure Resource Manager für die Interaktion mit Ihrem virtuellen Computer verwenden.

Installieren Sie die Knife-Erweiterung für Azure. Diese enthält auch das Azure-Plug-In.

Führen Sie den folgenden Befehl aus.

    chef gem install knife-azure ––pre

> [!NOTE]
> Durch das Argument `–-pre` wird sichergestellt, dass Sie die neueste RC-Version des Azure-Plug-Ins für Knife erhalten, das Zugriff auf die neuesten APIs bietet.
>
>

Wahrscheinlich wird gleichzeitig auch eine Reihe von Abhängigkeiten installiert.

![Ausgabe beim Installieren der Knife-Erweiterung für Azure](./media/windows-vm-configure/install-knife-azure.png)

Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass alles korrekt konfiguriert ist:

    knife azurerm server list

Wenn alles richtig konfiguriert ist, wird eine Liste der verfügbaren Azure-Images angezeigt.

Herzlichen Glückwunsch. Die Arbeitsstation ist eingerichtet!

## <a name="create-a-chef-cookbook"></a>Erstellen eines Chef-Cookbooks

Ein Cookbook wird von Chef verwendet, um eine Reihe von Befehlen zu definieren, die auf Ihrem verwalteten Client ausgeführt werden sollen. Verwenden Sie zur Cookbookerstellung einfach den Befehl `chef generate cookbook`, um die Cookbookvorlage zu generieren. Dieses Cookbook ist für einen Webserver vorgesehen, der automatisch IIS bereitstellt.

Führen Sie unter `C:\Chef directory` den folgenden Befehl aus.

    chef generate cookbook webserver

Mit diesem Befehl werden eine Reihe von Dateien im Verzeichnis C:\Chef\cookbooks\webserver generiert. Als Nächstes wird der Satz von Befehlen definiert, die der Chef-Client auf dem verwalteten virtuellen Computer ausführen soll.

Die Befehle werden in der Datei default.rb gespeichert. Definieren Sie in dieser Datei eine Reihe von Befehlen, um IIS zu installieren und zu starten sowie um eine Vorlagendatei in den Ordner `wwwroot` zu kopieren.

Ändern Sie die Datei „C:\chef\cookbooks\webserver\recipes\default.rb“, und fügen Sie die folgenden Zeilen hinzu:

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Speichern Sie die Datei, sobald Sie fertig sind.

## <a name="create-a-template"></a>Erstellen einer Vorlage

In diesem Schritt wird eine Vorlagendatei für die Seite `default.html` generiert.

Führen Sie den folgenden Befehl aus, um die Vorlage zu generieren:

    chef generate template webserver Default.htm

Navigieren Sie zur Datei `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Fügen Sie der Datei etwas einfachen HTML-Code (*Hello World*) hinzu, und speichern Sie sie.

## <a name="upload-the-chef-cookbook-to-the-chef-server"></a>Hochladen des Chef-Cookbooks auf den Chef-Server

In diesem Schritt wird das Cookbook, das Sie auf dem lokalen Computer erstellt haben, kopiert und auf den von Chef gehosteten Server hochgeladen. Das hochgeladene Cookbook wird dann auf der Registerkarte **Policy** (Richtlinie) angezeigt.

    knife cookbook upload webserver

![Ergebnisse der Installation des Cookbooks auf dem Chef-Server](./media/windows-vm-configure/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Bereitstellen eines virtuellen Computers mit Knife Azure

Verwenden Sie den Befehl `Webserver`, um einen virtuellen Azure-Computer bereitzustellen und das Cookbook `knife` anzuwenden.

Mit dem Befehl `knife` werden auch der IIS-Webdienst und die Standardwebseite installiert.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

Mit dem Beispielbefehl für `knife` wird ein virtueller Computer vom Typ *Standard_DS2_v2* mit Windows Server 2016 in der Region „USA, Westen“ erstellt. Passen Sie diese Werte gemäß Ihren App-Anforderungen an.

Wechseln Sie nach Ausführung des Befehls zum Azure-Portal, um die Bereitstellung Ihres virtuellen Computers zu verfolgen.

![Bereitstellung des virtuellen Computers](./media/windows-vm-configure/virtual-machine-being-provisioned.png)

Anschließend erscheint die Eingabeaufforderung.

![Knife-Ausgabe beim Erstellen eines virtuellen Computers](./media/windows-vm-configure/knife-output-when-creating-vm.png)

Nach Abschluss der Bereitstellung wird die öffentliche IP-Adresse des neuen virtuellen Computers angezeigt. Fügen Sie diesen Wert in einen Webbrowser ein, um die neue Website anzuzeigen. Da wir im Zuge der Bereitstellung des virtuellen Computers den Port 80 geöffnet haben, sollte er extern verfügbar sein.

![Testen des virtuellen Computers](./media/windows-vm-configure/vm-test.png)

In diesem Beispiel wird kreativer HTML-Code verwendet.

Sie können den Zustand des Knotens auch unter [Chef Manage](https://manage.chef.io/) anzeigen.

![Anzeigen des Knotenstatus](./media/windows-vm-configure/view-node-status.png)

Denken Sie daran, dass Sie die Verbindung auch im Azure-Portal per RDP-Sitzung über den Port 3389 herstellen können.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Chef in Azure](/azure/chef/)