---
author: mikeparker104
ms.author: miparker
ms.date: 07/27/2020
ms.service: mobile-services
ms.topic: include
ms.openlocfilehash: a0b7faea4c122012ddb296af339ff95ef6f41353
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87401470"
---
#### <a name="no-response-from-the-backend-service"></a>Keine Antwort vom Back-End-Dienst

Vergewissern Sie sich beim lokalen Testen, dass der Back-End-Dienst ausgeführt wird und den richtigen Port verwendet.

Wenn Sie den Text gegen die **Azure-API-App** ausführen, überprüfen Sie, ob der Dienst läuft und ohne Fehler bereitgestellt und gestartet wurde.

Vergewissern Sie sich, dass Sie die Basisadresse in **[Postman](https://www.postman.com/downloads)** oder in der Konfiguration der mobilen Anwendung richtig angegeben haben, wenn Sie über den Client testen. Die Basisadresse sollte bei lokalen Tests in etwa so aussehen: `https://<api_name>.azurewebsites.net/` oder `https://localhost:5001/`.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Kein Empfang von Benachrichtigungen unter Android nach dem Starten oder Beenden einer Debugsitzung

Stellen Sie sicher, dass Sie sich nach dem Starten oder Beenden einer Debugsitzung erneut registrieren. Der Debugger bewirkt, dass ein neues **Firebase**-Token generiert wird. Die Benachrichtigungshubinstallation muss ebenfalls aktualisiert werden.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Empfangen eines 401-Statuscodes vom Back-End-Dienst

Vergewissern Sie sich, dass Sie den **apikey**-Anforderungsheader festlegen und dieser Wert mit dem übereinstimmt, den Sie für den Back-End-Dienst konfiguriert haben.

Wenn dieser Fehler beim lokalen Testen auftritt, stellen Sie sicher, dass der Schlüsselwert, den Sie in der Clientkonfiguration definiert haben, mit dem von der [API](#create-the-api-app) verwendeten Benutzereinstellungswert **Authentication:ApiKey** übereinstimmt.

Wenn Sie mit einer **API-App** testen, stellen Sie sicher, dass der Schlüsselwert in der Clientkonfigurationsdatei mit der **Authentication:ApiKey**-Anwendungseinstellung übereinstimmt, die Sie in der [API-App](#create-the-api-app) verwenden.

> [!NOTE]
> Wenn Sie diese Einstellung erstellt oder geändert haben, nachdem Sie den Back-End-Dienst bereitgestellt haben, müssen Sie den Dienst neu starten, damit sie wirksam wird.

Wenn Sie entschieden haben, die Schritte im Abschnitt [Authentifizieren von Clients mit einem API-Schlüssel](#authenticate-clients-using-an-api-key-optional) durchzuführen, stellen Sie sicher, dass Sie das **Authorize**-Attribut nicht auf die **NotificationsController**-Klasse angewendet haben.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Empfangen eines 404-Statuscodes vom Back-End-Dienst

Überprüfen Sie, ob der Endpunkt und die HTTP-Anforderungsmethode korrekt sind. Die Endpunkte sollten z. B. wie folgt lauten:

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

Oder beim lokalen Testen:

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

Wenn Sie die Basisadresse in der Client-App angeben, stellen Sie sicher, dass sie mit einem `/` endet. Die Basisadresse sollte bei lokalen Tests in etwa so aussehen: `https://<api_name>.azurewebsites.net/` oder `https://localhost:5001/`.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Registrierung kann nicht ausgeführt werden, und eine Benachrichtigungshub-Fehlermeldung wird angezeigt

Stellen Sie sicher, dass das Testgerät über Netzwerkkonnektivität verfügt. Legen Sie dann den Statuscode der HTTP-Antwort fest, indem Sie einen Breakpoint festlegen, um den Wert der Eigenschaft **StatusCode** im **HttpResponse**-Objekt zu überprüfen.

Prüfen Sie gegebenenfalls die früheren Vorschläge zur Problembehandlung basierend auf dem Statuscode.

Legen Sie einen Breakpoint für die Zeilen fest, die diese spezifischen Statuscodes für die jeweilige API zurückgeben. Versuchen Sie dann, beim lokalen Debuggen den Back-End-Dienst aufzurufen.

Überprüfen Sie über **[Postman](https://www.postman.com/downloads)** unter Verwendung der entsprechenden Nutzlast, ob der Back-End-Dienst wie erwartet funktioniert. Verwenden Sie die tatsächliche Nutzlast, die vom Clientcode für die betreffende Plattform erstellt wurde.

Überprüfen Sie die plattformspezifischen Konfigurationsabschnitte, um sicherzustellen, dass keine Schritte ausgelassen wurden. Vergewissern Sie sich, dass für die Variablen `installation id` und `token` geeignete Werte für die entsprechende Plattform aufgelöst werden.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Fehlermeldung „Eine ID für das Gerät kann nicht aufgelöst werden“

Überprüfen Sie die plattformspezifischen Konfigurationsabschnitte, um sicherzustellen, dass keine Schritte ausgelassen wurden.
