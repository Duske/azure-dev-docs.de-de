---
ms.openlocfilehash: cbcc292bf0b59adf35789bbd24ca37b0301a2ad7
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84269053"
---
Nachdem Sie die Azure-Erweiterung installiert haben, melden Sie sich bei Ihrem Azure-Konto an, indem Sie zum **Azure**-Explorer navigieren, **Bei Azure anmelden** auswählen und dann den Anweisungen folgen. (Wenn Sie mehrere Azure-Erweiterungen installiert haben, wählen Sie die Erweiterung für den Bereich aus, in dem Sie arbeiten, z. B. App Service, Functions usw.)

![Anmelden bei Azure über VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

Vergewissern Sie sich nach der Anmeldung, dass die E-Mail-Adresse Ihres Azure-Kontos (oder „Angemeldet“) in der Statusleiste angezeigt wird und Sie Ihre Abonnements im **Azure**-Explorer sehen:

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
