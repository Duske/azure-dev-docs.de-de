---
ms.openlocfilehash: e58e36b140c1512600497bffbbd149334904981f
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/05/2020
ms.locfileid: "78894306"
---
Nachdem Sie die Azure-Erweiterung in VS Code installiert haben, melden Sie sich bei Ihrem Azure-Konto an, indem Sie zum **Azure**-Explorer navigieren, **Bei Azure anmelden** auswählen und dann den Anweisungen folgen. (Wenn Sie mehrere Azure-Erweiterungen installiert haben, wählen Sie die Erweiterung für den Bereich aus, in dem Sie arbeiten, z. B. App Service, Functions usw.)

![Anmelden bei Azure über VS Code](../media/deploy-azure/azure-sign-in.png)

Vergewissern Sie sich nach der Anmeldung, dass die E-Mail-Adresse Ihres Azure-Kontos (oder „Angemeldet“) in der Statusleiste angezeigt wird und Sie Ihre Abonnements im **Azure**-Explorer sehen:

![VS Code-Statusleiste mit Azure-Konto](../media/deploy-azure/azure-account-status-bar.png)

![Azure-Explorer in VS Code mit Abonnements](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Wenn der Fehler **Abonnement mit dem Namen [Abonnement-ID] nicht gefunden** auftritt, kann dies daran liegen, dass Sie sich hinter einem Proxy befinden und die Azure-API nicht erreichen können. Konfigurieren Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` in Ihrem Terminal mit Ihren Proxyinformationen:
>
> # <a name="bash"></a>[Bash](#tab/bash)
>
> ```bash
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> # <a name="powershell"></a>[PowerShell](#tab/powershell)
>
> ```powershell
> $env: HTTPS_PROXY = "https://username:password@proxy:8080"
> $env: HTTP_PROXY = "http://username:password@proxy:8080"
> ```
>
> # <a name="cmd"></a>[Cmd](#tab/cmd)
>
> ```cmd
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ---
