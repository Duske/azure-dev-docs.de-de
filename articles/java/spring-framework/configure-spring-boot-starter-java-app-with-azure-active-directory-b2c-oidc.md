---
title: Verwenden von Spring Boot Starter für Azure Active Directory B2C
description: Hier erfahren Sie, wie Sie eine Spring Boot Initializr-App mit Azure Active Directory B2C Starter konfigurieren.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: edburns
ms.date: 10/23/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: aa2d60f969895acbcb56f74e909993ceb9b0d7d5
ms.sourcegitcommit: 5c7f5fef798413b1a304cc9ee31c8518b73f27eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066301"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>Tutorial: Schützen einer Java-Web-App mithilfe von Spring Boot Starter für Azure Active Directory B2C

In diesem Artikel erfahren Sie, wie Sie eine Java-App mit [Spring Initializr](https://start.spring.io/) erstellen. Spring Initializr verwendet Spring Boot Starter für Azure Active Directory (Azure AD).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Java-Anwendung mit dem Spring Initializr
> * Konfigurieren von Azure Active Directory B2C
> * Schützen der Anwendung mit Spring Boot-Klassen und -Anmerkungen
> * Erstellen und Testen der Java-Anwendung

[Azure Active Directory](https://azure.microsoft.com/services/active-directory) ist die cloudbasierte Unternehmensidentitätslösung von Microsoft. [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) ergänzt den Funktionsumfang von Azure Active Directory und ermöglicht es Ihnen, den Zugriff von Kunden, Consumern und Bürgern auf Ihre B2C-Anwendungen (Business-to-Consumer) festzulegen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie noch nicht über ein Abonnement verfügen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

## <a name="create-an-app-using-spring-initializr"></a>Erstellen einer App mithilfe von Spring Initializr

1. Navigieren Sie zu <https://start.spring.io/>.

2. Geben Sie die Werte gemäß dieser Anleitung ein. Beachten Sie, dass die Bezeichnungen und das Layout von der hier gezeigten Abbildung abweichen können.

    * Wählen Sie unter **Projekt** die Option **Maven-Projekt** aus.
    * Wählen Sie unter **Sprache** die Option **Java** aus.
    * Wählen Sie unter **Spring Boot** die Option **2.3.4** aus.
    * Geben Sie unter **Gruppe**, **Artefakt** und **Name** denselben Wert mit einer kurzen beschreibenden Zeichenfolge ein. Die Benutzeroberfläche kann einige dieser Werte während der Eingabe automatisch ausfüllen.
    * Wählen Sie im Bereich **Abhängigkeiten** die Option **Abhängigkeiten hinzufügen** aus. Verwenden Sie die Benutzeroberfläche, um Abhängigkeiten für **Spring Web** und **Spring Security** hinzuzufügen.

   ![Eingeben der Werte, um das Projekt zu generieren](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/fill-in-the-values-to-generate-the-project.png)

    > [!NOTE]
    > Spring Initializr nutzt als Standardversion Java 11. Um die in diesem Thema beschriebenen Spring Boot Starter verwenden zu können, müssen Sie stattdessen Java 8 auswählen.


3. Klicken Sie auf **Projekt generieren**, und laden Sie das Projekt dann in einen Pfad auf dem lokalen Computer herunter. Verschieben Sie die heruntergeladene Datei in ein Verzeichnis, das nach dem Projekt benannt ist, und entpacken Sie die Datei. Das Dateilayout sollte in etwa wie folgt aussehen, wobei der Wert, den Sie für **Gruppe** eingegeben haben, `yourProject` ersetzt.

    ```
    .
    ├── HELP.md
    ├── mvnw
    ├── mvnw.cmd
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── yourProject
        │   │       └── yourProject
        │   │           └── YourProjectApplication.java
        │   └── resources
        │       ├── application.properties
        │       ├── static
        │       └── templates
        └── test
            └── java
                └── yourProject
                    └── yourProject
                        └── YourProjectApplicationTests.java
    ```

## <a name="create-and-initialize-an-azure-active-directory-instance"></a>Erstellen und Initialisieren einer Azure Active Directory-Instanz

### <a name="create-the-active-directory-instance"></a>Erstellen der Active Directory-Instanz

1. Melden Sie sich bei <https://portal.azure.com> an.

2. Wählen Sie **Ressource erstellen** und **Identität** und dann **Alle anzeigen** aus. Suchen Sie nach **Azure Active Directory B2C**.

    ![Erstellen einer neuen Azure Active Directory B2C-Instanz](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. Klicken Sie auf **Erstellen**.

    ![Abrufen des Namens Ihres B2C-Mandanten](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. Wählen Sie **Neuen Azure AD B2C-Mandanten erstellen**.

    ![Erstellen einer neuen Azure Active Directory-Instanz](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. Geben Sie für **Organisationsname** und **Name der Anfangsdomäne** die entsprechenden Werte an, und wählen Sie dann **Erstellen** aus.

    ![Auswählen Ihrer Azure Active Directory-Instanz](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. Wenn die Active Directory-Erstellung abgeschlossen ist, navigieren Sie zum neuen Verzeichnis. Suchen Sie alternativ nach `b2c`, und wählen Sie **Azure AD B2C** aus.

    ![Suchen der Azure Active Directory B2C-Instanz](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Hinzufügen einer Anwendungsregistrierung für Ihre Spring Boot-App

1. Wählen Sie auf der linken Seite unter **Verwalten** die Option **App-Registrierungen** und dann **Neue Registrierung** aus.

   ![Screenshot: Fenster mit Azure AD B2C-App-Registrierungen im Azure-Portal](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. Geben Sie im Feld **Name** den oben ermittelten Wert für **Gruppe** ein. Legen Sie anschließend den **Umleitungs-URI (empfohlen)** auf *http://localhost:8080/home* fest, und wählen Sie die Option **Registrieren** aus.

   ![Konfigurieren einer neuen App-Registrierung](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c4-n.png)

3. Wählen Sie im Bereich **Verwalten** die Option **Anwendungen (Legacy)** und dann den von Ihnen erstellten Anwendungsnamen aus.

   ![Aktualisieren der Anwendung](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c5-n.png)

4. Wählen Sie **Eigenschaften** aus, und legen Sie **Impliziten Ablauf zulassen** auf **Ja** fest.
   
5. Behalten Sie für die anderen Felder die Standardwerte bei.
    
6. Wählen Sie **Speichern** aus. Es kann einen Moment dauern, bis die Anwendung bereit ist.
    
   ![Aktualisieren des App-Schlüssels](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c6-n.png)

7. Wählen Sie im Bereich **Allgemein** die Option **Schlüssel** und dann **Schlüssel generieren** aus.

8. Legen Sie **App-Schlüssel** auf den Wert fest, den Sie oben für **Gruppe** eingegeben haben.

9. Wählen Sie **Speichern** aus. Warten Sie, bis der Schlüssel im Abschnitt „App-Schlüssel“ angezeigt wird, und kopieren Sie ihn dann zur späteren Verwendung in diesem Artikel.

    > [!NOTE]
    > Wenn Sie den Abschnitt **Schlüssel** verlassen und anschließend zurückkehren, wird der Schlüsselwert nicht angezeigt. In diesem Fall müssen Sie einen weiteren Schlüssel erstellen und ihn zur späteren Verwendung kopieren.
    > Der generierte Schlüssel kann gelegentlich Zeichen enthalten, die für die Einbindung in die Datei *application.yml* problematisch sind, z. B. umgekehrte Schrägstriche oder Backticks. Verwerfen Sie in diesem Fall den betreffenden Schlüssel, und generieren Sie einen neuen Schlüssel.

    ![Erstellen des Geheimnisses](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

10. Wählen Sie **Übersicht**.

11. Wählen Sie im linken Bereich im Abschnitt **Richtlinien** die Option **Benutzerflow** aus, und wählen Sie dann **Neuer Benutzerflow** aus.

12. Sie verlassen jetzt dieses Tutorial, führen ein weiteres Tutorial aus und kehren zu diesem Tutorial zurück, wenn Sie damit fertig sind. Im Folgenden finden Sie einige Punkte, die Sie beachten sollten, wenn Sie das andere Tutorial durcharbeiten.

    * Beginnen Sie mit dem Schritt, in dem Sie aufgefordert werden, **Neuer Benutzerflow** auszuwählen.
    * Wenn sich dieses Tutorial auf `webapp1`bezieht, verwenden Sie stattdessen den Wert, den Sie für **Gruppe** eingegeben haben.
    * Wenn Sie Ansprüche auswählen, die von den Flows zurückgegeben werden sollen, stellen Sie sicher, dass **Anzeigename** ausgewählt ist. Ohne diesen Anspruch funktioniert die App nicht, die in diesem Tutorial erstellt wird.
    * Wenn Sie aufgefordert werden, die Benutzerflows auszuführen, ist die Umleitungs-URL, die Sie oben angegeben haben, noch nicht aktiv. Sie können die Flows trotzdem ausführen, aber die Umleitung wird nicht erfolgreich abgeschlossen. Dies entspricht dem erwarteten Verhalten.
    * Wenn Sie „Nächste Schritte“ erreichen, kehren Sie zu diesem Tutorial zurück.

    Führen Sie die Schritte unter [Tutorial: Erstellen von Benutzerflows in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-user-flows) aus, um Benutzerflows für „Registrierung und Anmeldung“, „Profilbearbeitung“ und „Kennwortzurücksetzung“ zu erstellen.

    Von Azure AD B2C werden sowohl lokale Konten als auch soziale Netzwerke als Identitätsanbieter unterstützt. Ein Beispiel für die Erstellung eines GitHub-Identitätsanbieters finden Sie unter [Einrichten der Registrierung und Anmeldung mit einem GitHub-Konto mithilfe von Azure Active Directory B2C](/azure/active-directory-b2c/identity-provider-github).

## <a name="configure-and-compile-your-app"></a>Konfigurieren und Kompilieren Ihrer App

Nachdem Sie nun die Azure AD B2C-Instanz und einige Benutzerflows erstellt haben, verbinden Sie Ihre Spring-App mit der Azure AD B2C-Instanz.

1. Wechseln Sie in der Befehlszeile mit „cd“ zu dem Verzeichnis, in das Sie die ZIP-Datei entpackt haben, die Sie von Spring Initializr heruntergeladen haben.

2. Navigieren Sie zum übergeordneten Ordner für Ihr Projekt, und öffnen Sie die Maven-Projektdatei *pom.xml* in einem Text-Editor.

3. Fügen Sie der Datei *pom.xml* die Abhängigkeiten für Spring OAuth2-Sicherheit hinzu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        <version>See Below</version>
    </dependency>
    ```

    Verwenden Sie für die Angabe `azure-active-directory-b2c-spring-boot-starter` die neueste verfügbare Version. Möglicherweise können Sie [mvnrepository.com](https://mvnrepository.com/ artifact/com.microsoft.azure/azure-active-directory-spring-boot-starter) verwenden, um diese Angabe nachzuschlagen. Ab diesem Update ist die neueste Version `2.3.5`.

    Verwenden Sie für `spring-boot-starter-thymeleaf` die Version, die der oben ausgewählten Version von Spring Boot entspricht, z. B. `2.3.4.RELEASE`.

    Verwenden Sie für `thymeleaf-extras-springsecurity5` die neueste verfügbare Version. Möglicherweise können Sie [mvnrepository.com](https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity5) verwenden, um diese Angabe nachzuschlagen. Zum Zeitpunkt der Erstellung dieses Artikels ist die neueste Version `3.0.4.RELEASE`.

4. Speichern und schließen Sie die Datei *pom.xml*.

    * Überprüfen Sie, ob ihre Abhängigkeiten richtig sind, indem Sie `mvn -DskipTests clean install` ausführen. Wenn `BUILD SUCCESS` nicht angezeigt wird, beheben und lösen Sie das Problem, bevor Sie fortfahren.

5. Navigieren Sie in Ihrem Projekt zum Ordner *src/main/resources*, und erstellen Sie eine Datei *application.yml* in einem Text-Editor.

6. Geben Sie die Einstellungen für Ihre App-Registrierung mithilfe der zuvor erstellten Werte an. Beispiel:

    ```yaml
    azure:
      activedirectory:
        b2c:
          tenant: ejb0518domain
          client-id: 11111111-1111-1111-1111-1111111111111111
          client-secret: '<yourAppKey>'
          reply-url: http://localhost:8080/home
          logout-success-url: http://localhost:8080/home
          user-flows:
            sign-up-or-sign-in: B2C_1_signupsignin1
            profile-edit: B2C_1_profileediting1
            password-reset: B2C_1_passwordreset1
    ```

    Beachten Sie, dass der Wert `client-secret` in einfache Anführungszeichen eingeschlossen wird. Dies ist erforderlich, da der Wert von `<yourAppKey>` mit ziemlicher Sicherheit einige Zeichen enthält, die in einfache Anführungszeichen eingeschlossen werden müssen, wenn sie in YAML vorhanden sind.

    > [!NOTE]
    > Zum Zeitpunkt der Erstellung dieses Artikels lautet die vollständige Liste der Active Directory B2C Spring Integration-Werte, die für die Verwendung in *application.yml* verfügbar sind, wie folgt:
    >
    > ```
    > azure:
    >   activedirectory:
    >     b2c:
    >       tenant:
    >       oidc-enabled:
    >       client-id:
    >       client-secret:
    >       reply-url:  # should be absolute url.
    >       logout-success-url:
    >       user-flows:
    >         sign-up-or-sign-in:
    >         profile-edit: # optional
    >         password-reset: # optional
    > ```
    >
    > Die Datei *application.yml* ist im [Azure Active Directory B2C Spring Boot-Beispiel](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/application.yml) auf GitHub verfügbar.

7. Speichern und schließen Sie die Datei *application.yml*.

8. Erstellen Sie einen Ordner mit dem Namen *controller* in *src/main/java/<yourGroupId>/<yourGroupId>* , und ersetzen Sie `<yourGroupId>` durch den Wert, den Sie für **Gruppe** eingegeben haben.

9. Erstellen Sie im Ordner *controller* eine neue Java-Datei namens *WebController.java*, und öffnen Sie sie in einem Text-Editor.

10. Geben Sie den folgenden Code ein, ändern Sie `yourGroupId` entsprechend, und speichern und schließen Sie die Datei:

    ```java
    package yourGroupId.yourGroupId.controller;

    import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;

    @Controller
    public class WebController {

        private void initializeModel(Model model, OAuth2AuthenticationToken token) {
            if (token != null) {
                final OAuth2User user = token.getPrincipal();

                model.addAttribute("grant_type", user.getAuthorities());
                model.addAllAttributes(user.getAttributes());
            }
        }

        @GetMapping(value = "/")
        public String index(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }

        @GetMapping(value = "/greeting")
        public String greeting(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "greeting";
        }

        @GetMapping(value = "/home")
        public String home(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }
    }
    ```

    Da jede Methode im Controller `initializeModel()` aufruft und diese Methode `model.addAllAttributes(user.getAttributes());` aufruft, kann jede HTML-Seite in *src/main/resources/templates* auf jedes dieser Attribute zugreifen, z. B. auf `${name}`, `${grant_type}` oder `${auth_time}`. Die von `user.getAttributes()` zurückgegebenen Werte sind tatsächlich die Ansprüche von `id_token` für die Authentifizierung. Die vollständige Liste der verfügbaren Ansprüche finden Sie unter [Microsoft Identity Platform: ID-Token](/azure/active-directory/develop/id-tokens#payload-claims).

11. Erstellen Sie einen Ordner mit dem Namen *security* in *src/main/java/<yourGroupId>/<yourGroupId>* , und ersetzen Sie `yourGroupId` durch den Wert, den Sie für **Gruppe** eingegeben haben.

12. Erstellen Sie im Ordner *security* eine neue Java-Datei namens *WebSecurityConfiguration.java*, und öffnen Sie sie in einem Text-Editor.

13. Geben Sie den folgenden Code ein, ändern Sie `yourGroupId` entsprechend, und speichern und schließen Sie die Datei:

    ```java
    package yourGroupId.yourGroupId.security;

    import com.microsoft.azure.spring.autoconfigure.b2c.AADB2COidcLoginConfigurer;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

    @EnableWebSecurity
    public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

        private final AADB2COidcLoginConfigurer configurer;

        public WebSecurityConfiguration(AADB2COidcLoginConfigurer configurer) {
            this.configurer = configurer;
        }

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .apply(configurer)
            ;
        }
    }
    ```

14. Kopieren Sie die Dateien *greeting.html* und *home.html* aus dem [Azure AD B2C Spring Boot-Beispiel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/templates) in *src/main/resources/templates*, und ersetzen Sie `${your-profile-edit-user-flow}` und `${your-password-reset-user-flow}` durch die Namen der zuvor erstellten Benutzerflows.

## <a name="build-and-test-your-app"></a>Erstellen und Testen der App

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie zu dem Ordner, in dem sich die Datei *pom.xml* Ihrer App befindet.

2. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

    > [!NOTE]
    > Es ist außerordentlich wichtig, dass die Zeit gemäß der Systemuhr, unter der die lokale Spring Boot-App ausgeführt wird, genau ist. Bei der Verwendung von OAuth 2.0 gibt es nur eine sehr geringe Toleranz bei Uhrabweichungen. Schon drei Minuten Ungenauigkeit können dazu führen, dass die Anmeldung mit einem Fehler ähnlich `[invalid_id_token] An error occurred while attempting to decode the Jwt: Jwt used before 2020-05-19T18:52:10Z` fehlschlägt. Zum Zeitpunkt der Erstellung dieses Artikels stellt [time.gov](https://time.gov/) einen Indikator dafür bereit, wie weit Ihre Uhr von der tatsächlichen Zeit abweicht. Die App wurde erfolgreich mit einer Abweichung von + 0,019 Sekunden ausgeführt.

    ```shell
    mvn -DskipTests clean package
    mvn -DskipTests spring-boot:run
    ```

3. Nachdem Ihre Anwendung durch Maven erstellt und gestartet wurde, öffnen Sie `http://localhost:8080/` in einem Webbrowser. Daraufhin sollten Sie zur Anmeldeseite weitergeleitet werden.

    ![Anmeldeseite](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. Wählen Sie den Link mit dem Text aus, der sich auf die Anmeldung bezieht. Daraufhin sollten Sie zu Azure AD B2C weitergeleitet werden, um den Authentifizierungsprozess zu starten.

5. Nach erfolgreicher Anmeldung sollte das Beispiel `home page` im Browser angezeigt werden.

    ![Erfolgreiche Anmeldung](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie mit Azure Active Directory B2C Starter eine neue Java-Webanwendung erstellt, einen neuen Azure AD B2C-Mandanten konfiguriert und eine neue Anwendung darin registriert. Anschließend haben Sie Ihre Anwendung zur Verwendung der Spring-Anmerkungen und -Klassen zum Schützen der Web-App konfiguriert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie das [Azure-Portal](https://portal.azure.com/), um die in diesem Artikel erstellten Ressourcen zu löschen, wenn Sie sie nicht mehr benötigen, um unerwartete Gebühren zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](./index.yml)