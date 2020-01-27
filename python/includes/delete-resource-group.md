---
ms.openlocfilehash: 2f54e918e7126123ada68b696ea96f4d5f3dbb83
ms.sourcegitcommit: a8073315f751631ab983618fa9f812eb95d8b2dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125237"
---
Sie können die Ressourcengruppe löschen, indem Sie entweder das [Azure-Portal](https://portal.azure.com) oder die Azure CLI verwenden:

- Wählen Sie im Azure-Portal im Navigationsbereich auf der linken Seite die Option **Ressourcengruppen** und die im Rahmen dieses Tutorials erstellte Ressourcengruppe aus, und verwenden Sie anschließend den Befehl **Ressourcengruppe löschen**.

- Führen Sie den folgenden Azure CLI-Befehl aus (lokal oder per [Cloud Shell](/cloud-shell/overview)), indem Sie `<resource_group>` durch den Namen der in diesem Tutorial verwendeten Gruppe ersetzen:

    ```azurecli
    az group delete --name <resource_group>
    ```
