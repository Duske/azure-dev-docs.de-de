---
ms.openlocfilehash: ce5cdbebca22aa198a3e8b6cf883a36dfd4e4a57
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686061"
---
Nachdem Sie die Azure-Erweiterung installiert haben, melden Sie sich bei Ihrem Azure-Konto an, indem Sie zum **Azure**-Explorer navigieren, **Bei Azure anmelden** auswählen und dann den Anweisungen folgen. (Wenn Sie mehrere Azure-Erweiterungen installiert haben, wählen Sie die Erweiterung für den Bereich aus, in dem Sie arbeiten, z. B. App Service, Functions usw.)

![Anmelden bei Azure über VS Code](../media/deploy-azure/azure-sign-in.png)

Vergewissern Sie sich nach der Anmeldung, dass die E-Mail-Adresse Ihres Azure-Kontos (oder „Angemeldet“) in der Statusleiste angezeigt wird und Sie Ihre Abonnements im **Azure**-Explorer sehen:

![VS Code-Statusleiste mit Azure-Konto](../media/deploy-azure/azure-account-status-bar.png)

![Azure-Explorer in VS Code mit Abonnements](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Wenn der Fehler **Abonnement mit dem Namen [Abonnement-ID] nicht gefunden** auftritt, kann dies daran liegen, dass Sie sich hinter einem Proxy befinden und die Azure-API nicht erreichen können. Konfigurieren Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` in Ihrem Terminal mit Ihren Proxyinformationen:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```powershell
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
