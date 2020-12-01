---
ms.openlocfilehash: c78b25e2111e48ed9337bcd85f6585c4bdd4a081
ms.sourcegitcommit: 418e446e6ada5d50df283401df4f6b6370a356b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035513"
---
Melden Sie sich nach der Installation der Azure-Erweiterung bei Ihrem Azure-Konto an:

1. Navigieren Sie zum **Azure**-Explorer.
1. Wählen Sie **Bei Azure anmelden** aus, und befolgen Sie die Aufforderungen. (Wenn Sie mehrere Azure-Erweiterungen installiert haben, wählen Sie die Erweiterung für den Bereich aus, in dem Sie arbeiten, z. B. App Service, Functions usw.)

    ![Anmelden bei Azure über VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

1. Vergewissern Sie sich nach der Anmeldung, dass **Azure: Angemeldet** auf der Statusleiste angezeigt wird und Sie Ihre Abonnements im **Azure-** Explorer sehen:

    ![Visual Studio Code-Statusleiste mit Azure-Konto](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

    ![Azure App Service-Explorer in Visual Studio Code mit Abonnements](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> Wenn der Fehler **Abonnement mit dem Namen [Abonnement-ID] nicht gefunden** auftritt, kann dies daran liegen, dass Sie sich hinter einem Proxy befinden und die Azure-API nicht erreichen können. Konfigurieren Sie die Umgebungsvariablen `HTTP_PROXY` und `HTTPS_PROXY` in Ihrem Terminal mit Ihren Proxyinformationen:
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
