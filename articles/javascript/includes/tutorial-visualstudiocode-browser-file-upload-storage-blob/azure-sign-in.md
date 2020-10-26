---
title: include file azure-sign-in.md
description: include file azure-sign-in.md
ms.date: 10/13/2020
ms.topic: include
ms.custom: devx-track-javascript
ms.openlocfilehash: 085958cd12352273d5433279bae5f3edac934714
ms.sourcegitcommit: ced8331ba36b28e6e2eacd23a64b39ddc7ffe6ab
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344151"
---
Nachdem Sie die [Azure Storage-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) in Visual Studio Code installiert haben, melden Sie sich bei Ihrem Azure-Konto an, indem Sie zum **Azure**-Explorer navigieren, **Bei Azure anmelden** auswählen und dann den Anweisungen folgen. 

![Anmelden bei Azure über VS Code](../../media/tutorial-browser-file-upload/azure-sign-in.png)

Vergewissern Sie sich nach der Anmeldung, dass die E-Mail-Adresse Ihres Azure-Kontos (oder „Angemeldet“) in der Statusleiste angezeigt wird und Sie Ihre Abonnements im **Azure**-Explorer sehen:

![VS Code-Statusleiste mit Azure-Konto](../../media/tutorial-browser-file-upload/azure-account-status-bar.png)

![Azure-Explorer in VS Code mit Abonnements](../../media/tutorial-browser-file-upload/azure-subscription-view.png)

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
