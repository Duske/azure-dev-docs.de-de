---
ms.openlocfilehash: 8f757c030849cb89eea36d74b55867dcc2ff98a6
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441355"
---
Sie können die Ressourcengruppe löschen, indem Sie entweder das [Azure-Portal](https://portal.azure.com) oder die Azure CLI verwenden:

- Wählen Sie im Azure-Portal im Navigationsbereich auf der linken Seite die Option **Ressourcengruppen** und die im Rahmen dieses Tutorials erstellte Ressourcengruppe aus, und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

- Führen Sie den folgenden Azure CLI-Befehl aus (lokal oder per [Cloud Shell](/azure/cloud-shell/overview)), indem Sie `<resource_group>` durch den Namen der in diesem Tutorial verwendeten Gruppe ersetzen:

    ```azurecli
    az group delete --name <resource_group>
    ```
