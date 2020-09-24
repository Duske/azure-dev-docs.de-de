---
ms.openlocfilehash: 4215099ae39963448b7a94d389ded0c9096b1c67
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772677"
---
Sie können die Ressourcengruppe löschen, indem Sie entweder das [Azure-Portal](https://portal.azure.com) oder die Azure CLI verwenden:

- Wählen Sie im Azure-Portal im Navigationsbereich auf der linken Seite die Option **Ressourcengruppen** und die im Rahmen dieses Tutorials erstellte Ressourcengruppe aus, und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

- Führen Sie den folgenden Azure CLI-Befehl aus (lokal oder per [Cloud Shell](/azure/cloud-shell/overview)), indem Sie `<resource_group>` durch den Namen der in diesem Tutorial verwendeten Gruppe ersetzen:

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
