---
ms.custom: devx-track-js
ms.topic: include
ms.date: 010/16/2020
ms.openlocfilehash: fb1c04fe82b67076ca38ef353daf7260544a5881
ms.sourcegitcommit: 8a2a7df568c69fff2080ffab248409040efda1ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179315"
---
Wenn Sie die Azure-Diensterweiterungen bereits verwenden, sollten Sie bereits angemeldet sein, und Sie können diesen Schritt überspringen. Wenn Sie die Azure-Diensterweiterungen nicht verwenden, fahren Sie mit diesem Abschnitt fort, um sich bei Azure anzumelden.

Nachdem Sie die Azure-Diensterweiterung in Visual Studio Code installiert haben, müssen Sie sich bei Ihrem Azure-Konto anmelden, indem Sie zum **Azure**-Explorer navigieren, **Bei Azure anmelden** auswählen und dann den Anweisungen folgen. (Wenn Sie mehrere Azure-Erweiterungen installiert haben, wählen Sie die Erweiterung für den Bereich aus, in dem Sie arbeiten, z. B. App Service, Functions usw.)

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
