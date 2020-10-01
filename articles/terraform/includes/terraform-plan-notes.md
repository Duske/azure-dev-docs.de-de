---
title: include file
description: include file
author: tomarchermsft
ms.service: terraform
ms.topic: include
ms.date: 09/27/2020
ms.author: tarcher
ms.openlocfilehash: 9f3d916bc37cc9d5f86c1f6b2738f419414e5816
ms.sourcegitcommit: e20f6c150bfb0f76cd99c269fcef1dc5ee1ab647
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2020
ms.locfileid: "91401547"
---
  **Hinweise:**

  - Durch den Befehl `terraform plan` wird ein Ausführungsplan erstellt, aber nicht ausgeführt. Stattdessen werden die Aktionen ermittelt, die erforderlich sind, um die in Ihren Konfigurationsdateien angegebene Konfiguration zu erstellen. Mit diesem Muster können Sie überprüfen, ob der Ausführungsplan Ihren Erwartungen entspricht, bevor Sie Änderungen an den eigentlichen Ressourcen vornehmen.
  - Der optionale Parameter `-out` ermöglicht die Angabe einer Ausgabedatei für den Plan. Durch die Verwendung des Parameters `-out` wird sichergestellt, dass genau der von Ihnen überprüfte Plan angewendet wird.
  - Weitere Informationen zum Speichern von Ausführungsplänen und zur Sicherheit finden Sie im [Abschnitt mit der Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).