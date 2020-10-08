---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 718ac8c480c5d366e985f9488d81ab626a82b586
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401548"
---
## <a name="reverse-a-terraform-execution-plan"></a>Umkehren eines Terraform-Ausführungsplans

1. Wenn Sie den Ausführungsplan umkehren (also rückgängig machen) möchten, führen Sie [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus, und geben Sie dabei das Flag `destroy` an:

    ```cmd
    terraform plan -destroy -out <terraform_plan>.destroy.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. Führen Sie zum Anwenden des Ausführungsplans den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus.

    ```cmd
    terraform apply <terraform_plan>.destroy.tfplan
    ```
