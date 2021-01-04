---
title: Verwenden von Spring Boot Starter für Azure Active Directory
description: Hier erfahren Sie, wie Sie eine Spring Boot Initializer-App mit Azure Active Directory Starter konfigurieren.
services: active-directory
documentationcenter: java
ms.date: 10/14/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 57afae188dab9ad09adf344fbc360d4cf85aa0f0
ms.sourcegitcommit: 0d2ea78f18430c845a32e0d2311427ab81033465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97754186"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Tutorial: Schützen einer Java-Web-App mithilfe von Spring Boot Starter für Azure Active Directory

In diesem Artikel erfahren Sie, wie Sie eine Java-App mit **[Spring Initializr]** erstellen. Spring Initializr verwendet Spring Boot Starter für Azure Active Directory (Azure AD).

In diesem Tutorial lernen Sie Folgendes:

 * Erstellen einer Java-Anwendung mit dem Spring Initializr
 * Konfigurieren von Azure Active Directory
 * Schützen der Anwendung mit Spring Boot-Klassen und -Anmerkungen
 * Erstellen und Testen der Java-Anwendung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den für die Entwicklung in Azure verfügbaren JDKs finden Sie unter <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher

## <a name="create-an-app-using-spring-initializr"></a>Erstellen einer App mithilfe von Spring Initializr

1. Navigieren Sie zu <https://start.spring.io/>.

1. Geben Sie an, dass Sie ein **Maven**-Projekt mit **Java** generieren möchten, und geben Sie die Namen für **Gruppe** und **Artefakt** für Ihre Anwendung ein.
1. Fügen Sie **Abhängigkeiten** für **Spring Web**, **Azure Active Directory** und **Spring Security** hinzu.
1. Wählen Sie unten auf der Seite die Schaltfläche **GENERIEREN** aus.
   
   >[!div class="mx-imgBorder"]
   >![Angeben von Gruppen- und Artefaktnamen, Auswählen von Abhängigkeiten][create-spring-app-01]


1. Laden Sie das Projekt nach entsprechender Aufforderung unter einem Pfad auf dem lokalen Computer herunter.

## <a name="create-azure-active-directory-instance"></a>Erstellen einer Azure Active Directory-Instanz

### <a name="create-the-active-directory-instance"></a>Erstellen der Active Directory-Instanz

1. Melden Sie sich bei <https://portal.azure.com> an.

1. Wählen Sie **Ressource erstellen**, **Identität** und dann **Azure Active Directory** aus.
   
   >[!div class="mx-imgBorder"]
   >![Erstellen einer neuen Azure Active Directory-Instanz][create-directory-01]

1. Geben Sie Werte für **Name der Organisation** und **Name der Anfangsdomäne** ein. Kopieren Sie die vollständige URL Ihres Verzeichnisses. Sie verwenden diese später in diesem Tutorial zum Hinzufügen von Benutzerkonten.
 (Beispiel: `azuresampledirectory.onmicrosoft.com`.) 

    Kopieren Sie die vollständige URL Ihres Verzeichnisses. Sie verwenden diese später in diesem Tutorial zum Hinzufügen von Benutzerkonten. (Beispiel: azuresampledirectory.onmicrosoft.com.)

    Wählen Sie **Erstellen** aus, wenn Sie fertig sind. Die Erstellung der neuen Ressource dauert einige Minuten.
   
   >[!div class="mx-imgBorder"]
   >![Angeben von Azure Active Directory-Namen][create-directory-02]

1. Wählen Sie nach Abschluss des Vorgangs den Zugriff auf das neue Verzeichnis aus.
   
   >[!div class="mx-imgBorder"]
   >![Auswählen Ihres Azure-Kontonamens][create-directory-03]

1. Kopieren Sie die **Mandanten-ID**. Sie verwenden diesen Wert, um später in diesem Tutorial die Datei *application.properties* zu konfigurieren.
   
   >[!div class="mx-imgBorder"]
   >![Kopieren Ihrer Mandanten-ID][create-directory-04]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Hinzufügen einer Anwendungsregistrierung für Ihre Spring Boot-App

1. Wählen Sie im Portalmenü **App-Registrierungen** und dann **Anwendung registrieren** aus.
   
   >[!div class="mx-imgBorder"]
   >![Hinzufügen einer neuen App-Registrierung][create-app-registration-01]

1. Geben Sie Ihre Anwendung an, und wählen Sie dann **Registrieren** aus.
   
   >[!div class="mx-imgBorder"]
   >![Erstellen einer neuen App-Registrierung][create-app-registration-02]

1. Kopieren Sie auf der Seite für Ihre App-Registrierung Ihre **Anwendungs-ID** und die **Mandanten-ID**. Sie verwenden diese Werte später in diesem Tutorial zum Konfigurieren Ihrer Datei *application.properties*.
   
   >[!div class="mx-imgBorder"]
   >![Kopieren von App-Registrierungsschlüsseln][create-app-registration-03]

1. Klicken Sie im linken Navigationsbereich auf **Zertifikate und Geheimnisse**.  Wählen Sie dann **Neuer geheimer Clientschlüssel** aus.
   
   >[!div class="mx-imgBorder"]
   >![Erstellen von App-Registrierungsschlüsseln][create-app-registration-03-5]

1. Fügen Sie eine **Beschreibung** hinzu, und wählen Sie in der Liste **Gültig bis** die Dauer aus.  Klicken Sie auf **Hinzufügen**. Der Wert für den Schlüssel wird automatisch eingefügt.
   
   >[!div class="mx-imgBorder"]
   >![Angeben von Parametern für App-Registrierungsschlüssel][create-app-registration-04]

1. Kopieren und speichern Sie den Wert des geheimen Clientschlüssels, damit Sie später in diesem Tutorial die Datei *application.properties* konfigurieren können. (Dieser Wert kann später nicht mehr abgerufen werden.)
   
   >[!div class="mx-imgBorder"]
   >![Kopieren des Werts für den App-Registrierungsschlüssel][create-app-registration-04-5]

1. Klicken Sie im linken Navigationsbereich auf **API-Berechtigungen**. 

1. Klicken Sie auf **Microsoft Graph**, und aktivieren Sie die Optionen **Als angemeldeter Benutzer auf das Verzeichnis zugreifen** und **Anmelden und Benutzerprofil lesen**. Klicken Sie auf **Berechtigungen erteilen...** und **Ja**, wenn Sie dazu aufgefordert werden.
   
   >[!div class="mx-imgBorder"]
   >![Hinzufügen von Zugriffsberechtigungen][create-app-registration-08]
   
1. Klicken Sie auf **Grant admin consent for Azure Sample** (Administratoreinwilligung für Azure-Beispiel erteilen), und wählen Sie **Ja** aus.
   
   >[!div class="mx-imgBorder"]
   >![Erteilen von Zugriffsberechtigungen][create-app-registration-05]

1. Wählen Sie auf der Hauptseite für Ihre App-Registrierung die Option **Authentifizierung** und dann **Plattform hinzufügen** aus.  Wählen Sie anschließend **Webanwendungen** aus.
   
   >[!div class="mx-imgBorder"]
   >![Bearbeiten der Antwort-URLs][create-app-registration-09]

1. Geben Sie *http://localhost:8080/login/oauth2/code/azure* als neuen **Umleitungs-URI** ein, und wählen Sie dann **Konfigurieren** aus.
   
   >[!div class="mx-imgBorder"]
   >![Hinzufügen einer neuen Antwort-URL][create-app-registration-10]

1. Wählen Sie auf der Hauptseite für Ihre App-Registrierung die Option **Manifest** aus, legen Sie den Wert der Parameter `oauth2AllowIdTokenImplicitFlow` und `oauth2AllowImplicitFlow` auf `true` fest, und wählen Sie anschließend **Speichern** aus.
   
   >[!div class="mx-imgBorder"]
   >![App-Manifest konfigurieren][create-app-registration-11]

   > [!NOTE]
   > Weitere Informationen zu den Parametern `oauth2AllowIdTokenImplicitFlow` und `oauth2AllowImplicitFlow` sowie zu anderen Anwendungseinstellungen finden Sie unter [Azure Active Directory-Anwendungsmanifest][AAD app manifest].

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Hinzufügen eines Benutzerkontos zu Ihrem Verzeichnis und Hinzufügen dieses Kontos zu einer Gruppe

1. Wählen Sie auf der Seite **Übersicht** Ihrer Active Directory-Instanz die Option **Benutzer** und dann **Neuer Benutzer** aus.
   
   >[!div class="mx-imgBorder"]
   >![Hinzufügen eines neuen Benutzerkontos][create-user-01]

1. Geben Sie im angezeigten Bereich **Benutzer** den **Benutzernamen** und den **Namen** ein.  Klicken Sie anschließend auf **Erstellen**.
   
   >[!div class="mx-imgBorder"]
   >![Eingeben der Benutzerkontoinformationen][create-user-02]

   > [!NOTE]
   > Sie müssen die Verzeichnis-URL von weiter oben in diesem Tutorial eingeben, wenn Sie den Benutzernamen eingeben. Beispiel:
   >
   > `test-user@azuresampledirectory.onmicrosoft.com`

1. Wählen Sie auf der Seite **Übersicht** Ihrer Active Directory-Instanz die Option **Gruppen** und dann **Neue Gruppe** aus. Geben Sie die Gruppe an, die Sie in Ihrer Anwendung für die Autorisierung verwenden möchten.

1. Wählen Sie die Option **Keine Mitglieder ausgewählt** aus. (Für dieses Tutorial erstellen wir eine Gruppe mit dem Namen *users*.)  Suchen Sie nach dem Benutzer, der im vorherigen Schritt erstellt wurde.  Wählen Sie die Option **Auswählen** aus, um den Benutzer der Gruppe hinzuzufügen.  Wählen Sie anschließend **Erstellen** aus, um die neue Gruppe zu erstellen.

   >[!div class="mx-imgBorder"]
   >![Auswählen des Benutzers für die Gruppe][create-user-03]

1. Navigieren Sie zurück zum Bereich **Benutzer**, wählen Sie Ihren Testbenutzer und die Option **Kennwort zurücksetzen** aus, und kopieren Sie das Kennwort. Sie benötigen es später in diesem Tutorial, wenn Sie sich an Ihrer Anwendung anmelden.

   >[!div class="mx-imgBorder"]
   >![Anzeigen des Kennworts][create-user-04]

## <a name="configure-and-compile-your-app"></a>Konfigurieren und Kompilieren Ihrer App

1. Extrahieren Sie die Dateien aus dem Projektarchiv, das Sie zuvor in diesem Tutorial erstellt und heruntergeladen haben, in ein Verzeichnis.

1. Navigieren Sie zum übergeordneten Ordner für Ihr Projekt, und öffnen Sie die Maven-Projektdatei `pom.xml` in einem Text-Editor.

1. Fügen Sie die Abhängigkeiten für Spring OAuth2-Sicherheit zu `pom.xml` hinzu:

   ```xml
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-jose</artifactId>
   </dependency>
   ```

1. Speichern und schließen Sie die Datei *pom.xml*.

1. Navigieren Sie in Ihrem Projekt zum Ordner *src/main/resources*, und öffnen Sie die Datei *application.properties* in einem Text-Editor.

1. Geben Sie die Einstellungen für Ihre App-Registrierung mithilfe der zuvor erstellten Werte an. Beispiel:

   ```yaml
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

   # Specifies your App Registration's Application ID:
   spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

   # Specifies your App Registration's secret key:
   spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.user-group.allowed-groups=Users
   ```

   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `azure.activedirectory.tenant-id` | Enthält die **Verzeichnis-ID** Ihrer Active Directory-Instanz von vorhin. |
   | `spring.security.oauth2.client.registration.azure.client-id` | Enthält die **Anwendungs-ID** aus der zuvor durchgeführten App-Registrierung. |
   | `spring.security.oauth2.client.registration.azure.client-secret` | Enthält den **Wert** aus dem zuvor erstellten App-Registrierungsschlüssel. |
   | `azure.activedirectory.user-group.allowed-groups` | Enthält eine Liste mit Active Directory-Gruppen für die Autorisierung. |

   > [!NOTE]
   > Eine vollständige Liste mit verfügbaren Werten in der Datei *application.properties* finden Sie im [Spring Boot-Beispiel für Azure Active Directory][AAD Spring Boot Sample] auf GitHub.

1. Speichern und schließen Sie die Datei *application.properties*.

1. Erstellen Sie im Java-Quellordner für Ihre Anwendung einen Ordner namens *controller*. Beispiel: *src/main/java/com/wingtiptoys/security/controller*.

1. Erstellen Sie im Ordner *controller* eine neue Java-Datei namens *HelloController.java*, und öffnen Sie sie in einem Text-Editor.

1. Geben Sie den folgenden Code ein, speichern Sie die Datei, und schließen Sie sie:

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.access.prepost.PreAuthorize;
   import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
   import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
   import org.springframework.ui.Model;

   @RestController
   public class HelloController {
      @Autowired
      @PreAuthorize("hasRole('Users')")
      @RequestMapping("/")
      public String helloWorld() {
         return "Hello World!";
      }
   }
   ```

   > [!NOTE]
   > Der Gruppenname, den Sie für die Methode `@PreAuthorize("hasRole('')")` angeben, muss eine der Gruppen enthalten, die Sie im Feld `azure.activedirectory.user-group.allowed-groups` der Datei *application.properties* angegeben haben.
   >
   > Sie können auch verschiedene Autorisierungseinstellungen für verschiedene Anforderungszuordnungen angeben. Beispiel:
   >
   > ``` java
   > public class HelloController {
   >    @Autowired
   >    @PreAuthorize("hasRole('Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('Group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('Group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```

1. Erstellen Sie im Java-Quellordner für Ihre Anwendung einen Ordner namens *security*. Beispiel: *src/main/java/com/wingtiptoys/security/security*.

1. Erstellen Sie im Ordner *security* eine neue Java-Datei namens *WebSecurityConfig.java*, und öffnen Sie sie in einem Text-Editor.

1. Geben Sie den folgenden Code ein, speichern Sie die Datei, und schließen Sie sie:

    ```java
    package com.wingtiptoys.security;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
    import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
    import org.springframework.security.oauth2.core.oidc.user.OidcUser;

    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Autowired
        private OAuth2UserService<OidcUserRequest, OidcUser> oidcUserService;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
        }
    }
    ```

## <a name="build-and-test-your-app"></a>Erstellen und Testen der App

1. Öffnen Sie eine Eingabeaufforderung, und wechseln Sie zu dem Ordner, in dem sich die Datei *pom.xml* Ihrer App befindet.

1. Erstellen Sie mit Maven die Spring Boot-Anwendung, und führen Sie sie aus. Beispiel:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```
   
   >[!div class="mx-imgBorder"]
   >![Erstellen Ihrer Anwendung][build-application]

1. Nachdem Ihre Anwendung durch Maven erstellt und gestartet wurde, öffnen Sie „http:<span></span>//localhost:8080“ in einem Webbrowser. Daraufhin sollten Sie zur Eingabe eines Benutzernamens und eines Kennworts aufgefordert werden.
   
   >[!div class="mx-imgBorder"]
   ![Anmelden bei Ihrer Anwendung][application-login]

   > [!NOTE]
   > Wenn dies die erste Anmeldung für ein neues Benutzerkonto ist, werden Sie möglicherweise zum Ändern Ihres Kennworts aufgefordert.

   >[!div class="mx-imgBorder"]
   >![Ändern des Kennworts][update-password]

1. Nach erfolgreicher Anmeldung sollte der Beispieltext „Hello World“ des Controllers angezeigt werden.

   >[!div class="mx-imgBorder"]
   >![Erfolgreiche Anmeldung][hello-world]

   > [!NOTE]
   > Für nicht autorisierte Benutzerkonten wird eine Meldung vom Typ **HTTP 403 (nicht autorisiert)** zurückgegeben.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial haben Sie mit Azure Active Directory Starter eine neue Java-Webanwendung erstellt, einen neuen Azure AD-Mandanten konfiguriert und eine neue Anwendung darin registriert. Anschließend haben Sie Ihre Anwendung zur Verwendung der Spring-Anmerkungen und -Klassen zum Schützen der Web-App konfiguriert.

## <a name="see-also"></a>Weitere Informationen
* Informationen zu neuen UI-Optionen finden Sie unter [Neues Trainingshandbuch zu App-Registrierungen im Azure-Portal](/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

   >[!div class="nextstepaction"]
   >[Spring Framework in Azure](./index.yml)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: ../index.yml
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png

[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03-5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04-5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png
[create-app-registration-11]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-11.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-world]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-world.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png
