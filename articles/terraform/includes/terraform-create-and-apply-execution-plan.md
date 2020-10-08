---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 23a5bbc2e6a7e364b3c6eab38bfd5e98b97348a6
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401550"
---
## <a name="create-and-apply-a-terraform-execution-plan"></a>Erstellen und Anwenden eines Terraform-Ausführungsplans

In diesem Abschnitt erfahren Sie, wie Sie einen *Ausführungsplan* erstellen und auf Ihre Cloudinfrastruktur anwenden.

1. Führen Sie zum Initialisieren der Terraform-Bereitstellung [terraform init](https://www.terraform.io/docs/commands/init.html) aus. Mit diesem Befehl werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

    ```cmd
    terraform init
    ```

1. Führen Sie nach der Initialisierung [terraform plan](https://www.terraform.io/docs/commands/plan.html) aus, um einen Ausführungsplan zu erstellen.

    ```cmd
    terraform plan -out <terraform_plan>.tfplan
    ```

    [!INCLUDE [terraform-plan-notes.md](terraform-plan-notes.md)]

1. Wenn Sie so weit sind, können Sie den Ausführungsplan durch Ausführen von [terraform apply](https://www.terraform.io/docs/commands/apply.html) auf Ihre Cloudinfrastruktur anwenden.

    ```cmd
    terraform apply <terraform_plan>.tfplan
    ```
