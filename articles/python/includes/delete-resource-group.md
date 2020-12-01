---
ms.openlocfilehash: c45bda8b08ec963febbc6497136cda71086423a3
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035488"
---
Sie können die Ressourcengruppe löschen, indem Sie entweder das [Azure-Portal](https://portal.azure.com) oder die Azure CLI verwenden:

- Wählen Sie im [Azure-Portal](https://portal.azure.com) im Navigationsbereich auf der linken Seite die Option **Ressourcengruppen** und die im Rahmen dieses Tutorials erstellte Ressourcengruppe aus, und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

- Führen Sie den folgenden Azure CLI-Befehl aus (lokal oder per [Cloud Shell](/azure/cloud-shell/overview)), indem Sie `<resource_group>` durch den Namen der in diesem Tutorial verwendeten Gruppe ersetzen:

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
