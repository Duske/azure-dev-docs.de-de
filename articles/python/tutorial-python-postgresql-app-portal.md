---
title: 'Tutorial: Bereitstellen einer Django-App mit PostgreSQL mithilfe des Azure-Portals'
description: Stellen Sie eine Web-App und eine PostgreSQL-Datenbank in Azure sowie App-Code über GitHub bereit.
ms.devlang: python
ms.topic: tutorial
ms.date: 10/09/2020
ms.custom: devx-track-python
ms.openlocfilehash: 333cda811133e9ce4e83730b038a7d84b40b7fa1
ms.sourcegitcommit: ca7b58f60dd02709977b35175b43be582b868b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629934"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-using-the-azure-portal"></a>Tutorial: Bereitstellen einer Django-Web-App mit PostgreSQL mithilfe des Azure-Portals

Mithilfe des Azure-Portals können Sie eine datengesteuerte Python-[Django](https://www.djangoproject.com/)-Web-App in [Azure App Service](/azure/app-service/overview#app-service-on-linux) bereitstellen und mit einer [Azure Database for PostgreSQL](/azure/postgresql/)-Datenbank verbinden. Sie können mit einem kostenlosen Tarif beginnen, der zu einem späteren Zeitpunkt hochskaliert werden kann.

Der Web-App-Code stammt in diesem Fall aus einem GitHub-Repository, und Sie konfigurieren die Web-App für Continuous Deployment über GitHub. Nach der Konfiguration können Sie auf dem lokalen Computer weitere Entwicklungsschritte ausführen und die Änderungen im Repository committen. Die Web-App in Azure stellt diese Änderungen dann automatisch bereit.

In diesem Tutorial wird das Azure-Portal für die folgenden Aufgaben verwendet:

> [!div class="checklist"]
> - Bereitstellen einer Web-App in Azure, die über ein GitHub-Repository bereitgestellt wird
> - Bereitstellen eines PostgreSQL-Servers und einer Datenbank in Azure und Herstellen einer Verbindung zwischen ihr und der Web-App
> - Aktualisieren des Codes und Committen der Änderungen für die automatische erneute Bereitstellung über GitHub
> - Anzeigen von Diagnoseprotokollen
> - Verwalten der Web-App im Azure-Portal

Sie können auch die **[auf der Azure CLI basierende Version dieses Tutorials](/azure/app-service/tutorial-python-postgresql-app)** verwenden.

## <a name="fork-the-sample-repository"></a>Forken des Beispielrepositorys

Navigieren Sie in einem Browser zu [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp), und forken Sie das Repository in Ihr eigenes GitHub-Konto.

Sie erstellen einen Fork dieses Repositorys, damit Sie Änderungen vornehmen und den Code in einem späteren Schritt erneut bereitstellen können.

**(Optional) Infos zum Beispiel:** Das Beispiel „djangoapp“ enthält die datengesteuerte Django-Umfrage-App, die Sie erhalten, indem Sie die in der Django-Dokumentation unter [Schreiben Ihrer ersten Django-App](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) beschriebenen Schritte ausführen. Das Beispiel wird darüber hinaus mithilfe der [Bereitstellungsprüfliste für Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) für die Ausführung in einer Produktionsumgebung (etwa Azure App Service) geändert. (Diese Änderungen gelten für alle Produktionsumgebungen und nicht speziell für Azure.)

- Die Produktionseinstellungen befinden sich in der Datei *azuresite/production.py*. Die Entwicklungsdetails befinden sich in *azuresite/settings.py*.

- Von der App werden Produktionseinstellungen verwendet, wenn die Umgebungsvariable `DJANGO_ENV` auf „production“ festgelegt ist. Diese Umgebungsvariable wird später in diesem Tutorial zusammen mit anderen Umgebungsvariablen für die PostgreSQL-Datenbankkonfiguration erstellt.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-web-app-in-azure"></a>Bereitstellen der Web-App in Azure

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Wählen Sie **Ressource erstellen** aus. Daraufhin wird die Seite **Neu** geöffnet.

1. Suchen Sie nach **Web-App** , und wählen Sie die Option aus.

1. Geben Sie auf der Seite **Web-App erstellen** die folgenden Informationen ein:

    | Feld | Wert |
    | --- | --- |
    | Subscription | Wählen Sie das zu verwendende Abonnement aus, falls dieses von der Standardeinstellung abweicht. |
    | Resource group | Wählen Sie **Neu erstellen** aus, und geben Sie „DjangoPostgres-Tutorial-rg“ ein. |
    | App-Name | Ein Name für Ihre Web-App, der überall in Azure eindeutig ist (die URL der App lautet `https://<app-name>.azurewebsites.net`). Gültige Zeichen sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID. |
    | Veröffentlichen | Wählen Sie **Code** aus. |
    | Laufzeitstapel | Wählen Sie in der Dropdownliste den Eintrag **Python 3.8** aus. |
    | Region | Wählen Sie einen Ort in Ihrer Nähe aus. |
    | Linux-Plan | Das Portal füllt dieses Feld mit dem Namen des App Service-Plans auf der Grundlage Ihrer Ressourcengruppe auf. Wenn Sie den Namen ändern möchten, wählen Sie **Neu erstellen**  aus. |
    | SKU und Größe | Verwenden Sie zum Erzielen der besten Leistung den Standardplan, auch wenn dafür Gebühren in Ihrem Abonnement anfallen. Wählen Sie **Größe ändern** > **Dev/Test** > **B1** (30 Tage lang kostenlos) und dann **Anwenden** aus, um Gebühren zu vermeiden. Sie können den Plan später skalieren, um die Leistung zu verbessern. |

1. Wählen Sie **Überprüfen + erstellen** und dann **Erstellen** aus. Das Bereitstellen der Web-App dauert in Azure einige Minuten.

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus, um die Übersichtsseite für die Web-App zu öffnen. Lassen Sie dieses Browserfenster oder diese Browserregisterkarte für spätere Schritte geöffnet.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="provision-the-postgresql-database-server-in-azure"></a>Bereitstellen des PostgreSQL-Datenbankservers in Azure

1. Öffnen Sie ein neues Browserfenster oder eine neue Browserregisterkarte mit dem [Azure-Portal](https://portal.azure.com). Sie verwenden eine neue Registerkarte für die Bereitstellung der Datenbank, da Sie einige Informationen von der Datenbankseite auf die Web-App-Seite übertragen müssen, die noch vom vorherigen Abschnitt geöffnet ist.

1. Wählen Sie **Ressource erstellen** aus. Daraufhin wird die Seite **Neu** geöffnet.

1. Suchen Sie nach **Azure Database for PostgreSQL** , und wählen Sie die Option aus.

1. Wählen Sie auf der nächsten Seite unter **Einzelserver** die Option **Erstellen** aus.

1. Geben Sie auf der Seite **Einzelserver** die folgenden Informationen ein:

    | Feld | Wert |
    | --- | --- |
    | Subscription | Wählen Sie das zu verwendende Abonnement aus, falls dieses von der Standardeinstellung abweicht. |
    | Resource group | Wählen Sie die im vorherigen Abschnitt erstellte Gruppe „DjangoPostgres-Tutorial-rg“ aus. |
    | Servername | Ein Name für den Datenbankserver, der überall in Azure eindeutig ist (die URL des Datenbankservers lautet `https://<server-name>.postgres.database.azure.com`). Gültige Zeichen sind `A`-`Z`, `0`-`9` und `-`. Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer Server-ID. |
    | Datenquelle | **None** |
    | Standort | Wählen Sie einen Ort in Ihrer Nähe aus. |
    | Version | Übernehmen Sie den Standardwert (die neueste Version). |
    | Compute + Speicher | Wählen Sie **Server konfigurieren** > **Basic** > **Gen 5** aus. Legen Sie **Virtueller Kern** auf 1 und **Speicher** auf 5 GB fest, und wählen Sie dann **OK** aus. Mit diesen Einstellungen wird der günstigste Server bereitgestellt, der für PostgreSQL in Azure verfügbar ist. Unter Umständen verfügen Sie auch über Guthaben in Ihrem Azure-Konto, das die Kosten für den Server abdeckt. |
    | Administratorbenutzername, Kennwort, Kennwort bestätigen | Geben Sie die Anmeldeinformationen für ein Administratorkonto auf dem Datenbankserver ein. Notieren Sie sich diese Anmeldeinformationen, da Sie sie später in diesem Tutorial benötigen. Hinweis: Verwenden Sie das Zeichen `$` nicht im Benutzernamen oder Kennwort. Später erstellen Sie Umgebungsvariablen mit diesen Werten. Dabei hat das Zeichen `$` innerhalb des Linux-Containers, der zum Ausführen von Python-Apps verwendet wird, eine besondere Bedeutung. |

1. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus. Das Bereitstellen der Web-App dauert in Azure einige Minuten.

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus, um die Übersichtsseite für den Datenbankserver zu öffnen.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="create-the-pollsdb-database-on-the-postgresql-server"></a>Erstellen der Datenbank „pollsdb“ auf dem PostgreSQL-Server

In diesem Abschnitt stellen Sie eine Verbindung mit dem Datenbankserver in Azure Cloud Shell her und verwenden einen PostgreSQL-Befehl, um die Datenbank „pollsdb“ auf dem Server zu erstellen. Diese Datenbank wird vom Code der Beispiel-App erwartet.

1. Wählen Sie auf der Übersichtsseite des PostgreSQL-Servers **Verbindungssicherheit** aus (auf der linken Seite unter **Einstellungen** ).

    ![Portalseite „Verbindungssicherheit“ für Firewallregeln](media/tutorial-python-postgresql-app-portal/server-firewall-rules.png)

1. Wählen Sie die Schaltfläche **0.0.0.0 bis 255.255.255.255 hinzufügen** aus. Wählen Sie in der daraufhin angezeigten Popupmeldung **Weiter** und anschließend oben auf der Seite **Speichern** aus. Mit diesen Aktionen wird eine Regel hinzugefügt, die es Ihnen ermöglicht, über Cloud Shell sowie über SSH eine Verbindung mit dem Datenbankserver herzustellen. Informationen zur Verwendung von SSH für die Ausführung einer Django-Datenmodellmigration finden Sie weiter unten.

1. Öffnen Sie Azure Cloud Shell über das Azure-Portal, indem Sie das Cloud Shell-Symbol im oberen Fensterbereich auswählen:

    ![Cloud Shell-Schaltfläche auf der Symbolleiste im Azure-Portal](media/tutorial-python-postgresql-app-portal/portal-launch-icon.png)

1. Führen Sie in der Cloud Shell-Instanz folgenden Befehl aus:

    ```bash
    psql --host=<server-name>.postgres.database.azure.com --port=5432 --username=<user-name>@<server-name> --dbname=postgres
    ```

    Ersetzen Sie `<server-name>` und `<user-name>` durch die Namen, die im vorherigen Abschnitt beim Konfigurieren des Servers verwendet wurden. Beachten Sie, dass der vollständige Wert für den Benutzernamen, der für Postgres erforderlich ist, `<user-name>@<server-name>` lautet.

    Sie können den Befehl oben kopieren und in Cloud Shell einfügen. Klicken Sie dazu mit der rechten Maustaste, und wählen Sie **Einfügen** aus.

    Geben Sie Ihr Administratorkennwort ein, wenn Sie dazu aufgefordert werden.

1. Wenn die Shell eine Verbindung hergestellt hat, sollte die Eingabeaufforderung `postgres=>` angezeigt werden. Diese Eingabeaufforderung weist darauf hin, dass Sie mit der administrativen Standarddatenbank namens „postgres“ verbunden sind. (Die Datenbank „postgres“ ist nicht für die App-Nutzung konzipiert.)

1. Führen Sie an der Eingabeaufforderung den Befehl `CREATE DATABASE pollsdb;` aus. Schließen Sie unbedingt das Semikolon am Ende mit ein, da es das Ende des Befehls signalisiert.

1. Wurde die Datenbank erfolgreich erstellt, sollte vom Befehl `CREATE DATABASE` angezeigt werden. Um zu überprüfen, ob die Datenbank erstellt wurde, führen Sie `\c pollsdb` aus. Durch diesen Befehl wird die Eingabeaufforderung in `pollsdb=>` geändert, was auf eine erfolgreiche Erstellung hinweist.

1. Beenden Sie psql mithilfe des Befehls `exit`.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="connect-the-database"></a>Herstellen einer Verbindung mit der Datenbank

In diesem Abschnitt erstellen Sie Einstellungen für die Web-App, die für das Herstellen einer Verbindung mit der Datenbank `pollsdb` erforderlich sind. Diese Einstellungen werden für den App-Code als Umgebungsvariablen dargestellt. Weitere Informationen finden Sie unter [Zugreifen auf Umgebungsvariablen](/azure/app-service/configure-language-python#access-environment-variables).

1. Wechseln Sie zurück zur Browserregisterkarte oder zum Browserfenster für die Web-App, die Sie in einem vorherigen Abschnitt erstellt haben.

1. Wählen Sie **Konfiguration** (auf der linken Seite unter **Einstellungen** ) und dann oben auf der Seite **Anwendungseinstellungen** aus.

    ![Konfiguration der Portaleinstellungen für Web-Apps](media/tutorial-python-postgresql-app-portal/web-app-settings.png)

1. Verwenden Sie die Schaltfläche **Neue Anwendungseinstellung** , um Einstellungen für die folgenden Werte zu erstellen (diese werden vom Beispiel „djangoapp“ erwartet):

    | Einstellungsname | Wert |
    | --- | --- |
    | DJANGO_ENV | `production` (Dieser Wert weist die App an, eine Produktionskonfiguration zu verwenden, wie weiter oben in der [Beispielübersicht](#fork-the-sample-repository) beschrieben.) |
    | DBHOST | Der Name des Datenbankservers aus dem vorherigen Abschnitt, d. h. der Teil `<server-name>` der Server-URL, der `.postgres.database.azure.com` vorangestellt ist. (Der Code in *azuresite/production.py* erstellt die vollständige URL automatisch.) |
    | DBNAME | `pollsdb` |
    | DBUSER | Der Benutzername des Administrators, der bei der Bereitstellung der Datenbank verwendet wurde. (Im Beispielcode wird automatisch der Teil `@<server-name>` hinzugefügt, siehe *azuresite/production.py*.) |
    | DBPASS | Das Administratorkennwort, das Sie zuvor erstellt haben |

    Wie bereits erwähnt, darf das Zeichen `$` nicht im Benutzernamen oder Kennwort verwendet werden, da dieses Zeichen innerhalb von Umgebungsvariablen in dem Linux-Container, der als Host für Python-Apps fungiert, mit Escapezeichen versehen wird.

1. Wählen Sie **Speichern** und anschließend **Weiter** aus, um diese Einstellungen zu übernehmen.

    > [!IMPORTANT]
    > Wenn Sie Änderungen an den Einstellungen vorgenommen haben, müssen Sie **Speichern** auswählen. Alle Einstellungen, die Sie mithilfe der Schaltfläche **Neue Anwendungseinstellung** erstellen, werden erst angewendet, wenn Sie auf **Speichern** klicken.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="deploy-app-code-to-the-web-app-from-a-repository"></a>Bereitstellen von App-Code in der Web-App über ein Repository

Nach dem Einrichten der Datenbank und der Verbindungseinstellungen können Sie nun die Web-App so konfigurieren, dass sie Code direkt über ein GitHub-Repository bereitstellt.

1. Wählen Sie im Browserfenster oder auf der Browserregisterkarte der Web-App **Bereitstellungscenter** aus (auf der linken Seite unter **Bereitstellung** ).

1. Wählen Sie im Schritt **Quellcodeverwaltung** die Option **GitHub** und dann **Autorisieren** aus (falls erforderlich). Befolgen Sie dann die Anmeldeaufforderungen, oder wählen Sie **Weiter** aus, um Ihre aktuelle GitHub-Anmeldung zu verwenden.

1. Wählen Sie im Schritt **Buildanbieter** die Option **App Service-Builddienst** und anschließend **Weiter** aus.

1. Wählen Sie im Schritt **Konfigurieren** die folgenden Werte aus:

    | Feld | Wert |
    | --- | --- |
    | Organization | Das GitHub-Konto, in das Sie das Beispielrepository geforkt haben |
    | Repository | djangoapp |
    | Verzweigung | master |

1. Wählen Sie zum Auswählen des Repositorys **Weiter** und anschließend **Fertig stellen** aus. Azure sollte den Code innerhalb weniger Sekunden bereitstellen und die App starten.

    Zur Erkennung von Django-Projekten wird von App Service jedes Unterverzeichnis nach einer Datei namens *wsgi.py* durchsucht. Wird die Datei von App Service gefunden, wird die Django-Web-App geladen. Weitere Informationen finden Sie unter [Konfigurieren einer Linux-Python-App für Azure App Service](/azure/app-service/configure-language-python).

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="run-django-database-migrations"></a>Ausführen einer Django-Datenbankmigration

Wurden Code und Datenbank bereitgestellt, ist die App fast bereit für die Verwendung. Nun muss nur noch das erforderliche Schema in der Datenbank selbst eingerichtet werden. Hierzu können Sie die Datenmodelle in der Django-App zur Datenbank migrieren.

1. Wählen Sie im Browserfenster oder auf der Registerkarte für die Web-App die Option **SSH** (links unter **Entwicklungstools** ) und dann **Starten** aus, um auf dem Web-App-Server eine SSH-Konsole zu öffnen. Es kann eine Minute dauern, bis die Verbindung zum ersten Mal hergestellt wird, da der Web-App-Container gestartet werden muss.

1. Navigieren Sie in der Konsole zum Ordner der Web-App:

    ```bash
    cd site/wwwroot
    ```

1. Aktivieren Sie die virtuelle Umgebung des Containers:

    ```bash
    source /antenv/bin/activate
    ```

1. Installieren von Python-Paketen:

    ```bash
    pip install -r requirements.txt
    ```

1. Führen Sie eine Datenbankmigration aus:

    ```bash
    python manage.py migrate
    ```

1. Erstellen Sie eine Administratoranmeldung für die App:

    ```bash
    python manage.py createsuperuser
   ```

    Mit dem Befehl `createsuperuser` werden Sie zur Eingabe von Django-Superuser-Anmeldeinformationen (oder Administratoranmeldeinformationen) aufgefordert, die in der Web-App verwendet werden. Verwenden Sie für dieses Tutorial den Standardbenutzernamen `root`, drücken Sie für die E-Mail-Adresse die **EINGABETASTE** , um sie leer zu lassen, und geben Sie `Pollsdb1` als Kennwort ein.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

### <a name="create-a-poll-question-in-the-app"></a>Erstellen einer Frage für die Umfrage in der App

Sie können nun einen schnellen Test für die App ausführen, um zu überprüfen, ob sie mit der PostgreSQL-Datenbank arbeitet.

1. Kehren Sie im Browserfenster oder auf der Browserregisterkarte der Web-App zur Seite **Übersicht** zurück,und wählen Sie die **URL** für die Web-App (im Format `http://<app-name>.azurewebsites.net`) aus.

1. In der App sollte „No polls are available“ (Keine Umfragen verfügbar) angezeigt werden, da die Datenbank noch keine spezifischen Umfragen enthält.

1. Navigieren Sie zu `http://<app-name>.azurewebsites.net/admin` (Seite für die Django-Verwaltung), und melden sie sich mit den Superuser-Anmeldeinformationen aus dem vorherigen Abschnitt an (`root` und `Pollsdb1`).

1. Wählen Sie unter **Polls** (Umfragen) neben **Questions** (Fragen) die Option **Add** (Hinzufügen) aus, und erstellen Sie eine Frage für eine Umfrage mit mehreren Auswahlmöglichkeiten.

1. Navigieren Sie wieder zu `http://<app-name>.azurewebsites.net/`, und vergewissern Sie sich, dass die Fragen nun angezeigt werden. Beantworten Sie Fragen nach Belieben, um Daten in der Datenbank zu generieren.

**Glückwunsch!** Sie führen eine Python-Django-Web-App in Azure App Service für Linux mit einer aktiven PostgreSQL-Datenbank aus.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="update-the-app-and-redeploy"></a>Aktualisieren der App und erneutes Bereitstellen

Wie bereits weiter oben in diesem Tutorial beschrieben, stellt Azure Ihren App-Code erneut bereit, wenn Sie Änderungen im GitHub-Repository committen.

Wenn Sie jedoch die Datenmodelle der Django-App ändern, müssen Sie diese Änderungen zur Datenbank migrieren:

1. Stellen Sie über SSH erneut eine Verbindung mit der Web-App her, wie unter [Ausführen einer Django-Datenbankmigration](#run-django-database-migrations) beschrieben.

1. Navigieren Sie mit `cd site/wwwroot` zum App-Ordner.

1. Aktivieren Sie die virtuelle Umgebung mit `source /antenv/bin/activate`.

1. Führen Sie die Migration mit `python manage.py migrate` erneut aus.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="view-diagnostic-logs"></a>Anzeigen von Diagnoseprotokollen

Auf die generierten Konsolenprotokolle kann innerhalb des Containers zugegriffen werden, in dem die App in Azure gehostet wird.

Wählen Sie im Azure-Portal auf der Seite der Web-App die Option **Protokollstream** aus (auf der linken Seite unter **Überwachung** ). Die Protokolle werden als Konsolenausgabe angezeigt.

Sie können die Protokolldateien auch im Browser unter `https://<app-name>.scm.azurewebsites.net/api/logs/docker` untersuchen.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die App und die Datenbank für weitere Entwicklungen beliebig lange ausführen. Löschen Sie andernfalls die für dieses Tutorial erstellte Ressourcengruppe, um laufende Gebühren zu vermeiden. Dadurch werden auch alle darin enthaltenen Ressourcen gelöscht:

1. Geben Sie im Azure-Portal oben im Fenster in die Suchleiste „DjangoPostgres-Tutorial-rg“ ein, und wählen Sie dann den gleichen Namen unter **Ressourcengruppen** aus.

1. Wählen Sie auf der Ressourcengruppenseite die Option **Ressourcengruppe löschen** aus.

1. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und wählen Sie **Löschen** aus.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/DjangoPortalTutorialHelp)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie eine Python-App von App Service ausgeführt wird:

> [!div class="nextstepaction"]
> [Konfigurieren einer Python-App](/azure/app-service/configure-language-python)
