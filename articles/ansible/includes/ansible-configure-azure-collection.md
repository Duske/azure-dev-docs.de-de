---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/09/2020
ms.author: tarcher
ms.openlocfilehash: 4f1fbe75f974aadc9e92e518e0e84d49ee73ed3d
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "81755223"
---
- **Azure-Sammlung konfigurieren**: Führen Sie den folgenden Befehl in einem Terminalfenster aus, um die Azure-Sammlung zu installieren. Wenn die Azure-Sammlung bereits installiert ist, wird sie mit dem `--force`-Flag auf die aktuellste Version aktualisiert.

    ```bash
    ansible-galaxy collection install azure.azcollection --force
    ```
