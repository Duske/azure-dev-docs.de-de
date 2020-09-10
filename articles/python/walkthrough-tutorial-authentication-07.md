---
title: 'Exemplarische Vorgehensweise, Teil 7: Authentifizieren von Python-Apps bei Azure-Diensten'
description: Eine Beschreibung des Codes für den API-Endpunkt der Haupt-App, der den Drittanbieter-API-Endpunkt verwendet und eine Meldung in Azure Queue Storage schreibt.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e026eca0216147c6614582e0cd070cee81daf99c
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379523"
---
# <a name="part-7-main-application-api-endpoint"></a>Teil 7: API-Endpunkt der Hauptanwendung

[Vorheriger Teil: Startcode der Haupt-App](walkthrough-tutorial-authentication-06.md)

Die API */api/v1/getcode* der App generiert eine JSON-Antwort, die einen alphanumerischen Code und einen Zeitstempel enthält.

Zuerst teilt der Decorator `@app.route` Flask mit, dass die `get_code`-Funktion Anforderungen an die URL */api/v1/getcode* verarbeitet.

```python
@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
```

Als Nächstes rufen wir die Drittanbieter-API auf, deren URL sich in `number_url` befindet, und geben den Zugriffsschlüssel an, den wir aus dem Schlüsseltresor im Header abrufen.

```python
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code
```

Die `x-functions-key`-Eigenschaft im Header gibt speziell an, wie Azure Functions (wo in diese Drittanbieter-Beispiel-API bereitstellt wird) erwartet, dass ein Zugriffsschlüssel in einem Header angezeigt wird. Weitere Informationen finden Sie unter [HTTP-Trigger in Azure Functions – Funktionszugriffsschlüssel](/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys). Wenn der Aufruf der API aus irgendeinem Grund fehlschlägt, werden eine Fehlermeldung und der Statuscode zurückgegeben.

Angenommen, der API-Befehl wird erfolgreich ausgeführt gibt einen numerischen Wert zurück, dann erstellen wir mit dieser Zahl einen komplexeren Code und einige zufällige Zeichen (mit unserer eigenen `random_char`-Funktion).

```python
    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }
```

Die `code`-Variable hier enthält die vollständige JSON-Antwort für die API der App, die sowohl den Codewert als auch einen Zeitstempel enthält. Ein Beispiel für eine Antwort ist `{"code":"ojE-161-pTv","timestamp":"2020-04-15 16:54:48.816549"}`.

Vor dem Zurückgeben dieser Antwort schreiben wir jedoch mithilfe der [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-)-Methode des Warteschlangenclients eine Meldung in die Speicherwarteschlange:

```python
    queue_client.send_message(code)

    return jsonify(code)
```

## <a name="processing-queue-messages"></a>Verarbeiten von Warteschlangenmeldungen

In der Warteschlange gespeicherte Meldungen können über das [Azure-Portal](/azure/storage/queues/storage-quickstart-queues-portal#view-message-properties) oder mit dem Azure CLI-Befehl [`az storage message get`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-get)angezeigt und verwaltet werden. Das Beispielrepository enthält ein Skript (*test.cmd* und *test.sh*), um einen Code vom App-Endpunkt anzufordern und dann die Meldungswarteschlange zu überprüfen. Es gibt auch ein Skript, um die Warteschlange mit dem Befehl [`az storage message clear`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-clear) zu löschen.

Üblicherweise hat eine Anwendung wie dieses Beispiel einen anderen Prozess, um asynchrone Meldungen zur weiteren Verarbeitung aus der Warteschlange abzurufen. Wie bereits erwähnt, kann die von diesem API-Endpunkt generierte Antwort an anderer Stelle in der App mit zweistufiger Benutzerauthentifizierung verwendet werden. In diesem Fall muss die App den Code nach einem bestimmten Zeitraum, z. B. 10 Minuten, ungültig machen. Eine einfache Möglichkeit, diese Aufgabe auszuführen, ist das Pflegen einer Tabelle mit gültigen Codes für die zweistufige Authentifizierung, die von der Benutzeranmeldungsprozedur verwendet werden. Die App hätte dann einen einfachen Warteschlangen-Überwachungsprozess mit der folgenden Logik (in Pseudocode):

<pre>
pull a message from the queue and retrieve the code.

if (code is already in the table):
    remove the code from the table, thereby invalidating it
else:
    add the code to the table, making it valid
    call queue_client.send_message(code, visibility_timeout=600)
</pre>

In diesem Pseudocode wird der optionale `visibility_timeout`-Parameter der [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-)-Methode verwendet, der die Anzahl der Sekunden angibt, bevor die Meldung in der Warteschlange sichtbar wird. Da das Standardzeitlimit NULL ist, werden Meldungen, die anfänglich vom API-Endpunkt geschrieben werden, sofort für den Warteschlangen-Überwachungsprozess sichtbar. Folglich speichert dieser Prozess sie sofort in der Tabelle mit gültigen Codes. Wenn die gleiche Meldung erneut mit dem Timeout in die Warteschlange eingereiht wird, weiß der Prozess, dass der Code 10 Minuten später erneut empfangen wird. An diesem Punkt wird er aus der Tabelle entfernt.

## <a name="implementing-the-main-app-api-endpoint-in-azure-functions"></a>Implementieren des API-Endpunkts der Haupt-App in Azure Functions

Der zuvor in diesem Artikel gezeigte Code verwendet das Flask-Webframework zum Erstellen des API-Endpunkts. Da Flask mit einem Webserver ausgeführt werden muss, muss dieser Code in Azure App Service oder einem virtuellen Computer bereitgestellt werden.

Eine alternative Bereitstellungsoption ist die serverlose Umgebung von Azure Functions. In diesem Fall sind der gesamte Startcode und der API-Endpunktcode in derselben Funktion enthalten, die an einen HTTP-Trigger gebunden ist. Wie bei App Service verwenden Sie [Funktionsanwendungseinstellungen](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings), um Umgebungsvariablen für den Code zu erstellen.

Ein Teil der Implementierung, der einfacher wird, ist die Authentifizierung bei Queue Storage. Anstatt ein `QueueClient`-Objekt mit der URL der Warteschlange und einem Anmeldeinformationsobjekt abzurufen, erstellen Sie eine *Queue Storage-Bindung* für die Funktion. Die Bindung verarbeitet die gesamte Authentifizierung im Hintergrund. Mit solch einer Bindung erhält Ihre Funktion ein einsatzbereites Clientobjekt als Parameter. Weitere Informationen und Beispielcode finden Sie unter [Verbinden von Azure Functions mit Azure Queue Storage](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?tabs=bash%2Cbrowser&pivots=programming-language-python).

## <a name="next-steps"></a>Nächste Schritte

Anhand dieses Beispiels haben Sie gelernt, wie sich Apps bei anderen Azure-Diensten authentifizieren, und wie Apps mithilfe von Azure Key Vault alle anderen notwendigen Geheimnisse für APIs von Drittanbietern speichern können.

Das gleiche Muster, das hier mit Azure Key Vault und Azure Storage dargestellt wird, gilt auch für alle anderen Azure-Dienste. Der entscheidende Schritt besteht darin, dass Sie die richtigen Rollenberechtigungen für die App auf der Seite des jeweiligen Diensts im Azure-Portal oder über die Azure CLI festlegen. (Siehe [Zuweisen von Rollenberechtigungen](how-to-assign-role-permissions.md).) Lesen Sie unbedingt die Dienstdokumentation, um zu erfahren, ob Sie weitere Zugriffsrichtlinien konfigurieren müssen.

Denken Sie immer daran, dass Sie allen Dienstprinzipalen, die für die lokale Entwicklung verwendet werden, dieselben Rollen und Zugriffsrichtlinien zuweisen müssen.

Kurz gesagt, wenn Sie diese exemplarische Vorgehensweise abgeschlossen haben, können Sie Ihr Wissen auf eine beliebige Anzahl von anderen Azure-Diensten und externen Diensten anwenden.

Ein Thema, auf das wir hier nicht eingegangen sind, ist die Authentifizierung von *Benutzern*. Um sich mit diesem Bereich für Web-Apps vertraut zu machen, beginnen Sie mit [Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](/azure/app-service/tutorial-auth-aad?pivots=platform-linux).

## <a name="see-also"></a>Siehe auch

- [Authentifizieren und Autorisieren von Python-Apps in Azure](azure-sdk-authenticate.md)
- Beispiel der exemplarischen Vorgehensweise: [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication)
- [Dokumentation zu Azure Active Directory](/azure/active-directory)
- [Dokumentation zu Azure Key Vault](/azure/key-vault)
