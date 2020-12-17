---
title: 'Tutorial: Hinzufügen einer Microsoft-Anmeldeschaltfläche zur React-SPA'
description: Bei der in diesem Tutorial beschriebenen Azure Active Directory-Authentifizierung geht es um eine Schaltfläche zum An- und Abmelden und um den Zugriff auf den Benutzernamen (E-Mail-Adresse) eines Benutzers. Entwickeln Sie die Anwendung mit einem clientseitigen Azure-SDK (`@azure/msal-browser`), um die Interaktion des Benutzers in der Single-Page-Webanwendung (SPA) zu verwalten.
ms.topic: tutorial
ms.date: 12/01/2020
ms.custom: devx-track-js, "azure-sdk-javascript-@azure/msal-browser-2.7.0"
ms.openlocfilehash: a5c07696c6c774408bf2772542234e59f5c0b58c
ms.sourcegitcommit: 09b4a2dbe13601fdf16fcc4082a5075b46ad3459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563725"
---
# <a name="add-microsoft-login-button-to-a-single-page-application-for-authentication"></a>Hinzufügen einer Microsoft-Anmeldeschaltfläche zu einer Single-Page-Webanwendung für die Authentifizierung

Bei der in diesem Tutorial beschriebenen Azure-Authentifizierung geht es um eine Anmelde- und Abmeldeschaltfläche und um den Zugriff auf das Konto eines Benutzers. Entwickeln Sie die Anwendung mit einem clientseitigen Azure-SDK (`@azure/msal-browser`), um die Interaktion des Benutzers in der Single-Page-Webanwendung (SPA) zu verwalten.

* [Quellcode](https://github.com/Azure-Samples/js-e2e-client-azure-login-button)

## <a name="application-architecture-and-functionality"></a>Anwendungsarchitektur und Funktionalität

Die in diesem Tutorial erstellte SPA ist eine React-App (create-react-app) mit den folgenden Vorgängen:

- Anmelden mit einem von Microsoft unterstützten Anmeldenamen, z. B. „Office 365“ oder „Outlook.com“
- Abmelden von der Anwendung

Für die Bereitstellung einer schnellen und einfachen Single-Page-Webanwendung wird im Beispiel **create-react-app** mit TypeScript genutzt. Dieses Front-End-Framework verfügt über mehrere hilfreiche Elemente, die typisch für die Cliententwicklung mit Azure SDKs sind:

- Bündelung (erforderlich für in einer Clientanwendung verwendete Azure SDKs)
- Umgebungsvariablen in der `.env`-Datei

## <a name="1-set-up-development-environment"></a>1. Einrichten der Entwicklungsumgebung

Vergewissern Sie sich, dass auf dem lokalen Computer Folgendes installiert ist:

- Ein Azure-Benutzerkonto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
- [Node.js und npm](https://nodejs.org/en/download) (installiert auf Ihrem lokalen Computer)
- [Visual Studio Code](https://code.visualstudio.com/) oder vergleichbare IDE mit Bash-Shell oder Terminal (installiert auf Ihrem lokalen Computer) 

## <a name="2-keep-value-for-environment-variable"></a>2. Beibehalten des Werts für die Umgebungsvariable

Legen Sie einen Speicherort fest, an den Sie den Wert der App-Client-ID kopieren können. 

## <a name="3-create-app-registration-for-authentication"></a>3. Erstellen der App-Registrierung für die Authentifizierung

1. Führen Sie die **Anmeldung** beim [Azure-Portal](https://portal.azure.com/?quickstart=True#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) durch, um die App-Registrierungen des Standardverzeichnisses vorzunehmen.
1. Wählen Sie **+ Neue Registrierung** aus.
1. **Geben Sie Ihre App-Registrierungsdaten ein**, indem Sie die folgende Tabelle verwenden:

   | Feld                   | Wert                                                                                                                                                                      |BESCHREIBUNG|
   | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |--|
   | Name                    | `Simple Auth Tutorial`|Dies ist der App-Name, der Benutzern beim Anmelden bei Ihrer App im Berechtigungsformular angezeigt wird.                                                 |
   | Unterstützte Kontotypen | **Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig) und persönliche Microsoft-Konten**|Hierdurch sind die meisten Kontotypen abgedeckt. |
   | Typ des Umleitungs-URI           | **Single-Page-Anwendung (SPA)**                                                                                        | |
   | Wert des Umleitungs-URI           | `http://localhost:3000` | Die URL, zu der nach der erfolgreichen oder fehlgeschlagenen Authentifizierung zurückgekehrt werden soll.                                                                                        |

    :::image type="content" source="../media/tutorial-login-button/azure-active-directory-create-new-app-registration.png" alt-text="Neue App-Registrierung für Azure":::  

1. Wählen Sie **Registrieren**. Warten Sie, bis der Prozess der App-Registrierung abgeschlossen ist.
1. **Kopieren Sie die Anwendungs-ID (Client)** aus dem Abschnitt „Übersicht“ der App-Registrierung. Sie fügen diesen Wert später Ihrer Umgebungsvariable für die Client-App hinzu.

## <a name="4-create-react-single-page-application-for-typescript"></a>4. Erstellen einer React-Single-Page-Webanwendung mit TypeScript

1. Führen Sie in einer Bash-Shell **eine create-react-app-Erstellung** mit TypeScript als Sprache durch:

   ```bash
   npx create-react-app tutorial-demo-login-button --template typescript
   ```

1. Wechseln Sie in das neue Verzeichnis, und installieren Sie das Paket `@azure/msal-browser`:

   ```bash
   cd tutorial-demo-login-button && npm install @azure/msal-browser
   ```

1. Erstellen Sie auf Stammebene eine `.env`-Datei, und fügen Sie ihr die folgenden Zeilen hinzu:

    :::code language="env" source="~/../js-e2e-client-azure-login-button/.env"  :::

    Die `.env`-Datei wird vom „create-react-app“-Framework gelesen.

1. Kopieren Sie Ihre Anwendungs-ID (Client) in den zweiten Wert.

## <a name="5-add-login-and-logoff-buttons"></a>5. Hinzufügen von Anmelde- und Abmeldeschaltflächen

1. Erstellen Sie im Ordner `./src` einen Unterordner mit dem Namen `azure` für die Azure-spezifischen Dateien. 

1. Erstellen Sie im Ordner `azure` eine neue Datei für die Authentifizierungskonfiguration mit dem Namen `azure-authentication-config.ts`, und kopieren Sie den folgenden Code in die Datei:

    :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-config.ts"  highlight="3-4,, 11-12":::

    Mit dieser Datei wird Ihre Anwendungs-ID aus der `.env`-Datei eingelesen, „Sitzung“ als Browserspeicher festgelegt (anstelle von Cookies) und eine Protokollierung durchgeführt, bei der auf personenbezogene Informationen geachtet wird.

1. Erstellen Sie im Ordner `azure` eine neue Datei für die Azure-Authentifizierungsmiddleware mit dem Namen `azure-authentication-context.ts`, und kopieren Sie den folgenden Code in die Datei:

    :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-context.ts"  highlight="43, 58, 65":::

    Diese Datei ermöglicht für einen Benutzer die Authentifizierung bei Azure mit den Funktionen `login` und `logout`.

1. Erstellen Sie im Ordner `azure` eine neue Datei für die Schaltflächenkomponente der Benutzeroberfläche mit dem Namen `azure-authentication-component.tsx`, und kopieren Sie den folgenden Code in die Datei:

   :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/azure/azure-authentication-component.tsx"  highlight="3, 11, 23, 29, 36":::

   Über diese Schaltflächenkomponente wird ein Benutzer angemeldet, und das Benutzerkonto wird an die aufrufende bzw. übergeordnete Komponente zurückgegeben.

   Der Text und die Funktionalität der Schaltfläche werden basierend darauf umgeschaltet, ob der Benutzer gerade angemeldet ist. Dies wird mit der Funktion `onAuthenticated` als Eigenschaft erfasst, die an die Komponente übergeben wird.

   Wenn sich ein Benutzer anmeldet, wird mit der Schaltfläche die Methode `authenticationModule.login` der Azure-Authentifizierungsbibliothek mit `returnedAccountInfo` als Rückruffunktion aufgerufen. Das zurückgegebene Benutzerkonto wird mit der Funktion `onAuthenticated` dann an die übergeordnete Komponente zurückgegeben.


1. Öffnen Sie die Datei `./src/App.tsx`, und ersetzen Sie den gesamten Code durch den folgenden Code, um die Schaltflächenkomponente für die An- und Abmeldung einzufügen:

   :::code language="typescript" source="~/../js-e2e-client-azure-login-button/src/App.tsx"  highlight="10, 37-42":::

    Nachdem sich ein Benutzer angemeldet hat und die Umleitung der Authentifizierung an diese App erfolgt ist, wird das currentUser-Objekt angezeigt. 

## <a name="6-run-react-spa-app-with-login-button"></a>6. Ausführen der React-Single-Page-Webanwendung mit der Anmeldeschaltfläche

1. Starten Sie die App im Visual Studio Code-Terminal:

    ```bash
    npm run start
    ```

1. Wählen Sie im Webbrowser die Schaltfläche **Login** (Anmelden) aus. 

    :::image type="content" source="../media/tutorial-login-button/create-react-app-before-authentication-login-button-display.png" alt-text="Auswählen der Schaltfläche „Login“ (Anmelden)":::  

1. Wählen Sie ein Benutzerkonto aus. Hierbei muss es sich nicht um dasselbe Konto handeln, das Sie zum Zugreifen auf das Azure-Portal verwendet haben, aber es sollte ein Konto mit Microsoft-Authentifizierung sein.

    :::image type="content" source="../media/tutorial-login-button/authentication-popup-select-user-account.png" alt-text="Auswählen eines Benutzerkontos: Hierbei muss es sich nicht um dasselbe Konto handeln, das Sie zum Zugreifen auf das Azure-Portal verwendet haben, aber es sollte ein Konto mit Microsoft-Authentifizierung sein.":::

1. Sehen Sie sich das Popupelement mit 1) Benutzername, 2) App-Name und 3) Berechtigungen an, für das Ihre Zustimmung erforderlich ist, und wählen Sie dann **Ja** aus.

    :::image type="content" source="../media/tutorial-login-button/authentication-popup-let-this-app-access-your-info.png" alt-text="Überprüfen des Popupelements mit 1) Benutzername, 2) App-Name und 3) Berechtigungen, für das die Zustimmung erforderlich ist, und Auswählen von „Ja“":::

1. Sehen Sie sich die Benutzerkontoinformationen an. 

    :::image type="content" source="../media/tutorial-login-button/create-react-app-after-authentication-login-button-succeeds.png" alt-text="Überprüfen der Benutzerkontoinformationen":::  

1. Wählen Sie in der App die Schaltfläche **Logout** (Abmelden) aus. Die App enthält auch hilfreiche Links zu den Microsoft-Benutzer-Apps, über die die Berechtigungen widerrufen werden können. 

## <a name="7-clean-up-resources"></a>7. Bereinigen von Ressourcen

Nachdem Sie dieses Tutorial abgeschlossen haben, ist es ratsam, die Anwendung im Azure-Portal aus der [Liste mit den App-Registrierungen](https://portal.azure.com/?quickstart=True#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) zu löschen.

Falls Sie die App behalten, aber die Berechtigungen widerrufen möchten, die der App von Ihrem Benutzerkonto gewährten wurden, können Sie einen der folgenden Links verwenden:

* [Widerrufen der AAD-Berechtigung](https://myapps.microsoft.com/)
* [Widerrufen der Consumerberechtigung](https://account.live.com/consent/manage)

## <a name="next-steps"></a>Nächste Schritte

Diese App ermöglicht die Benutzerauthentifizierung für Ihre App und gibt Benutzerinformationen zurück. Für eine einfache Version einer App kann die Authentifizierungsfunktion auf diesem Stand belassen werden. Sie haben aber auch die Möglichkeit, Funktionen für die Benutzerverwaltung der App und die Autorisierung von Benutzern für App-Features hinzuzufügen. 

Die Daten der Benutzerverwaltung können je nach den von Ihnen ausgewählten Funktionen und Tools in einer Azure Active Directory-Instanz oder in Ihrer eigenen Datenbank gespeichert werden. 

Die Benutzerautorisierung kann über Azure bereitgestellt werden, oder Sie können die Autorisierung ohne Nutzung von Azure entwickeln. Eine weitere Option ist die Kombination dieser beiden Vorgehensweisen, um eine benutzerdefinierte Umgebung für die Autorisierung, Rollen und App-Features zu erhalten. 

* Weitere Nutzung der [MSAL-Bibliothek](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) zum Abrufen des Benutzerprofils und Bereitstellen der automatischen Anmeldung
* Hinzufügen von [Microsoft Graph](https://docs.microsoft.com/graph/overview) für den Zugriff auf Microsoft 365, einschließlich E-Mails und Kalenderterminen