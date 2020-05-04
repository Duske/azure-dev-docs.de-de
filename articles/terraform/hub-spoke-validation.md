---
title: 'Tutorial: Überprüfen eines Hub-and-Spoke-Netzwerks in Azure mit Terraform'
description: Es wird beschrieben, wie Sie eine Hub-Spoke-Netzwerktopologie überprüfen, bei der alle virtuellen Netzwerke miteinander verbunden sind.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e061fc882962b33261fe68a6f9d4c2d78172a871
ms.sourcegitcommit: 9ff9b51ab21c93bfd61e480c6ff8e39c9d4bf02e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82170896"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Tutorial: Überprüfen eines Hub-and-Spoke-Netzwerks in Azure mit Terraform

In diesem Artikel führen Sie die Terraform-Dateien aus, die im vorherigen Artikel dieser Reihe erstellt wurden. Das Ergebnis ist eine Überprüfung der Verbindungen zwischen den virtuellen Demonetzwerken.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Verwenden von HCL (HashiCorp-Sprache), um das Hub-VNET in einer Hub-Spoke-Topologie zu implementieren
> * Verwenden von „terraform plan“, um die bereitzustellenden Ressourcen zu überprüfen
> * Verwenden von „terraform apply“, um die Ressourcen in Azure zu erstellen
> * Überprüfen der Verbindung zwischen den verschiedenen Netzwerken
> * Verwenden von Terraform, um alle Ressourcen zu zerstören

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen einer hybriden Hub-Spoke-Netzwerktopologie mit Terraform in Azure](./hub-spoke-introduction.md)
1. [Erstellen eines lokalen virtuellen Netzwerks mit Terraform in Azure](./hub-spoke-on-prem.md)
1. [Erstellen eines virtuellen Hub-Netzwerks mit Terraform in Azure](./hub-spoke-hub-network.md)
1. [Erstellen einer virtuellen Hub-Netzwerkappliance mit Terraform in Azure](./hub-spoke-hub-nva.md)
1. [Erstellen eines virtuellen Spoke-Netzwerks mit Terraform in Azure](./hub-spoke-spoke-network.md)

## <a name="verify-your-configuration"></a>Überprüfen Ihrer Konfiguration

Überprüfen Sie nach dem Erfüllen der [Voraussetzungen](#prerequisites), ob die entsprechenden Konfigurationsdateien vorhanden sind.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/common/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd hub-spoke
    ```

1. Führen Sie den Befehl `ls` aus, um zu überprüfen, ob die in den vorherigen Tutorials erstellten Konfigurationsdateien vom Typ `.tf` aufgeführt sind:

    ![Terraform-Demokonfigurationsdateien](./media/hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Bereitstellen der Ressourcen

1. Initialisieren Sie den Terraform-Anbieter:
    
    ```bash
    terraform init
    ```
    
    ![Beispielergebnisse des Befehls „terraform init“](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Führen Sie den Befehl `terraform plan` aus, um die Auswirkungen der Bereitstellung vor der Ausführung anzuzeigen:

    ```bash
    terraform plan
    ```
    
    ![Beispielergebnisse des Befehls „terraform plan“](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Führen Sie die Bereitstellung der Lösung durch:

    ```bash
    terraform apply
    ```
    
    Geben Sie bei der entsprechenden Aufforderung `yes` ein, um die Bereitstellung zu bestätigen.

    ![Beispielergebnisse des Befehls „terraform apply“](./media/hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testen des Hub-VNET und der Spoke-VNETs

In diesem Abschnitt wird beschrieben, wie Sie die Konnektivität zwischen der simulierten lokalen Umgebung und dem Hub-VNET testen.

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe **onprem-vnet-rg**.

1. Wählen Sie auf der Registerkarte **onprem-vnet-rg** den virtuellen Computer **onprem-vm** aus.

1. Wählen Sie **Verbinden**.

1. Kopieren Sie den **SSH**-Befehl neben dem Text **Mit lokalem VM-Konto anmelden** in die Zwischenablage.

1. Führen Sie in einer Linux-Eingabeaufforderung `ssh` zum Herstellen der Verbindung mit der simulierten lokalen Umgebung aus. Verwenden Sie das in der Parameterdatei `on-prem.tf` angegebene Kennwort.

1. Führen Sie den Befehl `ping` aus, um die Konnektivität mit dem virtuellen Jumpbox-Computer im Hub-VNET zu testen:

   ```bash
   ping 10.0.0.68
   ```

1. Führen Sie den Befehl `ping` aus, um die Konnektivität mit den virtuellen Jumpbox-Computern in den einzelnen Spokes zu testen:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Geben Sie zum Beenden der SSH-Sitzung auf dem virtuellen Computer **onprem-vm** den Befehl `exit` ein, und drücken Sie die &lt;EINGABETASTE>.

## <a name="troubleshoot-vpn-issues"></a>Beheben von VPN-Problemen

Informationen zum Beheben von VPN-Fehlern finden Sie im Artikel [Behandeln von Problemen mit einer VPN-Hybridverbindung](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in der Tutorialreihe erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

1. Entfernen Sie die im Plan deklarierten Ressourcen:

    ```bash
    terraform destroy
    ```

    Geben Sie bei der entsprechenden Aufforderung `yes` ein, um das Entfernen der Ressourcen zu bestätigen.

1. Wechseln Sie in das übergeordnete Verzeichnis:

    ```bash
    cd ..
    ```

1. Löschen Sie das Verzeichnis `hub-scope` (mit allen darin befindlichen Dateien):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Weitere Informationen zur Verwendung von Terraform in Azure](/azure/terraform)