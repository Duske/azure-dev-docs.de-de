---
title: Zuweisen von Rollenberechtigungen zu einer App-Identität oder einem Dienstprinzipal
description: Erteilen von Berechtigungen für einen Dienstprinzipal oder eine App-Identität mithilfe der Azure CLI
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 4eedf982d16f9991bd884d6b575f0f8d8ee97bd2
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256435"
---
# <a name="how-to-assign-role-permissions-to-an-app-identity-or-service-principal"></a>Informationen zum Zuweisen von Rollenberechtigungen zu einer App-Identität oder einem Dienstprinzipal

Das RBAC-System (Role-Based Access Control, rollenbasierte Zugriffssteuerung) von Azure verwaltet bestimmte Berechtigungen für eine Vielzahl von Ressourcen. Eine *Rollen* ist im Wesentlichen eine Sammlung verwandter Berechtigungen, die häufig zusammen benötigt werden. Um Berechtigungen zu aktivieren, weisen Sie einem *Sicherheitsprinzipal* (einem Benutzer, einer Gruppe, einem Dienstprinzipal oder einer App-Identität) eine Rolle mit einem bestimmten *Bereich* zu, für den diese Rolle gilt.

Weisen Sie in der Praxis immer nur die Rollen zu, die ein Sicherheitsprinzipal für den spezifischsten Bereich tatsächlich benötigt. Vermeiden Sie es, umfangreichere Rollen in umfassenderen Bereichen zuzuweisen, auch wenn dies anfänglich bequemer erscheint. Durch das Einschränken von Rollen und Bereichen schränken Sie ein, welche Ressourcen gefährdet sind, wenn der Sicherheitsprinzipal jemals kompromittiert wird (d. h., wenn die Anmeldeinformationen für diesen Prinzipal bei einer Datenverletzung oder einem anderen Sicherheitsvorfall verfügbar gemacht werden).

Da Sie unterschiedliche Sicherheitsprinzipale in der Entwicklungs- und der Produktionsumgebung verwenden, wiederholen Sie die Rollenzuweisungen in jeder Umgebung. Dies bedeutet, dass Sie während der Entwicklung in der Regel dem lokalen Dienstprinzipal, der auf Ihrer Arbeitsstation erstellt wurde, Rollen zuweisen (weitere Informationen finden Sie unter [Konfigurieren Ihrer lokalen Python-Entwicklungsumgebung: Authentifizierung](configure-local-development-environment.md#configure-authentication)). In der Produktionsumgebung weisen Sie der Anwendungsidentität oder dem Dienstprinzipal vor der Bereitstellung Rollen zu, um sicherzustellen, dass die Anwendung beim Start Zugriff besitzt.

Weitere allgemeine Informationen zu RBAC finden Sie unter [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure?](/azure/role-based-access-control/overview)

## <a name="role-assignment-process"></a>Rollenzuweisungsprozess

Das Zuweisen einer Rolle geschieht in drei Schritten:

1. [Ermitteln Sie die entsprechende Rolle](#find-the-roles-you-need) für den Typ der beteiligten Ressource und die Vorgänge, die Sie autorisieren möchten.

1. Identifizieren Sie den erforderlichen Bereich für die betreffende Rolle, der den Umfang der Ressourcen beschreibt, für die Vorgänge autorisiert werden.

1. Weisen Sie die Rolle einem Sicherheitsprinzipal zu.

Schritt 1 ist für das Azure-Portal und die Azure CLI identisch. Die Schritte 2 und 3 unterscheiden sich zwischen dem Portal und der CLI und werden daher in den folgenden Abschnitten kombiniert: [Identifizieren des Bereichs und Zuweisen einer Rolle im Azure-Portal](#azure-portal) und [Identifizieren des Bereichs und Zuweisen einer Rolle über die Azure CLI](#azure-cli).

> [!NOTE]
> Wenn Ihr Benutzerkonto nicht über Berechtigungen zum Zuweisen einer Rolle in Ihrem Abonnement verfügt, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass Ihr Konto keine Berechtigung zum Ausführen der Aktion „Microsoft.Authorization/roleAssignments/write“ besitzt. Wenden Sie sich in diesem Fall an die Administratoren Ihres Abonnements, da sie die Berechtigungen in Ihrem Namen zuweisen können.

## <a name="find-the-roles-you-need"></a>Ermitteln der erforderlichen Rollen

1. Beginnen Sie mit dem umfassenden Artikel [Integrierte Azure-Rollen](/azure/role-based-access-control/built-in-roles). Die Tabelle am Anfang des Artikels stellt einen Index für die Details weiter unten in diesem Artikel dar.

1. Navigieren Sie in diesem Artikel zur Dienstkategorie (Compute, Speicher, Datenbanken usw.) für die Ressource, der Sie Berechtigungen erteilen möchten. Die einfachste Möglichkeit, das Gesuchte zu finden, ist die Suche nach einem relevanten Schlüsselwort, etwa „Blob“, „virtueller Computer“ usw.

1. Überprüfen Sie die Rollen, die für die Dienstkategorie aufgeführt werden, und identifizieren Sie die jeweils erforderlichen Vorgänge. Beginnen Sie auch hier immer mit der restriktivsten Rolle.

    Wenn ein Sicherheitsprinzipal z. B. Blobs in einem Azure Storage-Konto lesen muss, aber keinen Schreibzugriff benötigt, wählen Sie „Storage-Blobdatenleser“ anstelle von „Mitwirkender an Storage-Blobdaten“ (und definitiv nicht die Rolle „Besitzer von Speicherblobdaten“ auf Administratorebene) aus. Sie können die Rollenzuweisungen später jederzeit nach Bedarf aktualisieren.

    Wenn der Sicherheitsprinzipal auch Zugriff auf Queue Storage benötigt, können Sie Rollen wie „Storage-Warteschlangendatenleser“ und „Mitwirkender an Storage-Warteschlangendaten“ verwenden. Auch hier sollten Sie so spezifisch wie möglich sein, anstatt eine umfassende Rolle wie etwa „Lese- und Datenzugriff“ zuzuweisen, die Zugriff auf Kontoschlüssel ermöglicht, über die der Prinzipal auf alle Elemente im gesamten Speicherkonto zugreifen kann.

1. Wenn Sie keine geeignete Rolle finden, können Sie [benutzerdefinierte Rollen](/azure/role-based-access-control/custom-roles) erstellen.

## <a name="identify-scope-and-assign-a-role-on-the-azure-portal"></a><a name="azure-portal"></a>Identifizieren des Bereichs und Zuweisen einer Rolle im Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu der Ressource, der Sie eine Rolle zuweisen möchten. Der Bereich der Ressource bestimmt den Bereich der Zuweisung.

    Wenn Sie z. B. zu einem Speicherkonto navigieren, gilt eine Rollenzuweisung für das gesamte Speicherkonto. Wenn Sie zu einem bestimmten Blobcontainer in diesem Speicherkonto navigieren, gilt die Rollenzuweisung nur für diesen Container.

1. Wählen Sie das Blatt **Zugriffssteuerung (IAM)** aus (IAM steht für „Identity & Access Management“).

1. Auf diesem Blatt finden Sie einen Abschnitt **Rollenzuweisungen**, in dem Sie Rollen hinzufügen und entfernen können, die beliebigen Sicherheitsprinzipalen zugewiesen sind.

Ausführliche Informationen und eine exemplarische Vorgehensweise für die Benutzeroberfläche finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen](/azure/role-based-access-control/role-assignments-portal) in der Azure RBAC-Dokumentation.

## <a name="identify-scope-and-assign-a-role-through-the-azure-cli"></a><a name="azure-cli"></a>Identifizieren des Bereichs und Zuweisen einer Rolle über die Azure CLI

Bei der Rollenzuweisung mit der Azure CLI wird der Befehl [`az role assignment`](/cli/azure/role/assignment?view=azure-cli-latest) verwendet. Mit `az role assignment create` können Sie eine Zuweisung hinzufügen und mit `az role assignment delete` eine Zuweisung entfernen. 

Obwohl der vollständige Prozess unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure CLI](/azure/role-based-access-control/role-assignments-cli) beschrieben wird, werden in der folgenden Zusammenfassung bestimmte Beispiele vorgestellt, die für andere Artikel in diesem Developer Center relevant sind.

Der Befehl `az role assignment create` weist die folgende Syntax auf:

```azurecli
az role assignment create --assignee <assignee> --role <role> --scope <scope>
```

- `<assignee>` identifiziert den Sicherheitsprinzipal. Bei Dienstprinzipalen (z. B. bei dem bei der lokalen Entwicklung verwendeten Prinzipal) ist das zugewiesene Objekt die Client-ID dieses Prinzipals. Bei Anwendungen, die in der Cloud bereitgestellt werden, ist das zugewiesene Objekt der Name der Anwendung.
- `<role>` ist der Name der zuzuweisenden Rolle (z. B. „Mitwirkender an Storage-Blobdaten“) oder die zugehörige GUID (z. B. „ba92f5b4-2d11-453d-A403-e96b0029c9fe“).
- `<scope>` ist eine potenziell lange Zeichenfolge, die den genauen Bereich der Zuweisung identifiziert.

Der Bereich besteht aus einer Reihe von Bezeichnern, die durch das Zeichen / getrennt werden. Sie können sich diese Zeichenfolge als Ausdruck für die folgende Hierarchie vorstellen, wobei Text ohne Platzhalter (`<>`) feste Bezeichner sind:

<pre>
/subscriptions
  /&lt;subscription_id&gt;
    /resourcegroups
      /&lt;resource_group_name&gt;
        /providers
          /&lt;provider_name&gt;
            /&lt;resource_type&gt;
              /&lt;resource_sub_type_1&gt;
                /&lt;resource_sub_type_2&gt;
                  /&lt;resource_name&gt;
</pre>

- `<subscription_id>` ist die ID des zu verwendenden Abonnements (eine GUID).
- `<resources_group_name>` ist der Name der enthaltenden Ressourcengruppe.
- `<provider_name>` ist der Name des Diensts, der die Ressource verarbeitet, und `<resource_type>` und `<resource_sub_type_*>` identifizieren dann weitere Ebenen innerhalb dieses Diensts.
  
    Diese Namen und Typen können Sie anhand der Referenz [Integrierte Azure-Rollen](/azure/role-based-access-control/built-in-roles) ermitteln. Suchen Sie die Rolle in der oberen Tabelle, und wählen Sie sie aus, um zur spezifischen Beschreibung der Rolle zu navigieren. Dort finden Sie Zeichenfolgen, die den Anbieternamen, den Ressourcentyp und die Ressourcenuntertypen enthalten. Beispielsweise wird für die Rolle „Mitwirkender an Storage-Blobdaten“ die Angabe „Microsoft.Storage/storageAccounts/blobServices/containers“ angezeigt. Für die „Cosmos DB-Rolle Kontoleser“ wird „Microsoft.DocumentDB/databaseAccounts/readonlykeys“ mit nur einem Untertyp angezeigt.

- `<resource_name>` der letzte Teil der Zeichenfolge, die eine bestimmte Ressource identifiziert.

Der umfangreichste (am wenigsten spezifische) Bereich ist `/subscriptions/<subscription_id>`, der eine Zuweisung über das gesamte Abonnement hinweg anwendet. Mit jeder zusätzlichen Hierarchieebene wird der Bereich spezifischer.

### <a name="examples"></a>Beispiele

In den folgenden Beispielen werden die folgenden Bedingungen angenommen (siehe [Beispiel: Verwenden der Azure-Bibliotheken mit Azure Storage](azure-sdk-example-storage.md)):

- Ihre Azure Abonnement-ID ist in einer Umgebungsvariablen mit dem Namen `AZURE_SUBSCRIPTION_ID` enthalten.
- Die Client-ID des Dienstprinzipals, dem Sie eine Rolle zuweisen möchten, ist in einer Umgebungsvariablen namens `AZURE_CLIENT_ID` enthalten.
- Im Abonnement verfügen Sie über eine Ressourcengruppe mit dem Namen „PythonAzureExample-Storage-rg“.
- Die Ressourcengruppe enthält ein Azure Storage-Konto mit dem Namen „pythonazurestorage-12345“.
- Sie haben einen Blobcontainer mit dem Namen „blob-container-01“ in diesem Speicherkonto.
- Sie möchten dem Dienstprinzipal die Rolle „Mitwirkender an Storage-Blobdaten“ zuweisen.

> [!TIP]
> Es kann eine Minute dauern, bis Änderungen an Rollenzuweisungen innerhalb von Azure weitergegeben werden. Daher können Sie feststellen, dass Code nach dem Entfernen einer Berechtigung für kurze Zeit weiterhin funktioniert. Wenn Sie unerwartetes Verhalten feststellen, warten Sie ein oder zwei Minuten, und versuchen Sie es noch mal.

#### <a name="grant-permissions-for-the-specific-container-only"></a>Erteilen von Berechtigungen nur für den spezifischen Container

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-storage-account"></a>Erteilen von Berechtigungen für alle Blobcontainer im Speicherkonto

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-resource-group"></a>Erteilen von Berechtigungen für alle Blobcontainer in der Ressourcengruppe

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg"
```

Alternativ kann die Ressourcengruppe auch mithilfe des Parameters `--resource-group` angegeben werden:

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --resource-group "PythonAzureExample-Storage-rg"
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg"
```

Alternativ kann die Ressourcengruppe auch mithilfe des Parameters `--resource-group` angegeben werden:

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --resource-group "PythonAzureExample-Storage-rg"
```

---

#### <a name="grant-permissions-to-all-blob-containers-in-the-subscription"></a>Erteilen von Berechtigungen für alle Blobcontainer im Abonnement

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%"
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID"
```

---
